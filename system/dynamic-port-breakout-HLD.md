# Feature Name

Dynamic Port Breakout (DPB)

# High Level Design Document

#### Rev 0.1

# Table of Contents

* [List of Tables](#list-of-tables)

* [Revision](#revision)

* [About This Manual](#about-this-manual)

* [Scope](#scope)

* [Definition/Abbreviation](#definitionabbreviation)

# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision

|  Rev  |  Date      |  Author     | Change Description |
| ----- | ---------- | ----------- | ------------------ |
| 0.1 | 03/27/2020 | Vishnu Shetty | Initial version |
| 0.2 | 04/09/2020 | Vishnu Shetty | Update review comments |
| 0.3 | 04/21/2020 | Vishnu Shetty | Update review comments |
| 0.4 | 05/11/2020 | Vishnu Shetty | Update QoS config comments |
| 0.5 | 05/29/2020 | Vishnu Shetty | Update CLI and review comments |

# About this Manual

This document provides dynamic port break-out feature content.

# Scope

This document captures dynamic port break-out requirements and provides design overview.

# Definition/Abbreviation

### Table 1: Abbreviations
  
| Term | Meaning |
| ---- | ---- |
| CONFIG_DB | SONiC configuration database in Redis |
| CVL | Config VaLidation |
| DPB | Dynamic Port Breakout |

# 1 Feature Overview

Ports can be broken out to different sub-ports and speeds with subset of lanes in most HW today. Currently in SONiC, the port breakouts are only made possible by hard-coding the HW profiles that are loaded at boot-up time. If a different port breakout config is desired, a new image must be loaded, or at least services need to be restarted which impacts the traffic on the box (and on irrelevant ports). The DPB will address these issues, ie

- No system or config reload required,

- No traffic impact on other ports which are not subject to DPB configuration, and

- Users have option to choose a different break out mode

A high-level view of the operation:

1. DPB is initiated through certain management interface (SONiC management framework CLI,  REST)

2. Management framework infrastructure derives port dependencies from the SONiC YANG model, and updates the CONFIG DB. (e.g. VLAN member removal followed by port delete, and followed by new port addition on impacted ports).

3. Based on the CONFIG_DB update (ports delete followed by create), back-end deletes ports and all its dependencies (configurations and dynamic dependencies). Back-end will then create new ports in default state. Here, "back-end" primarily refers to SWSS and syncd components (orchagent, SAI etc). System has other dependency as well like protocols, platform services like xvrd, led, pmon, etc. For some of the components these DB triggers are "no op".

This document is complementary to the community DPB HLD. Link to the community HLD is [here](https://github.com/Azure/SONiC/blob/439944c120d59edf7f62ed28a902b1a3c07a35f8/doc/dynamic-port-breakout/sonic-dynamic-port-breakout-HLD.md). Please refer design section for major difference with community.

## 1.1 Requirements

High Level Requirements:

- SONiC to support port breakout dynamically. User should be able to change port breakout mode at run time without affecting the traffic on other ports. The remaining ports of the switch should not be touched, traffic should not be interrupted on those ports.
  
- Configuration dependencies and respective resources of the ports that are subject to DPB should be removed automatically. User should be able to query configuration dependencies on the ports. 

- After the breakout, the newly created ports shall be initialized with default port configurations. e.g., default flow control setting, etc.

The management framework generates port configuration dependencies and automatically deletes those dependencies in the CONFIG_DB. e.g., portchannel member remove followed by a port deletion. The SONiC YANG model is used to generate configuration dependencies.  Please refer to management framework design section and additional external documentations for details.
    

### 1.1.1 Functional (detail) Requirements

- Only allow valid break-out modes. This is achieved by a platform-specific file called "platform.json" that spells out port and lane capabilities and constraints.  
- Users should be able to migrate from a previous Broadcom sonic release. Migration scripts allow for this seamless transition. 
- If a given platform does not have the platform.json file, the DPB CLI shall error out. The system falls back to previous release behavior.
- All of the configuration dependency checking logic for a deleted port is implemented in the SONiC Management Framework. In order for this logic work correctly, the following must be true.
	- All of the affected configuration is in the CONFIG_DB.
	- A matching SONiC YANG file is available.
	- Where necessary (complex cases only), custom override functions are provided to delete the configuration.
- For respective User Interface (UI)
	- The above is always expected to be true to features supported in the Management Framework (including FRR features).
	- For Click features, if there is SONiC YANG provided through Management Framework support for the feature, then Click configuration will also work correctly both before and after a breakout operation.
	- For FRR, Configuration applied directly to FRR (through either vtysh or *.conf startup files in split mode) is not present in the CONFIG_DB, and so will not be cleaned up by a DPB operation. However, configuration applied to FRR through the Management framework is correctly cleaned up in FRR after a DPB operation. In reality this mean that vyhsh can be used for show but not configuration before and after a DPB operation.
- To support automatic configuration cleanup SONiC YANG model adoption is a must.
- The back-end components, CONFIG_DB listeners (majority of SWSS components, protocols) act on DB updates and clean-up all configurations and run time dependencies and resources.
- DPB interface (REST, CLI command) is supported in platform open-config YANG. On the YANG model it is update request.
- DPB operation result in port deletion and creation events. The ports must be shutdown before deletion. New ports created as part of "create" operation will be in the default state and configuration. The user need to take care of new desired configuration and any topology changes.  
- DBP configuration dependency checking is implemented in the SONiC Management Framework in broadcom-sonic builds. Community builds have a separate mechanism that is not used in Broadcom builds. However both methods depend upon SONiC YANG files, and this is required to be common between the two approaches. 
- "config reload" functionality shall remain available to users. Users shall be able to apply port breakout configuration directly in config DB and issue "config reload" (without issuing DPB CLI). It is not dynamic, existing configuration gets cleared and services get restarted.
- No custom MMU setting is applied as part of DPB. 
- When upgrading from a prior image into the new image supporting DPB:
 1. All HW SKUs that are defined in prior images must be supported in the new image.
 2. All HW SKUs are upgraded into the same exact HW SKU after upgrade into the new image. It is advised to migrate to **base** HW SKU to make use of seamless DPB capabilities. 
 3. No modifications allowed to ports that are already broken out by the virtue of HW SKU. For example, in case of DellEMC-Z9332f-C64O16SH (16 x (4x100G) + 16 x 400G), lower 64 ports remain to be 100G. The upper 16 ports are 400G, but the user may use DPB to break-out any of the upper 16 ports to 100G. The user may later undo breakout on these upper 16 ports. But the lower 16 front-panel ports will remain static at 16 x (4x100G). To overcome above restriction,  DB migration along with DPB specific changes required during release upgrade. Later this SKU can be switched to base SKU.
 4. Customers can (but don't have to) change HW SKU before upgrade, if deployment plans have changed. (Requires customers to change configuration - a maintenance activity)

5.  When downgrading from an image supporting DPB to an older image that DPB is not supported:
	 - User needs to remove all "new" configuration (features not supported in the old image) including DPB, before initiating a downgrade.
	 - The configuration must match the contents of the port-config.ini file that will be used by the downgrade image.

### 1.1.2 Scalability Requirements

Port breakout must be supported on all ports as per platform.json file. All the platform design, SDK, and ASIC constraints should be considered when preparing platform.json file. 

### 1.1.3 Warm Boot Requirements
  
Ports that are broken out must be retained across warmboot.


## 1.2 Design Overview

## 1.2.1 DPB Management Framework

![](https://lh5.googleusercontent.com/BnWFIXVVILN-knX-UjEU_gqbeQVwhUU4Jtk46g1iBTUnhZflaoLhwO4yoO2o1ON2YKU_aKcgVDpuzw92LwgzxE5ykv1hNsmlOvulMM_g15syPQnP-e0Td9xvKgE5DfEKEqFStDSX)

Overview:

1. DPB is initiated through management interface (CLI, REST, etc).
2. Transformer gets a dependency list from ConfigValidation module (CVL). The CVL builds dependency list from the SONiC YANG model.
3. Transformer deletes dependency through DB access.
4. DB access finally deletes entries in Redis DB.
  
### 1.2.1.1 YANG model and port configuration dependency

Few examples in the Sonic YANG model to show port dependencies.

```  
        container VLAN_MEMBER_TABLE {
            config false;
 
            list VLAN_MEMBER_TABLE_LIST {
                key "name ifname";
 
                leaf name {
                    type leafref {
                        path "../../../VLAN_TABLE/VLAN_TABLE_LIST/name";
                    }
                }
 
                leaf ifname {
                    type leafref {
                        path "/prt:sonic-port/prt:PORT/prt:PORT_LIST/prt:ifname";
                    }
                }
                leaf tagging_mode {
                    type scommon:tagging_mode;
                }
            }
        }
 
        container PORTCHANNEL_MEMBER {
 
            list PORTCHANNEL_MEMBER_LIST {
                key "name ifname";
 
                leaf name {
                    type leafref {
                        path "../../../PORTCHANNEL/PORTCHANNEL_LIST/name";
                    }
                }
 
                leaf ifname {
                    type leafref {
                        path "/prt:sonic-port/prt:PORT/prt:PORT_LIST/prt:ifname";
                    }
                }
            }
        }
 
 
 
        container ACL_TABLE {
 
            list ACL_TABLE_LIST {
                key "aclname";
                max-elements 1024; // Max 1K ACL tables for all platforms
 
                leaf aclname {
                    type string {
                        pattern '[a-zA-Z0-9]{1}([-a-zA-Z0-9_]{0,71})';
                        length 1..72;
                    }
                }
 
                leaf policy_desc {
                    type string {
                        length 1..255 {
                            error-app-tag policy-desc-invalid-length;
                        }
                    }
                }
 
                leaf stage {
                    type enumeration {
                        enum INGRESS;
                        enum EGRESS;
                    }
                }
 
                leaf type {
                    sonic-ext:custom-validation ValidateLeafConstant;
                    type enumeration {
                        enum MIRROR;
                        enum L2;
                        enum L3;
                        enum L3V6;
                        enum TAM;
                    }
                }
 
                leaf-list ports {
                    type union {
                        type leafref {
                            path "/sprt:sonic-port/sprt:PORT/sprt:PORT_LIST/sprt:ifname";
                        }
                        type leafref {
                            path "/spc:sonic-portchannel/spc:PORTCHANNEL/spc:PORTCHANNEL_LIST/spc:name";
                        }
                        type leafref {
                            path "/svlan:sonic-vlan/svlan:VLAN/svlan:VLAN_LIST/svlan:name";
                        }
                        type string {
                            pattern "Switch";
                        }
                    }
                }
 
                must "((/cmn:operation/cmn:operation = 'DELETE') and (count(ports) = 0)) or ((/cmn:operation/cmn:operation != 'DELETE'))" {
                    error-message "ACL actively applied to ports.";
                }
            }
        }
 ```
The CVL API "GetDepDataForDelete("PORT|Ethernet7")" will be able to get all the dependent keys for the given port keys. E.g.
 
 ```
        "VLAN_MEMBER" : map[string]interface{} {
            "Vlan21|Ethernet7": map[string] interface{} {
                "tagging_mode":   "tagged",
            },
            "Vlan22|Ethernet7": map[string] interface{} {
                "tagging_mode":   "tagged",
            },
        },
        "PORTCHANNEL_MEMBER" : map[string]interface{} {
            "Ch47|Ethernet7": map[string] interface{} {
                "NULL": "NULL",
            },
        },
        "ACL_TABLE" : map[string]interface{} {
            "TestACL1": map[string] interface{} {
                "stage": "INGRESS",
                "type": "L3",
                "ports@": "Ethernet7,Ethernet9",
            },
        }
 ```

  

The CVL API "<font color='blue'>
GetDepDataForDelete("PORT|Ethernet7")</font>" will be able to get all the dependent keys for the given port keys. E.g.

  
 ```
        "VLAN_MEMBER" : map[string]interface{} {
            "Vlan21|Ethernet7": map[string] interface{} {
                "tagging_mode":   "tagged",
            },
            "Vlan22|Ethernet7": map[string] interface{} {
                "tagging_mode":   "tagged",
            },
        },
        "PORTCHANNEL_MEMBER" : map[string]interface{} {
            "Ch47|Ethernet7": map[string] interface{} {
                "NULL": "NULL",
            },
        },
        "ACL_TABLE" : map[string]interface{} {
            "TestACL1": map[string] interface{} {
                "stage": "INGRESS",
                "type": "L3",
                "ports@": "Ethernet7,Ethernet9",
            },
        }
``` 

In the above keys fetched by CVL, VLAN member table and PORTCHANNEL member table have reference to the port in the keys itself. Transformer infrastructure will be able to delete these keys automatically (no need for special handling). In the ACL table the reference is in the value part of the table.  Transformer will handle the value part as well. 

In the table below, where the key is a leaflist and has multiple references to the port table, during the deletion of Ethernet52 the entire key cannot be deleted but instead the key needs to be updated to have Ethernet52 removed from it and have only "Ethernet50,Ethernet54,Ethernet56". This is difficult to do in the common transformer infrastructure code and needs to be handled by special overloaded functions (custom handling).
  
 ```
{
"PORT_QOS_MAP": {
    "Ethernet50,Ethernet52,Ethernet54,Ethernet56": {
        "tc_to_pg_map": "[TC_TO_PRIORITY_GROUP_MAP|AZURE]",
        "tc_to_queue_map": "[TC_TO_QUEUE_MAP|AZURE]",
        "pfc_enable": "3,4",
        "pfc_to_queue_map": "[MAP_PFC_PRIORITY_TO_QUEUE|AZURE]",
        "dscp_to_tc_map": "[DSCP_TO_TC_MAP|AZURE]"
    }
  }
} 
```

Whether a feature needs special handling or not depends on the schema. Feature team needs to get it reviewed with the management team if it does not fall into a simple case.

Also there is one more point to be noted here. In the previous example we had VLAN member, port channel member and ACL table as dependent configs of port table.

For example, let us assume there is one more configuration (config1) which is dependent on VLAN members. This config1 has to be deleted first before deleting the VLAN member. This will also be automatically taken care of by the transformer.

But even these recursive dependent configurations should be looked into and made sure that there is no special schema like the one in port_qos_map or a more complex one.

Also the feature team needs to make sure we do not have circular dependencies. Eg: in the above example config1 depends on VLAN member and VLAN member depends on port. Now if the port depends on config1 this will result in circular dependencies and transformer infrastructure will not be able to resolve this. It is one of the limitations of transformer infrastructure.

Dependent configuration should be specified through leafref in sonic YANG. The "must" and "when" expression conditions specified in the SONiC YANG will neither be dependent configurations nor be automatically handled during cascaded delete operation. If these "must" and "when" expressions need to be honored during cascaded delete, then the special handling  needs to be used and those conditions should be checked in it.

### 1.2.1.2 Basic Approach

Basic Flow 
 - Platform module within management infrastructure processes DPB request from user
 - Management infrastructure application gets all port dependent configuration and updates the CONFIG_DB for cleanup.
 - Back end applications receives config clean up triggers.
 - SWSS (portmgd) receives DPB requests and updates APP_DB.
 - SWSS (orchagent) updates ASIC_DB for port delete/create request after port dependent resource cleanup (e.g. MAC, VLAN etc).
 - Syncd (syncd) calls SAI api to carry the port operation.

  
### 1.2.2 Container

Front End - mgmt-framework
Back End - swss and syncd
Configuration  Cleanup -  application containers e.g BGP, LLDP etc  
  

### 1.2.3 SAI Overview

sai_port_api_t create_port and  remove_port.

# 2 Functionality

  

## 2.1 Target Deployment Use Cases

Allows user to have different port speed connectivity without service disruption on already deployed ports. User is allowed to create and destroy port objects. 

  

## 2.2 Functional Description  

  
Based on hardware platform various port speeds are supported. As part of DPB operation the port configuration will be cleaned up. The services on DPB involved ports will be down. Newly created ports will be ready for configuration after DPB operation is complete. New ports created will be in default state (admin down). The port speed change command is unchanged. The port and dependent configuration is unchanged in speed command where as in DPB port configuration will be deleted. In DPB port get deleted and created.
  

# 3 Design

  

  

## 3.1 Overview

  In community version,  DPB is supported only through CLICK management infrastructure. The SONiC YANG configuration dependency generation and cleanup are done through Python YANG (libYANG, pyang) tools. The dependency cleanup is directly applied to CONFIG_DB in controlled way.
  
In current release (Buzznik+) DPB is supported through KLISH management infrastructure only.  Due to some device restriction (master port to be deleted last) and DB update ordering challenges DPB being staged in back end.  To achieve synchronization between back and front end there are some additional CONFIG_DB tables and STATE_DB checks.  

  

## 3.2 DB Changes 

  
CONFIG_DB and STATE_DB being updated as part of DPB. Below is an example when default 100G port being broken into 4x25G. Here Ethernet0 is master port.
  

### 3.2.1 CONFIG DB

**BREAKOUT_CFG:**

127.0.0.1:6379[4]> HGETALL BREAKOUT_CFG|Ethernet0
"brkout_mode"
"4x10G"
"lanes"
"73,74,75,76"

**BREAKOUT_PORTS:**

127.0.0.1:6379[4]> HGETALL "BREAKOUT_PORTS|Ethernet0"
1) "master"
2) "Ethernet0"
127.0.0.1:6379[4]> HGETALL "BREAKOUT_PORTS|Ethernet1"
1) "master"
2) "Ethernet0"
127.0.0.1:6379[4]> HGETALL "BREAKOUT_PORTS|Ethernet2"
1) "master"
2) "Ethernet0"
127.0.0.1:6379[4]> HGETALL "BREAKOUT_PORTS|Ethernet3"
1) "master"
2) "Ethernet0" 

