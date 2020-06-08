# SONiC Default Configuration

# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [Feature Overview](#1-feature-overview)
      * [Requirements](#11-requirements)
      * [Design Overview](#12-design-overview)
  * [Functionality](#2-functionality)
      * [Target Deployment Use cases](#21-target-deployment-use-cases)
      * [Functional Description](#22-functional-description)
          * [Default Configuration Profiles](#221-default-configuration-profiles)
              * [L2 Switch Configuration Profile](#2211-l2-switch-configuration-profile)
              * [L3 Switch Configuration Profile](#2212-l3-switch-configuration-profile)
          * [Erase Switch Configuration](#222-erase-switch-configuration)
          * [Default Port Configuration](#223-default-port-configuration)
  * [Design](#3-design)
      * [DB Changes](#32-db-changes)
      * [User Interface](#36-user-interface)
          * [Data Models](#361-data-models)
          * [CLI](#362-cli)
              * [Configuration Commands](#3621-configuration-commands)
              * [Show Commands](#3622-show-commands)
              * [IS-CLI Compliance](#3624-is-cli-compliance)
          * [REST API Support](#363-rest-api-support)
  * [Flow Diagrams](#4-flow-diagrams)
  * [Unit Test](#9-unit-test)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)
[Table 2: Requirement Traceability Matrix](#table-2-requirement-traceability-matrix)
[Table 3: Default Configuration Profiles](#table-3-default-configuration-profiles)
[Table 4: L2 Profile Platform Constants](#table-4-l2-profile-platform-constants)
[Table 5: L3 Profile Platform Constants](#table-5-l3-profile-platform-constants)
[Table 6: IS-CLI Compliance](#table-6-is-cli-compliance)

# Revision
| Rev  |    Date    |       Author       | Change Description |
| :--: | :--------: | :----------------: | ------------------ |
| 0.1  | 06/08/2020 | Rajendra Dendukuri | Initial version    |

# About this Manual
This document provides information about how switch configuration files are managed in a Broadcom SONiC switch. Some of the behavior may also be applicable for community version of SONiC.
# Scope
This document describes the high level design of how default configuration of a SONiC switch is created. The topics discussed in this document are limited to Broadcom SONiC.



# Definition/Abbreviation

### Table 1: Abbreviations
| **Term** | **Meaning**           |
| -------- | --------------------- |
| RPC      | Remote Procedure Call |

# 1 Feature Overview

When a SONiC switch boots for the first time, the SONiC configuration is created using templates provided as part of the image. These templates are used to create a configuration file */etc/sonic/config_db.json* which is loaded into the Config DB. In addition to the */etc/sonic/config_db.json*, there can be other files that can be created during this process.  This first boot configuration is referred to as factory default configuration or simply default configuration throughout this document. A more detailed explanation on the framework used to create the default configuration is provided in the  [SONiC Configuration Setup manual](https://github.com/Azure/SONiC/blob/1e289547d1a58ffc1ade50bfd6f0b28016be8566/doc/ztp/SONiC-config-setup.md).



The various conditions under which the default configuration is generated are described in this document.




## 1.1 Requirements

### 1.1.1 Functional Requirements

- 1.1.1.0 - A predictable configuration sequence must be defined when a SONiC switch boots for the first time.
- 1.1.1.1 - The user must be provided a method to restore a SONiC switch to its default configuration state.
- 1.1.1.2 - The user can be provided a method to choose the first boot configuration state the SONiC switch needs to be restored to.
- 1.1.1.3 - The user should be able to save the switch configuration state as a named checkpoint. At a later point in time, such a saved checkpoint can be specified as an input to specify the intended switch configuration. 
- 1.1.1.4 - The user must be provided a method to restore a selected switch port's configuration to its default configuration state.
- 1.1.1.5 - All configurations derived from a port configuration must be removed except break out configuration. The port will continue to be in the broken out state when switch port's config is restored to its default configuration. The default speed of the port will be defined by the breakout mode configured on its master port.
- 1.1.1.6 - When a switch port is being restored to its default configuration, configuration operations on the port are allowed only after the configuration restoration procedure is completed.
- 1.1.1.7 -  The user should be able to save the configuration state of a selected switch port as a named checkpoint. At a later point in time, such a saved checkpoint can be specified as an input to specify the intended switch port configuration instead of the default configuration.

### 1.1.2 Configuration and Management Requirements

- 1.1.2.0 - The user must be able to view and choose between one or more available default configuration profiles.
- 1.1.2.1 - The available configuration profiles are identified using a unique name string.
- 1.1.2.2 - When the user changes the default configuration profile setting, the switch configuration is restored to the default configuration defined by the newly chosen default configuration profile.

### 1.1.3 Scalability Requirements
N/A
### 1.1.4 Warm Boot Requirements

N/A

### 1.1.5 Requirement Traceability

### Table 2: Requirement Traceability Matrix

| Requirement No | Requirement Description                                      | Supported Release |
| -------------- | ------------------------------------------------------------ | ----------------- |
| 1.1.1.0        | A predictable configuration sequence must be defined when a SONiC switch boots for the first time. | 2.0               |
| 1.1.1.1        | The user must be provided a method to restore a SONiC switch to its default configuration state. | 3.0               |
| 1.1.1.2        | The user can be provided a method to choose the first boot configuration state the SONiC switch needs to be restored to. | 2.0               |
| 1.1.1.3        | The user should be able to save the switch configuration state as a named checkpoint. At a later point in time, such a saved checkpoint can be specified as an input to specify the intended switch configuration. | Future            |
| 1.1.1.4        | The user must be provided a method to restore a selected switch port's configuration to its default configuration state. | 3.1               |
| 1.1.1.5        | All configurations derived from a port configuration must be removed except break out configuration. The port will continue to be in the broken out state when switch port's config is restored to its default configuration. The default speed of the port will be defined by the breakout mode configured on its master port. | 3.1               |
| 1.1.1.6        | When a switch port is being restored to its default configuration, configuration operations on the port are allowed only after the configuration restoration procedure is completed. | 3.1               |
| 1.1.1.7        | The user should be able to save the configuration state of a selected switch port as a named checkpoint. At a later point in time, such a saved checkpoint can be specified as an input to specify the intended switch port configuration instead of the default configuration. | Future            |
| 1.1.2.0        | The user must be able to view and choose between one or more available default configuration profiles. | 2.0               |
| 1.1.2.1        | The available configuration profiles are identified using a unique name string. | 2.0               |
| 1.1.2.2        | When the user changes the default configuration profile setting, the switch configuration is restored to the default configuration defined by the newly chosen default configuration profile. | 2.0               |




## 1.2 Design Overview

### 1.2.1 Basic Approach
The [SONiC Configuration Setup](https://github.com/Azure/SONiC/blob/1e289547d1a58ffc1ade50bfd6f0b28016be8566/doc/ztp/SONiC-config-setup.md) feature is used to create factory default configuration of a switch. Appropriate factory default configuration hooks are used to define and generate the default configuration files. 

Broadcom SONiC supports the following default configuration profiles:

### Table 3: Default Configuration Profiles

| Profile | Description                  |
| ------- | ---------------------------- |
| l2      | Layer 2 Switch Configuration |
| l3      | Layer 3 Router Configuration |

As part of the default configuration generation step, the following actions are performed:

- A */etc/sonic/config_db.json* file is created according to the configured factory default configuration profile
- The switch platform's config.bcm file is adjusted according to the configured default configuration profile

### 1.2.2 Container
#### 1.2.2.1 Management Framework

In Broadcom SONiC, the management framework docker container will mount the host volume */usr/share/broadcom_sonic* in read only mode. This allows the REST server to access available default configure profiles and also know which default configuration profile is currently active. 

### 1.2.3 SAI Overview
N/A

# 2 Functionality
## 2.1 Target Deployment Use Cases
While configuring and managing a network, it is a common practice used by the switch administrators to try different switch configurations. The SONiC switch is expected to come with some predefined configuration which satisfies the first level of basic configuration expected on a typical network switch. The ports need to be defined, appropriate port configuration parameters like speed, MTU and administrative mode need to be set to a typical configuration. The default configuration profiles provided by Broadcom SONiC are intended to provide such configuration which can be used as base to build more complex switch configurations.



It is also a common practice among network administrators to restore the switch to its base configuration state, the default configuration, before they start to rebuild the configuration. This is required for taking a switch offline or to re-provision it. The default configuration state helps the user to place the switch to an identified predictable configuration. Similar to the switch configuration, it is a common practice to undo a configuration made on a single switch port.



## 2.2 Functional Description
### 2.2.1 Default Configuration Profiles



A *default configuration profile* can be described as a set of configuration templates and scripts use to to generate configuration files. In Broadcom SONiC the following files define a configuration profile



1. Description: *<profile_name>/description*
2. Config DB configuration template: *<profile_name>/templates/config_db.json.j2*
3. scripts/factory-default-hooks/10-render-config-db-json
4. Configuration rendering hooks:
   - ConfigDB *scripts/factory-default-hooks/10-render-config-db-json*
   - config.bcm changes: *scripts/factory-default-hooks/09-swap-bcm-config*



The constituents of a default configuration profile will be adjusted as per the needs of Broadcom SONiC.

The default configuration profiles in Broadcom SONiC are defined in the below mentioned source code directory. 

```
organization_extensions/vendor/broadcom/
|-- config_profiles
|   |-- l2
|   |   |-- description
|   |   `-- templates
|   |       `-- config_db.json.j2
|   `-- l3
|       |-- description
|       `-- templates
|           `-- config_db.json.j2
|-- extensions.sh
`-- scripts
    |-- config-profile-migration-hooks
    |   |-- 08-config-profile-backup
    |   `-- 08-config-profile-migrate
    |-- config-profiles
    `-- factory-default-hooks
        |-- 09-swap-bcm-config
        `-- 10-render-config-db-json
```



The *l3* profile is selected as the default profile used on a newly installed image. The contents of the file */usr/share/broadcom_sonic/config_profiles/active* on the SONiC switch indicates the current configured default configuration profile on a switch. The profile name (l2, l3, ..) is used to refer to a default configuration profile in the code as well as user commands. 



The organization extension script *organization_extensions/vendor/broadcom/extensions.sh* is used to copy the configuration profiles infrastructure into the switch image as part of SONiC image compilation process. The below diagram is a representation of the configuration profiles that are available on a SONiC switch.

```
root@sonic:/home/admin# tree --charset=ascii /usr/share/broadcom_sonic
/usr/share/broadcom_sonic
|-- config_profiles
|   |-- active
|   |-- l2
|   |   |-- description
|   |   `-- templates
|   |       `-- config_db.json.j2
|   `-- l3
|       |-- description
|       `-- templates
|           `-- config_db.json.j2
`-- scripts
    |-- config-profile-migration-hooks
    |   |-- 08-config-profile-backup
    |   `-- 08-config-profile-migrate
    |-- config-profiles
    `-- factory-default-hooks
        |-- 09-swap-bcm-config
        `-- 10-render-config-db-json
```



The factory default hooks in config-setup infrastructure are also prepared as part of the extension script. 

```
root@somic:/home/admin# tree --charset=ascii /etc/config-setup/factory-default-hooks.d
/etc/config-setup/factory-default-hooks.d/
|-- 09-swap-bcm-config -> /usr/share/broadcom_sonic/scripts/factory-default-hooks/09-swap-bcm-config
`-- 10-render-config-db-json -> /usr/share/broadcom_sonic/scripts/factory-default-hooks/10-render-config-db-json

```



When a SONiC switch boots and there is no configuration defined in the switch, the factor default configuration is generated using the scripts listed in the */etc/config-setup/factory-default-hooks.d* directory. As indicated above, the scripts *09-swap-bcm-config* and *10-render-config-db-json* are executed which use the profile name defined in the file */usr/share/broadcom_sonic/config_files/active* as a key to generate the *config.bcm* and *config_db.json* files.



A command line utility  */usr/bin/config-profiles* is provided to configure the default configuration profile.



```
root@sonic:/home/admin# /usr/bin/config-profiles -h
 Usage:  config-profiles < check <profile-name> | factory [profile-name] |
                           get factory | list | set factory <profile-name> >

         check        - Verify if user provided configuration profile exists.
         factory      - Load factory default config if factory default
                        profile has changed or if startup config '/etc/sonic/config_db.json'
                        is not present.
         get factory  - Display configuration profile currently marked as factory default.
         list         - Display list of available configuration profiles.
         set factory  - Set user provided configuration profile as factory default.
         help | -h    - Display this text.
```



The sonic YANG model provides an RPC to modify the configured default configuration profile. This RPC takes, default configuration profile name as input. As a result of this RPC action, the current running configuration is removed, a new startup configuration is created using the new default configuration profile and the created startup configuration is applied to the switch. The *config-profile* command is used to initiate the change of default configuration profile. The *config-setup factory* command is used by the *config-profile* command to create a new startup configuration. The *config reload* command is then used by the *config-profile* command to clear and load the newly created configuration.



The following sections describe the switch configuration defined by the supported default configuration profiles in Broadcom SONiC.

#### 2.2.1.1 L2 Switch Configuration Profile

The following Switch configuration *(l2/templates/config_db.json.j2)* is rendered as part of L2 default configuration profile:

- Device metadata

  - Hardware SKU name (hwsku)
  - MAC Address
  - Platform Name
  - Type: ToRRouter
  - hostname: sonic
  - default_config_profile: l2

- VLAN 1

  - All ports are members of VLAN 1
  - Spanning Tree Enabled
  - Forwarding Delay: 15s
  - Hello Time: 2s
  - Max Age Interval: 20s
  - Bridge Priority: 32768

- Bridge Ageing Time: 600s

- Spanning Tree

  - Enabled
  - Forward Delay: 15s
  - Hello Time: 2s
  - Max Age Interval: 20s
  - Spanning Tree Mode: pvst
  - Bridge Priority: 32768
  - Root Guard Timeout: 30s

- Ports

  - Administrative mode: Up
  - Speed
  - MTU: 9100
  - Spanning tree: Enabled
  - BPDU Guard: Disabled
  - BPDU Guard - do_disable : Disabled
  - Root Guard: Disabled
  - Port Fast Mode: Enabled
  - Uplink Fast Mode: Disabled

- KDUMP

  - Enabled
  - Max number of Kdumps: 3
  - Memory: 0M-2G:256M,2G-4G:320M,4G-8G:384M,8G-:448M

  

  **Platform Specific Configurations** *(09-swap-bcm-config)*:

  The following platform specific configuration is adjusted in the config.bcm file for L2 default configuration profile:

  ALPM is disabled

### Table 4: L2 Profile Platform Constants 
| Broadcom Chip | Max FDB Entries | Max L3 Host Entries |
| ------------- | --------------- | ------------------- |
| Tomahawk      | 136K            | 8K                  |
| Tomahawk2     | 256K            | 8K                  |
| Trident2      | 288K            | 16K                 |
| Trident3      | 288K            | 16K                 |

  


#### 2.2.1.2 L3 Switch Configuration Profile

The following Switch configuration *(l3/templates/config_db.json.j2)* is rendered as part of L3 default configuration profile:

- Device metadata

  - Hardware SKU name (hwsku)
  - MAC Address
  - Platform Name
  - Type: LeafRouter
  - hostname: sonic
  - default_config_profile: l3

- Bridge Ageing Time: 600s

- Ports

  - Administrative mode: Down
  - Speed
  - MTU: 9100

- KDUMP

  - Enabled
  - Max number of Kdumps: 3
  - Memory: 0M-2G:256M,2G-4G:320M,4G-8G:384M,8G-:448M

  

  **Platform Specific Configurations** *(09-swap-bcm-config)*:

  The following platform specific configuration is adjusted in the config.bcm file for L3 default configuration profile:

  ALPM shared mode "2"
### Table 5: L3 Profile Platform Constants
| Broadcom Chip | Max FDB Entries |
| ------------- | --------------- |
| Tomahawk      | 40K             |
| Tomahawk2     | 40K             |
| Trident2      | 16K             |
| Trident3      | 40K             |

  

### 2.2.2 Erase Switch Configuration

A switch's default configuration can be created and applied as the switch startup configuration upon user's request. The following operations are available to the user:

  - **Erase switch configuration excluding management configuration** : This operation deletes the existing startup configuration JSON file */etc/sonic/config_db.json* and all other application configuration files in the */etc/sonic* directory. The management interface configuration in the startup configuration file is retained so that the user can access the switch using the same management address along with other factory default configuration after the switch reboots. A new startup configuration JSON file is created during the switch bootup. It includes restored management interface configuration and the switch factory default configuration generated from the current active default configuration profile.
  - **Erase switch configuration** : This operation deletes the startup configuration JSON file */etc/sonic/config_db.json* and all other application configuration files in the */etc/sonic* directory. The management interface configuration in the startup configuration JSON file is also removed. The SONiC switch boots with a factory default configuration file. A new startup configuration JSON file is created during the switch bootup. It includes the switch factory default configuration generated from the current active default configuration profile.
  - **Restore switch to first boot state** : This operation removes all the changes made by the user to the SONiC switch files. All user installed packages and file changes are removed. It also deletes the startup configuration JSON file */etc/sonic/config_db.json* and the files in the */etc/sonic* directory. The SONiC switch is reverted to a state similar to a newly installed image. After the SONiC switch is rebooted, if the Zero Touch Provisioning (ZTP) feature is enabled, the SONIC switch will start performing ZTP to discover and download the switch configuration. If ZTP is disabled, a new startup configuration JSON file is created during the switch bootup. It includes the switch factory default configuration generated from the current active default configuration profile.
  - **Cancel  switch configuration erase operation** : For all of the above configuration erase operations to take effect, the user has to reboot the switch after issuing the erase command. If the user wishes to not proceed with the configuration removal operation, the configuration erase cancel command can be used to undo the previously issued *configuration erase* command. Also, if a configuration save operation is performed it cancels the pending configuration erase command.

  The wam-reboot operation cannot be performed if there is a pending configuration erase operation. If the user issues a warm-reboot command, it is aborted with an alert message.

  

### 2.2.3 Default Port Configuration

The default port configuration of a switch port is also part of the switch's default configuration. The sections [2.2.1.1 L2 Switch Configuration Profile](#2211-l2-switch-configuration-profile) and [2.2.1.2 L3 Switch Configuration Profile](#2212-l3-switch-configuration-profile) provide details about the port configuration that is created for the corresponding default configuration profile. The default port configuration is applied to all ports of the switch.

  

The switch administrators, as part of provisioning the network, configure various parameters on all the switch ports. It may involve VLAN membership, network protocol configurations, port specific parameters like MTU or an IP address. Most of these configurations are not defined as part of the default configuration. After going through various configuration steps, the user may choose to revert all the changes done to a port's configuration. This operation can be performed easily by issuing the *default interface interface-name*  command. The port's configuration is reverted to the configuration defined by the current active default configuration profile. Only port breakout configuration of the port is not modified as it alters the identity of the port.

  

To perform the *default interface* configuration operation, the user has to issue the corresponding KLISH command.

```
admin@sonic:~$ sonic-cli
sonic# configure terminal
sonic(config)# default interface Ethernet 8
```



The port configuration restore operation is performed as an RPC action by the REST server. The RPC action is initiated by the KLISH CLI command and uses interface name as input. The *default interface* KLISH CLI command is blocked until the RPC operation is completed and feedback is provided to the user. The *default interface* RPC command can also be issued by a REST client. No configurations are allowed on the port, including a parallel instance of the *default interface* command. while a port is going through the port configuration restore operation. A STATE_DB table entry is used to mark the port to be in a busy state. The CVL validation layer in the management framework will make use of this busy state to reject any configuration requests on the interface. The busy status is also used as a gating check to initiate the RPC command and this allows the REST server to not allow more than one parallel instance of *default interface* command on the same port. As the *default interface* command is designed to be a synchronous operation, there is no status command available to the user to view the operation's progress.



#### Dependent Configuration Delete Operations

The first step of port configuration restore operation is to identify all the configurations performed on the port and remove them. The REST server uses the YANG configuration representation model of the SONiC switch to identify the dependent configuration and the order between each dependent configurations. Delete operations are then issued by the REST server to remove each dependent configuration in the identified order. 



#### Default Port Configuration Template

After the dependent configuration delete operations, the next step is to configure the port to the default configuration state. A port's default configuration profile is represented in the form of a jinja2 (j2) template file. It is accessible to the REST server in the path */usr/share/broadcom_sonic/config_profiles/active-profile-name/templates/port_config.j2*. When the *default interface* command is executed, the port configuration restore operation creates a port specific configuration file using this template file. The contents of the generated file are used by the REST server to configure the port to its default configuration state.



# 3 Design
## 3.1 Overview
## 3.2 DB Changes
### 3.2.1 CONFIG DB

The active default configuration profile value is set in the DEVICE_METADATA table. 

```
table: DEVICE_METADATA
key: DEVICE_METADATA|localhost
field: default_config_profile
value: l3
```

### 3.2.2 STATE DB

A new table **PORT_CONFIG_STATUS** is introduced to mark a port state as busy. This will be used to block port related configuration from the management framework.

```
table: PORT_CONFIG_STATUS
key: PORT_CONFIG_STATUS|Ethernet0
field: state
value: busy

table: PORT_CONFIG_STATUS
key: PORT_CONFIG_STATUS|Ethernet0
field: state
value: ok
```

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent

A few changes are expected to be made to configure the **PORT_CONFIG_STATUS** table entries while executing the *default interface* command. More details will be added to this section after implementing the feature.

### 3.3.2 Other Process 
N/A

## 3.4 SyncD
N/A

## 3.5 SAI
N/A

## 3.6 User Interface
### 3.6.1 Data Models
```
module: sonic-config-mgmt
  +--ro factory-default-profiles
     +--ro factory-default-profile* [name]
        +--ro name       -> ../state/name
        +--ro state        
           +--ro name         string
           +--ro description  string
           +--ro active       boolean

  rpcs:
    +---x write-erase
       +---w input
       |  +---w subcmd?   string
       +--ro output
          +--ro status?          int32
          +--ro status-detail?   string
```



```
module: openconfig-interfaces-ext

  rpcs:
    +---x default
       +---w input
       |  +---w interface   string
       +--ro output
          +--ro status?          int32
          +--ro status-detail?   string
```



### 3.6.2 CLI
#### 3.6.2.1 Configuration Commands

For SONiC switches, the following CLI commands are provided to manage default configuration.

#### factory default profile < profile-name >

**Description**

The *factory default profile* command is used to set the factory default configuration profile of a SONiC switch. This command removes the current switch running configuration, creates a new startup configuration file using the input default configuration profile name. The newly created startup configuration is also applied as part of this command. This command may result in a loss of switch connectivity as it results in a restart of all SONiC application services.

**Usage**

```
factory default profile < profile-name >
```

**Example**

```
sonic# configure terminal
sonic(config)# factory default profile l2
```



#### write erase

**Description**

The *write erase* command is used to clear the existing switch configuration files except the management interface configuration. The *write erase* command deletes the startup configuration JSON file and all application configuration files in the */etc/sonic* directory. The management interface configuration in the startup configuration file is retained so that the user can access the switch using the same management address. For the *write erase* command operation to take effect, the user has to reboot the switch after issuing the write erase command. If the user wishes to not proceed with the configuration removal operation, the *write erase cancel* command can be used to undo the previously issued *write erase* command.

**Usage**

```
write erase
```

**Example**

```
sonic# configure terminal
sonic(config)# write erase 
```



#### write erase boot

**Description**

The "write erase boot" command deletes the startup configuration JSON file and all application configuration files in the /etc/sonic directory. The management interface  configuration in the startup configuration JSON file is also removed. For the *write erase* command operation to take effect, the user has to reboot the switch after issuing the *write erase boot* command. The SONiC switch boots with a startup configuration which is created using the current active factory default configuration profile.

**Usage**

```
write erase boot
```

**Example**

```
sonic# configure terminal
sonic(config)# write erase boot
Existing switch configuration files will be removed, continue? [y/N]:
```



#### write erase install

**Description**

The *write erase install* command removes all changes made by the user to a SONiC switch. All user installed  packages and file changes are removed. It also deletes the startup configuration JSON file and the files in */etc/sonic* directory. The SONiC switch is reverted to the same state as a newly installed image. For the *write erase install* command operation to take effect, the user has to reboot the switch after issuing the *write erase install* command. After the SONiC switch is rebooted, if the Zero Touch Provisioning (ZTP) feature is enabled, the SONIC switch will start performing ZTP to discover and download the switch configuration. If the ZTP feature is disabled, the SONiC switch boots with a startup configuration which is created using the current active factory default configuration profile.

**Usage**

```
write erase install
```

**Example**

```
sonic# configure terminal
sonic(config)# write erase install
All SONiC switch content will be restored to default values, continue? [y/N]:
```



#### no write erase

**Description**

For the *write erase* command to take effect, the user has to reboot the switch after issuing the erase command. If the user wishes to not proceed with the configuration removal operation, the configuration *no write erase* command can be used to undo the previously issued *write erase* command. The *no write erase* command can be used to undo the *write erase boot* and *write erase install* command as well.

**Usage**

```
no write erase
```

**Example**

```
sonic# configure terminal
sonic(config)# no write erase
Switch configuration erase operation will be cancelled, continue? [y/N]:
```



#### default interface

**Description**

The *default interface* command is used to delete all the all the user configuration of a specified interface(s) and restore it to its default state.


**Usage**

```
default interface < if >
```

**Example**

```
sonic# configure terminal
sonic(config)# default interface Ethernet 0
```



#### 3.6.2.2 Show Commands

#### show factory default profiles

**Description**

Use this command to list all supported factory default configuration profiles. It also specifies the current active default configuration profile. The current active profile name is used to create a startup configuration file when the *write erase* command is used.

- Name: Profile name used to refer to a default configuration profile
- Description: A descriptive string

**Usage**

```
show factory default profiles
```

**Example**

```
sonic# show factory default profiles
Factory Default: l3

Name            Description
-------------   -----------------------
l2              Layer 2 Switch Configuration
l3              Layer 3 Router Configuration
```

#### 3.6.2.3 Debug Commands
N/A

#### 3.6.2.4 IS-CLI Compliance

### Table 6: IS-CLI Compliance
|CLI Command|Compliance|IS-CLI Command (if applicable)| Link to the web site identifying the IS-CLI command (if applicable)|
|:---:|:-----------:|:------------------:|-----------------------------------|
| show factory default profiles | SONiC | | |
| factory default profile | SONiC | | |
| write erase | IS-CLI drop-in replace | write erase | https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/7-x/fundamentals/configuration/guide/b_Cisco_Nexus_9000_Series_NX-OS_Fundamentals_Configuration_Guide_7x/b_Cisco_Nexus_9000_Series_NX-OS_Fundamentals_Configuration_Guide_7x_chapter_01001.html |
| write erase boot | IS-CLI drop-in replace | write erase boot | https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/7-x/fundamentals/configuration/guide/b_Cisco_Nexus_9000_Series_NX-OS_Fundamentals_Configuration_Guide_7x/b_Cisco_Nexus_9000_Series_NX-OS_Fundamentals_Configuration_Guide_7x_chapter_01001.html |
| write erase install | IS-CLI-like | write erase | https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/7-x/fundamentals/configuration/guide/b_Cisco_Nexus_9000_Series_NX-OS_Fundamentals_Configuration_Guide_7x/b_Cisco_Nexus_9000_Series_NX-OS_Fundamentals_Configuration_Guide_7x_chapter_01001.html |
| default interface | IS-CLI drop-in replace | default interface | https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus7000/sw/interfaces/command/cisco_nexus7000_interfaces_command_ref/d_commands.html#wp2521540877 |



### 3.6.3 REST API Support

This section will be updated with the supported REST API after implementing the YANG data model.



### 3.6.4 Service and Docker Management

N/A
# 4 Flow Diagrams
**Default Interface Configuration Command Flow**

![](default_interface_flow_chart.png)



# 5 Error Handling
N/A

# 6 Serviceability and Debug
Appropriate log messages are generated by the REST server as part of the RPC command handling the *default interface* operation.

# 7 Warm Boot Support
N/A

# 8 Scalability
N/A

# 9 Unit Test
| ID   | Description                                                  |
| ---- | ------------------------------------------------------------ |
| 1    | Display the list of available configuration profiles and current active factory default configuration profile. |
| 2    | Modify factory default configuration profile to a supported profile name. Verify that the switch configuration is cleared a new default configuration is created as the startup configuration and applied to the switch. |
| 3    | Modify factory default configuration profile to a non-existent profile. |
| 4    | Modify factory default configuration profile to the current active factory default configuration profile name. No change in the switch configuration must be observed. |
| 5    | Issue write erase, reboot. Verify that management interface configuration is not modified and other configurations are restored to default values. |
| 6    | Issue write erase boot, reboot. Verify that all configurations are restored to default values. User installed packages in SONiC o/s remain intact. |
| 7    | Issue write erase install, reboot. Verify that all configurations are restored to default values and all user installed packages in SONiC o/s  are also removed. |
| 8    | "no write erase" to cancel a previously issued "write erase/write erase boot/write erase install" command. |
| 9    | Issue write erase/write erase boot/write erase install command followed by config save and reboot. Verify that the configuration erase is not performed and the switch uses the saved configuration file as configuration. |
| 10   | Issue write erase/write erase boot/write erase install commands. Issue warm-reboot command. The warm-reboot command should fail indicating that there is a pending configuration erase action. |
| 11   | Configure factory default configuration profile as l2. Issue "default interface" command and verify that the interface is restored to L2 default configuration. |
| 12   | Configure factory default configuration profile as l3. Issue "default interface" command and verify that the interface is restored to L3 default configuration. |
| 13   | Issue two "default interface Ethernet 0" commands in two sonic-cli sessions. Only one of them should succeed and the other one fails. |
| 14   | Issue "default interface Ethernet 0" command and while it is in progress,and add port Ethernet0 to a port channel. The port channel member add command should fail. |
| 15   | Issue "default interface Ethernet 0" command and after the command exits, issue a command to add the port Ethernet0 to a VLAN. The VLAN add command should succeed indicating that the port Ethernet0 is ready to accept new configurations. |
| 16   | Verify that the STATE_DB table PORT_CONFIG_STATUS indicates busy state when the "default interface" command is in progress. It should be set to ok, once the "default interface" command exits . |
