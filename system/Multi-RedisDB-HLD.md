# Multiple Instances of RedisDB in SONiC

# High Level Design Document
#### Rev 0.2



# Table of Contents

  * [List of Tables](#list-of-tables)

  * [Revision](#revision)

  * [About This Manual](#about-this-manual)

  * [Scope](#scope)

  * [Definition/Abbreviation](#definitionabbreviation)

  * [1. Feature Overview](#1-feature-overview)
      * [1.1 Requirements](#11-requirements)
      * [1.2 Design Overview](#12-design-overview)
      
* [2. Functionality](#2-functionality)

     * [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
     * [2.2 Functional Description](#22-functional-description)
          * [2.2.1 Database Configuration File](#221-database-configuration-file)
          * [2.2.2 Database Access API](#222-database-access-api)
          * [2.2.3 Database Access Commands](#223-database-access-commands)
          * [2.2.4 Database Connector Changes Summary](#224-database-connector-changes-summary)

* [3. Design](#3-design)

* [4. User Interface](#4-user-interface)

* [5. Flow Diagrams](#5-flow-diagrams)

* [6. Error Handling](#6-error-handling)

* [7. Serviceability and Debug](#7-serviceability-and-debug)

* [8. Warm Boot Support](#8-warm-boot-support)

* [9. Scalability](#9-scalability)

* [10. Unit Test](#10-unit-test)

* [Appendix](#appendix)

     * [Case Study](#case-study)

     



# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

[Table 2: RedisDB Instances](#table-2-redisdb-instances)

[Table 3: RedisDB to SONiC Database Mapping](#table-3-redisdb-to-sonic-database-mapping)

[Table 4: Database Connector Changes](#table-4-database-connector-changes)

[Table 5: Warm-Boot use cases](#table-5-warm-boot-use-cases)

# Revision

| Rev  |    Date    |              Author              | Change Description |
| :--: | :--------: | :------------------------------: | ------------------ |
| 0.1  | 05/25/2020 | Rajendra Dendukuri/Guru Harakere | Initial version    |
| 0.2  | 05/28/2020 | Rajendra Dendukuri/Guru Harakere | Fixed bullet numbering, updated sections 8,9,10,11    |
| 0.3  | 05/29/2020 | Rajendra Dendukuri/Guru Harakere | Clarified 3rd-party data migration restrictions, removed refs for internal docs, Added a table of DB Migration scenarios   |


# About this Manual

This document provides information about the proposed changes to the Redis Database service used by various applications in SONiC.



# Scope
This document is limited to providing the below mentioned details:

- Proposed changes to the SONiC source code
- Impact of Multi-DB in our test environment
- Proposed organization of Redis DB's in Broadcom SONiC
- A case study on how using multiple instances of Redis DB helps improve system performance
- Unit tests

The finer details about the decisions made to arrive at the proposed code changes are out of scope of this document. The Multi-DB feature is a feature that has been proposed and implemented by the SONiC developer community.  This document augments the original design specification by providing additional details on how this will be rolled out in Broadcom SONiC.



# Definition/Abbreviation

### Table 1: Abbreviations
| **Term** | **Meaning**                              |
| -------- | ---------------------------------------- |
| Multi-DB | Multiple instances of the Redis Database |
| RedisDB  | Redis Database                           |



# 1 Feature Overview

Broadcom SONiC currently uses a single Redis instance to serve all the databases used by various SONiC applications. As a result, every table access made to the RedisDB will appear to be a single threaded operation from the applications requiring database services. Thus, having a single instance can be a bottleneck when various SONiC applications try to modify databases in parallel. Degraded response times have been observed during large table update operations. For example, while pushing a large number of routes down to the switch ASIC by the routing protocols, a perceivable delay is observed.



To allow the databases to be served in parallel, more than one instance of the RedisDB service is used. The choice of number of RedisDB instances and which databases each of them holds can be specified by the user. As part of the switch factory default configuration, an optimal combination is chosen and qualified.



The support for Multi-DB has been continuously added to the community branch since 2H 2019. For the SONiC-Broadcom 3.1 release, the Multi-DB changes made in the community version of SONiC are migrated. After performing comparative performance tests,  an optimal multi-instance schema will be chosen for Broadcom SONiC in order to achieve the desired performance.




## 1.1 Requirements

### 1.1.1 Functional Requirements

- 1.1.1.0 - An optimal Multi-DB configuration suitable for a typical steady state use case must be included as part of the switch factory default configuration.
- 1.1.1.2 - SONiC code integrators must be able to make changes to the Multi-DB configuration by making appropriate changes to the configuration file. This includes the choice of using the classic single instance scheme. It may require a firmware upgrade for the new Multi-DB configuration to take effect.
- 1.1.1.3 - The changes to the Multi-DB configuration file must not require any associated changes to the rest of the switch configuration files.
- 1.1.1.4 - A non-default Multi-DB configuration file must persist across a firmware upgrade to newer versions of Broadcom SONiC.
- 1.1.1.5 - Users should be able to downgrade from Multi-DB capable SONiC version to the legacy versions of SONiC without any feature breakage.
- 1.1.1.6 - Using the factory default Multi-DB configuration must not result in a degraded performance in comparison to the classic single instance configuration.
- 1.1.1.7 - Using the factory default Multi-DB configuration should not result in any loss of functionality. Any exceptions to this requirement should be highlighted clearly in the Broadcom SONiC product guides and release notes.
- 1.1.1.8 - The Multi-DB configuration must be supported by all SONiC platforms including vs and docker-vs.
- 1.1.1.9 - Users can be allowed to make changes to the Multi-DB configuration by making appropriate changes to the configuration file. This includes the choice of using the classic single instance scheme. It may require a switch reboot for the new Multi-DB configuration to take effect.

### 1.1.2 Configuration and Management Requirements
- 1.1.2.0 - A KLISH CLI command should be provided to view the list of RedisDB instances currently under use. It should also display the current mapping .
- 1.1.2.1 - Only users with administrative privileges must be able to make changes to the Multi-DB configuration files.

### 1.1.3 Scalability Requirements
- 1.1.3.1 - Usage of Multi-DB configuration must not result in a degraded performance when used in a scaled up deployments. The performance must be at least on par with the single RedisDB configuration.
- 1.1.3.2 - A user chosen Multi-DB configuration may perform better or worse compared to the switch factory default Multi-DB configuration. The requirement to guarantee a performance benefit for a user chosen Multi-DB configuration will not be supported as part of the Broadcom SONiC product offering.

### 1.1.4 Warm Boot Requirements

- 1.1.4.1 - Users must be allowed to perform warm-reboot, fast-reboot while using a Multi-DB configuration.

- 1.1.4.2 - Users must be allowed to load a new SONiC version which has a different Multi-DB configuration. A warm-reboot of the switch must be allowed to make use of the new intended Multi-DB configuration without any loss of functionality. This requirement is a must have to support In-service software upgrade to a newer version.

### 1.1.5 Requirement Traceability

The following requirements will not be part of the Broadcom SONiC-3.1 release. The rest of the requirements  listed in the section 1.1 will be supported in the Broadcom SONiC-3.1 release.



| Requirement Number | Description                                                  | Supported in 3.1 |
| ------------------ | ------------------------------------------------------------ | ---------------- |
| 1.1.1.9 | Users must be able to make changes to the Multi-DB configuration by making appropriate changes to the configuration file. This includes the choice of using the classic single instance scheme. It may require a switch reboot for the new Multi-DB configuration to take effect. | No |
| 1.1.4.2              | Users must be allowed to load a new SONiC version which has a different Multi-DB configuration. A warm-reboot of the switch must be allowed to make use of the new intended Multi-DB configuration without any loss of functionality. This requirement is a must have to support In-service software upgrade to a newer version. | Best Effort               |



## 1.2 Design Overview
The Multi-DB feature is being designed and delivered as part of the SONiC community. A more detailed design is available in the document [Support Multiple user-defined redis database instances](https://github.com/dzhangalibaba/SONiC/blob/database_doc/doc/database/multi_database_instances.md).



The changes to support Multi-DB are spread across all SONiC applications which require a database service and the common libraries which are used by the applications to access the database services. The following is a summary of the required code changes. A more detailed explanation can be found in the design document  [Support Multiple user-defined redis database instances](https://github.com/dzhangalibaba/SONiC/blob/database_doc/doc/database/multi_database_instances.md).



The database container provides the core functionality of interpreting the Multi-DB configuration file and creating appropriate RedisDB instances and mapping SONiC  databases to the corresponding RedisDB instance. All application containers which use the database service in SONiC are modified to ensure that they use the right database access method to reach the correct instance where the  requesting database resides.



# 2 Functionality
## 2.1 Target Deployment Use Cases

Database heavy operations like pushing large numbers of routes can benefit from performance improvements done to the parallel database access. Improvements done can benefit a scaled up configuration where a large number of VLANs and interfaces are used.



Third-party applications can also leverage this infrastructure by using a separate instance of RedisDB and leave the default RedisDB instances to be used for SONiC applications.

## 2.2 Functional Description

In the current implementation of SONiC there is only one RedisDB instance which stores all the SONiC databases(e.g CONFIG_DB, APPL_DB). It has been observed that when there are large write operations during a short time period (e.g large number of routes are created), the single database instance becomes busy serving one particular application's operations while other applications are penalized with higher wait times. With Multi-DB instances, separate DB threads can process transactions to separate DBs, in parallel. Multi-DB test results showed a performance (time) improvement of 20-30% from the routing applications perspective. The databases used in SONiC can thus be split based on the frequency of their usage and their role in the switch operation.



The following sections provided details on how multiple instances of RedisDB are created and used in SONiC.



### 2.2.1 Database Configuration File

A new configuration file *database_config.json* is defined to be used by the database container to identify the required number of RedisDB instances and their connection information. Each of the databases used by SONiC applications are also mapped to the defined instances. Below is the proposed database configuration file for Broadcom SONiC that shall be used as part of the switch default configuration.  In this document, the *database_config.json* file is also referred to as the Multi-DB configuration file.



```
{
    "INSTANCES": {
        "redis":{
            "hostname" : "127.0.0.1",
            "port" : 6379,
            "unix_socket_path" : "/var/run/redis/redis.sock"
        },
        "redis2":{
            "hostname" : "127.0.0.1",
            "port" : 26379,
            "unix_socket_path" : "/var/run/redis/redis2.sock"
        },
        "redis3":{
            "hostname" : "127.0.0.1",
            "port" : 36379,
            "unix_socket_path" : "/var/run/redis/redis3.sock"
        }
    },
    "DATABASES" : {
        "APPL_DB" : {
            "id" : 0,
            "separator": ":",
            "instance" : "redis2"
        },
        "ASIC_DB" : {
            "id" : 1,
            "separator": ":",
            "instance" : "redis3"
        },
        "COUNTERS_DB" : {
            "id" : 2,
            "separator": ":",
            "instance" : "redis"
        },
        "LOGLEVEL_DB" : {
            "id" : 3,
            "separator": ":",
            "instance" : "redis"
        },
        "CONFIG_DB" : {
            "id" : 4,
            "separator": "|",
            "instance" : "redis"
        },
        "PFC_WD_DB" : {
            "id" : 5,
            "separator": ":",
            "instance" : "redis"
        },
        "FLEX_COUNTER_DB" : {
            "id" : 5,
            "separator": ":",
            "instance" : "redis"
        },
        "STATE_DB" : {
            "id" : 6,
            "separator": "|",
            "instance" : "redis"
        },
        "SNMP_OVERLAY_DB" : {
            "id" : 7,
            "separator": "|",
            "instance" : "redis"
        },
        "ERROR_DB" : {
            "id" : 8,
            "separator": "|",
            "instance" : "redis"
        }
    },
    "VERSION" : "1.0"
}
```



The proposed Multi-DB configuration can be summarized as below:





#### Table 2: RedisDB Instances

| RedisDB Instance | TCP Port | Unix Domain Socket         |
| ---------------- | :------: | -------------------------- |
| redis            |   6379   | /var/run/redis/redis.sock  |
| redis2           |  26379   | /var/run/redis/redis2.sock |
| redis3           |  36379   | /var/run/redis/redis3.sock |



#### Table 3: RedisDB to SONiC Database Mapping

| SONiC DB ID | SONiC Database Name | RedisDB Instance |
| :---------: | ------------------- | :--------------: |
|      0      | APPL_DB             |      redis2      |
|      1      | ASIC_DB             |      redis3      |
|      2      | COUNTERS_DB         |      redis       |
|      3      | LOGLEVEL_DB         |      redis       |
|      4      | CONFIG_DB           |      redis       |
|      5      | PFC_WD_DB           |      redis       |
|      5      | FLEX_COUNTER_DB     |      redis       |
|      6      | STATE_DB            |      redis       |
|      7      | SNMP_OVERLAY_DB     |      redis       |
|      8      | ERROR_DB            |      redis       |

NOTE: The above  layout is an initial proposal only. Other DBs will be considered after some validation.


The database container and service in SONiC parses the *database_config.json* file during system bootup. The switch factory default Multi-DB configuration is available in the path  */etc/default/sonic-db/database_config.json* inside the database container. If a user defined configuration file */etc/sonic/database_config.json* is provided, it is used instead of the default configuration file. The Multi-DB configuration file that is currently active can be found in the path */var/run/redis/sonic-db/* 



A new Multi-DB configuration can only be used if the database container is restarted. This is only possible if the user reboots the switch. The *config reload* operation does not restart the database container.



### 2.2.2 Database Access API

All SONiC applications which require database access use a database connector API. Depending on the application type, the C++/Python/Golang, appropriate connector library is used.



All the connector libraries are updated to provide a database connection based on database name in addition to the existing method of using the database ID.  This API change is required because in the database configuration file, database names are assigned redis instances.



The libswss-common and the swsssdk-py libraries are updated to provide this new connector APIs.



##### 2.2.2.1 New Design of C++ Interface : DBConnector()



**Legacy Single Instance:**

```
DBConnector(int dbId, const std::string &hostname, int port, unsigned int timeout);
DBConnector(int dbId, const std::string &unixPath, unsigned int timeout);
```



**New Multi-DB:**

```
DBConnector(const string& dbName, unsigned int timeout, bool isTcpConn);
```



**Example:**

```
diff --git a/src/sonic-stp/daemon/stpsync/stp_sync.cpp b/src/sonic-stp/daemon/stpsync/stp_sync.cpp

-DBConnector db(APPL_DB, DBConnector::DEFAULT_UNIXSOCKET, 0);
-DBConnector cfgDb(CONFIG_DB, DBConnector::DEFAULT_UNIXSOCKET, 0);
-DBConnector stateDb(STATE_DB, DBConnector::DEFAULT_UNIXSOCKET, 0);
+DBConnector db("APPL_DB", 0);
+DBConnector cfgDb("CONFIG_DB", 0);
+DBConnector stateDb("STATE_DB", 0);
```



##### 2.2.2.2 New Design of Python Interface: DBConnector()

The *swsscommon.DBConnector* Python interface is a SWIG wrapper to the C++ DBConnector. The changes in the applications using the swsscommon Python interface will be similar to the one described in the section 2.2.2.1.



**Example:**


```
diff --git a/src/sonic-tam/dropmgr/dropmgrd b/src/sonic-tam/dropmgr/dropmgrd

-        self.app_db = swsscommon.DBConnector(swsscommon.APPL_DB, 0, True)
+        self.app_db = swsscommon.DBConnector("APPL_DB", 0, True)
```



##### 2.2.2.3 New Design of Python Interface: SonicV2Connector()

The SonicV2Connector() interface in swsssdk-py library has been updated to connect to the appropriate RedisDB instance. Users of this API need not change the way the connector is called.



##### 2.2.2.4 Golang: initialize DB connection Design

A go library, *db_config.go is used to obtain the TCP port number and the Unix domain socket path   for a given database name and not use hardcoded strings.



**Example:**


```
diff --git a/src/sonic-telemetry/dialout/dialout_client/dialout_client.go b/src/sonic-telemetry/dialout/dialout_client/dialout_client.go

-	separator, _ := sdcfg.GetDbId("CONFIG_DB")
+	separator, _ := sdc.GetTableKeySeparator("CONFIG_DB")
<snip>
-	dbn := spb.Target_value["CONFIG_DB"]
+	dbn := sdcfg.GetDbId("CONFIG_DB")


diff --git a/gnmi_server/server_test.go b/gnmi_server/server_test.go
     dbn := spb.Target_value["COUNTERS_DB"]
     rclient := redis.NewClient(&redis.Options{
         Network:     "tcp",
-        Addr:        "localhost:6379",
+        Addr:        sdcfg.GetDbTcpAddr("COUNTERS_DB"),
         Password:    "", // no password set
-        DB:          int(dbn),
+        DB:          sdcfg.GetDbId("COUNTERS_DB"),
```

###  2.2.3 Database Access Commands

The redis-cli command is used in various places in SONiC for a quick one shot access to the contents of a database. When the redis-cli command is used in Multi-DB configuration, the current usage of the redis-cli commands always points to the default RedisDB instance.  To overcome this issue, a new command sonic-db-cli has been introduced which accepts the DB name as its input parameter. The sonic-db-cli command internally creates a connection to the appropriate RedisDB instances for a given database name. The functionality of sonic-db-cli is exactly the same as redis-cli except that the *"-n db-id"* used in redis-cli is now replaced with the DB name. The sonic-db-cli command is part of the *swsssdk-py* package and is available in all SONiC application containers. All applications in order to be Multi-DB compliant must use sonic-db-cli instead of redis-cli. Similarly, the *redis-dump* command is also replaced with the *sonic-db-dump* command.



**Example:**

```
diff --git a/src/sonic-telemetry/dialout/dialout_client/dialout_client_test.go b/src/sonic-telemetry/dialout/dialout_client/dialout_client_test.go

-	exe_cmd(t, "redis-cli -n 4 hset TELEMETRY_CLIENT|Global retry_interval 5")
-	exe_cmd(t, "redis-cli -n 4 hset TELEMETRY_CLIENT|Global encoding JSON_IETF")
-	exe_cmd(t, "redis-cli -n 4 hset TELEMETRY_CLIENT|Global unidirectional true")
-	exe_cmd(t, "redis-cli -n 4 hset TELEMETRY_CLIENT|Global src_ip  30.57.185.38")
+	exe_cmd(t, "sonic-db-cli CONFIG_DB hset TELEMETRY_CLIENT|Global retry_interval 5")
+	exe_cmd(t, "sonic-db-cli CONFIG_DB hset TELEMETRY_CLIENT|Global encoding JSON_IETF")
+	exe_cmd(t, "sonic-db-cli CONFIG_DB hset TELEMETRY_CLIENT|Global unidirectional true")
+	exe_cmd(t, "sonic-db-cli CONFIG_DB hset TELEMETRY_CLIENT|Global src_ip  30.57.185.38")
```



To access the redis-cli shell use **-p < tcp-port number >** option. Use the [RedisDB to SONiC Database Mapping](#table-3-redisdb-to-sonic-database-mapping) to find the TCP port number to use.

**Example:**

```
root@sonic:/home/admin# redis-cli -p 26379
127.0.0.1:26379> hget PORT_TABLE:Ethernet0 admin_status
"up"
127.0.0.1:26379>

root@sonic:/home/admin# redis-cli -p 26379 hget PORT_TABLE:Ethernet0 admin_status
"up"

root@sonic:/home/admin# sonic-db-cli APPL_DB hget PORT_TABLE:Ethernet0 admin_status
up
```



### 2.2.4 Database Connector Changes Summary

Use the below mentioned table as a quick reference for making changes to the applications to ensure that the connectors used are Multi-DB compliant.

#### Table 4: Database Connector Changes

| Legacy                                                       | MultiDB Compliant                          |
| ------------------------------------------------------------ | ------------------------------------------ |
| redis-cli                                                    | sonic-db-cli                               |
| redis-dump                                                   | sonic-db-dump                              |
| swsscommon.DBConnector(swsscommon.APPL_DB, 0, True)          | swsscommon.DBConnector("APPL_DB", 0, True) |
| DBConnector db(APPL_DB, DBConnector::DEFAULT_UNIXSOCKET, 0); | DBConnector db("APPL_DB", 0);              |
| swsscommon.DBConnector(APPL_DB, "localhost", 6379, 0)        | swsscommon.DBConnector("APPL_DB", 0, True) |



# 3 Design

## 3.1 Overview
Refer to section 2.
## 3.2 DB Changes
Refer to section 2.
### 3.2.1 CONFIG DB
### 3.2.2 APP DB

### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
All database accesses will be using the new database connector APIs defined in section 2.2.2 and 2.2.3.

### 3.3.2 Other Process 

All database accesses will be using the new database connector APIs defined in section 2.2.2 and 2.2.3.

## 3.4 SyncD
All database accesses will be using the new database connector APIs defined in section 2.2.2 and 2.2.3.

## 3.5 SAI
No changes proposed.

# 4 User Interface
### 4.1 Data Models
Future release.

### 4.2 CLI
#### 4.2.1 Configuration Commands

Future release.

#### 4.2.2 Show Commands

**show database map**

This command displays the current Multi-DB configuration currently in use.

- **Usage:**
  show database map

- **Example:**

```
ID   Name               RedisDB Instance    TCP Port    Socket Path
----------------------------------------------------------------------------------
0    APPL_DB            redis2              26379       /var/run/redis/redis2.sock
1    ASIC_DB            redis3              36379       /var/run/redis/redis3.sock
2    COUNTERS_DB        redis               6379        /var/run/redis/redis.sock
3    LOGLEVEL_DB        redis               6379        /var/run/redis/redis.sock
4    CONFIG_DB          redis               6379        /var/run/redis/redis.sock
5    PFC_WD_DB          redis               6379        /var/run/redis/redis.sock
5    FLEX_COUNTER_DB    redis               6379        /var/run/redis/redis.sock
6    STATE_DB           redis               6379        /var/run/redis/redis.sock
7    SNMP_OVERLAY_DB    redis               6379        /var/run/redis/redis.sock
8    ERROR_DB           redis               6379        /var/run/redis/redis.sock
```

#### 4.2.3 Debug Commands

N/A

#### 4.2.4 IS-CLI Compliance
N/A

### 4.3 REST API Support

Future release.

### 4.4 Service and Docker Management

No changes proposed.

# 5 Flow Diagrams
Flow diagrams are documented in  [Support Multiple user-defined redis database instances](https://github.com/dzhangalibaba/SONiC/blob/database_doc/doc/database/multi_database_instances.md) and  [Warm-Boot Support](https://github.com/Azure/SONiC/blob/95836ce967e9c140b51b93aeb55fddc91b232d87/doc/database/multi_database_instances.md#proposed-new-implementation)

# 6 Error Handling

No changes proposed.

# 7 Serviceability and Debug
All new database access APIs ensure that the existing debug and serviceability provided in swss and other applications are not affected.

# 8 Warm Boot Support
Enablement of Multi-DB support introduces an additional task of database migration that needs to be handled under various scenarios. These scenarios are captured in the next section. Regardless of the scenarios, the additional task  of data migration adds to the overall time needed to complete the Warm-Boot process. To minimize the control plane down time during Warm-Reboot, the SONiC community has proposed an optimal scheme that reduces both the time and complexity involved in this operation. Broadcom SONiC will adopt the same approach as the community version of SONiC. Details of this mechanism can be found in the new proposal, [Warm-Boot Support](https://github.com/Azure/SONiC/blob/95836ce967e9c140b51b93aeb55fddc91b232d87/doc/database/multi_database_instances.md#proposed-new-implementation).

Based on the proposed design a warm-reboot command triggers a migration process that follows the below mentioned steps:
- Transfers all the in-memory databases in each of the existing instances to a single temporary DB which is also in memory
- The redis-cli SAVE command is used to save the contents of this temporary DB to an RDB file
- When the new image boots , multiple RedisDB instances are created based on the switch's Multi-DB configuration.
- The saved rdb file is then restored into each of the created RedisDB instances. The unused databases of the RedisDB instances are identified and flushed.
- The switch applications access the restored databases

From a performance standpoint, these above tasks will be adding up to the overall warm-boot completion time. The effect will be quantified after testing the Warm-Reboot test cases.

Future optimization could include saving the rdb file in a warm-memory instead of the disk, which will reduce the overall latency induced by disk I/O. 

## 8.1 Warm-Boot database Migration scenarios:
The algorithm proposed by the SONiC community handles the below listed scenarios:

#### Table 5: Warm-Boot use cases

| From    | To | Use Case  |
| :----------: | :----------: | :----------: |
| Single-DB    | Multi-DB    | Image upgrade from SONIC 2.x, 3.0.x to 3.1    |
| Multi-DB Scheme-A    | Multi-DB Scheme-A    | Patch upgrade from 3.1 to 3.1.x (same DB scheme)    |
| Multi-DB Scheme-A    | Multi-DB Scheme-B     | Patch/Version upgrade from 3.1 (different DB scheme) |
| Multi-DB    | Single-DB  | Image Downgrade or reverting to an image using single  DB scheme |



## 8.2 Third-party application Data Migration

Broadcom SONIC 3.1 supports installation and management of third party applications(Dockers). These applications may choose to use the Redis Database service either by defining a new Redis instance or creating new databases in existing Redis instances. The Warm-Reboot data migration process will migrate the third-party data in databases that have been configured through database_config.json.  If they are defined and configured separately (not as part of database_config.json), then it will be up to the application to handle its data migration. 



# 9 Scalability

As an alternative to the Multi-instance method for improving the performance, the SONiC community considered the Redis clustering solution where a single database is served by a cluster of Redis nodes. The cluster also provides for high-availability. However, due to several other limitations as listed in the [SONiC community design document](https://github.com/Azure/SONiC/blob/master/doc/database/multi_database_instances.md#potential-redis-cluster-solution), this solution was not used.



# 10 Unit Tests
The following tests are limited to verify basic infrastructure sanity post the code merge from the community. Application specific unit tests are tracked in a separate document. Various automated unit tests are already defined in swss-common and swss-py-swsssdk repositories to test database connector library APIs.



- Verify that the system becomes operational with a single RedisDB instance to maintain all SONiC databases.
- Verify that the system becomes operational with each SONiC DB as part of a separate RedisDB instance.
- Verify that the system becomes operational with each SONiC DB assigned to a non-default DB ID's. e.g Use 4 for APPL_DB and 0 for CONFIG_DB.
- Verify that the system becomes operational with two or more instances, with each instance handling more than one DB. The layout will be modified to find the optimal combination.
- Verify warm-reboot completes successfully without any link flapping during the process.
- Verify that warm-reboot works across images which have a different switch default Multi-DB configuration as described in the [Table 5: Warm-Boot use cases](#table-5-warm-boot-use-cases).
- 100% Pass rate in DevSanity tests on a single RedisDB instance.
- 100% Pass rate in DevSanity tests with Multi-DB configuration.
- Verify that the multi-DB configuration is supported on real switch hardware, SONiC-VS and docker-vs environment.
- Verify that the SONiC image upgrades from Arlo/Buzznik to Buzznik+ works seamlessly.
- Verify that the SONiC image downgrades from Buzznik+ to Arlo/Buzznik works seamlessly.
- Verify that a user defined database config file is ignored if it doesn't have valid contents.




# Appendix
## Case Study

### Test Scenario

Establish BGP neighborship between DUT and a traffic generator port. Distribute 128K routes to the DUT. Identify the time duration spent by the DUT to install routes into the switch ASIC. The timestamp of each route addition event is recorded in the file */var/log/swss/sairedis.rec*. The difference between first and last route add timestamp provides the total time taken for the DUT to learn all 128K routes. Similarly, route withdrawal tests can be performed and timed.



### Observations

| **Test description**                                         | **Single RedisDB Instance** | **Multi-DB Configuration( 3 instances)** |
| ------------------------------------------------------------ | --------------------------- | ---------------------------------------- |
| Time taken to add 128K routes in the switch hardware         | 48 seconds                  | 31 seconds                               |
| Time taken to delete 128K routes in the switch hardware      | 40 seconds                  | 26 seconds                               |
| redis-service process CPU usage when routes are being added or removed to the switch hardware | 95%                         | 25% (redis2)<br/>25% (redis3)            |



### Conclusion

When using a Multi-DB configuration, parallel processing of database accesses results in a reduced CPU usage. Spare CPU cycles are used by the zebra routing application and syncd for processing route add/delete operations more efficiently. This results in a faster system response time as observed.