### 3.2.2 APP DB 

  None

### 3.2.3 STATE DB

**PORT_BREAKOUT:**

127.0.0.1:6379[6]> KEYS PORT_BREAKOUT|Ethernet0
1) "PORT_BREAKOUT|Ethernet0"
127.0.0.1:6379[6]> HGETALL PORT_BREAKOUT|Ethernet0
1) "status"
2) "Completed"
127.0.0.1:6379[6]>  
  

### 3.2.4 ASIC DB
None
  

### 3.2.5 COUNTER DB 

  None

 ### 3.2.6 QoS handling multiple interface limitation
 
-   QoS config via mgmt interfaces (REST/KLISH/gNMI)
        -   Modified sonic-yang model to prevent multiple interfaces
-   Loading QoS configs in config_db.json at init time or during "config reload"
	-   Added handling in db_migrator.py to break up multiple interfaces in QUEUE and PORT_QOS_MAP tables
	-   Allows backward compatibility with old config_db.json upgrading to Buzznik+ with DPB support

-   "config qos reload" to load platform specific buffer and qos defaults

	-   Common buffer and qos jinja2 templates were modified back in early Buzznik time frame by Microsoft
-   "config load" json file (sonic-cfggen)
	-   CVL yang model to prevent multiple interfaces can reject "config load". CVL for sonic-cfggen is not available today (future enhancement).
	-   Incremental config load will not be supported. Use "db_migrator.py" use case as an upgrade path for DPB support.    

## 3.3 Switch State Service Design

  

### 3.3.1 Orchestration Agent

  From DPB perspective orchagent handles new port creation and deletion based on lane map and maintains reference count for SAI port object. The reference count zero indicates resources are cleaned up.  

### 3.3.2 Other Process
  
Portmgr consumes CONFIG_DB port table, port break out table and produces APP_DB to carry out DPB operation. After DPB operation is successful updates STATE_DB port breakout table with completion status.   

## 3.4 SyncD

Refer community HLD.  

## 3.5 SAI
  
Added port create and delete API for all supported devices.  

  

## 3.6 User Interface

  

  

### 3.6.1 Data Models 

  

sonic-mgmt-common/models/yang/openconfig-platform.yang

  

### 3.6.2 CLI

#### 3.6.2.1 Configuration Commands

**interface breakout port <slot/port> mode \<option\>**
**no interface breakout port <slot/port>**

slot - for future use (fixed to 1 now for pizza box)
port - Front panel port number (cage number)

To breakout to 4x10G:

```
sonic-cli# configure terminal
sonic-cli(config)# interface breakout port 1/1 mode 4x10G
```

To undo breakout 4x25G (back to default mode, e.g. 1x100G):

```
sonic-cli# configure terminal
sonic-cli(config)# no interface breakout port 1/1
```

#### 3.6.2.2 Show Commands

To display broken out ports and status.

**show interface breakout port <slot/port>**
```
 show interface breakout port 1/1
```

To display port dependent configurations.

**show interface breakout dependencies port <slot/port>**

```
 show interface breakout dependencies port 1/1
```

#### 3.6.2.3 Debug Commands

  
No specific debug commands available.
  

#### 3.6.2.4 IS-CLI Compliance

  
NA
  

### 3.6.3 REST API Support

REST request to breakout port 1/1 to 4x25G:

```

PATCH /restconf/data/openconfig-platform:components/component=1/1/port/openconfig-platform-port:breakout-mode/config
data={"openconfig-platform-port:config": {"num-channels": 4, "channel-speed": "SPEED_25GB"}}

``` 

REST request to undo breakout port 1/1:

```

DELETE /restconf/data/openconfig-platform:components/component=1/1/port/openconfig-platform-port:breakout-mode/config

```

### 3.6.4 Service and Docker Management

  NA
  
# 4 Flow Diagrams 

 
mgmt-framework - front end processing 
swss - back end processing
syncd - device level handling 

  

# 5 Error Handling

In DPB there is no easy way to handle the error cases.  For now, in case of error need to recover using reboot. Use DPB show command to check the progress. We will update this section when error handling being added. 
  

# 6 Serviceability and Debug

  The extensive logging and DB state is provides information to debug DPB issues.  The following logs need to looked into to debug DPB issues.

mgmt-framework - DB update and user requests
DB - CONFIG_DB, APP_DB and STATE_DB port break out tables
portmgrd - DPB operations
orchagent - dependency removal,  look for reference count
syncd - SDK and SAI level operation status 
  

# 7 Warm Boot Support

  
There is no direct impact on warm-reboot. As port increases in the system, the dependencies and resources scales up. This will have impact on overall warm-boot time.


# 8 Scalability

 DPB allows to scale up total number of ports in the system.

  

# 9 Unit Test

  Tests can be performed on any platform.  The script will execute all possible break-out combination and provides result. 

  tests/ut/dpb/test_ut_dpb_infra.py

# 10 Internal Design Information

 None
