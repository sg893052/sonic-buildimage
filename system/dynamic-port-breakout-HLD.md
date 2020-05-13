
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

# About this Manual

This document provides dynamic port break-out feature content.

# Scope

This document captures dynamic port break-out requirements and provides design overview.

# Definition/Abbreviation

### Table 1: Abbreviations
  
| Term | Meaning |
| ---- | ---- |
| CONFIG_DB | SONiC configuration database in Redis |
| CVL | Config Validation |
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
 2. All HW SKUs are upgraded into the same exact HW SKU after upgrade into the new image.
 3. No modifications allowed to ports that are already broken out by the virtue of HW SKU. For example, in case of DellEMC-Z9332f-C64O16SH (16 x (4x100G) + 16 x 400G), lower 64 ports remain to be 100G. The upper 16 ports are 400G, but the user may use DPB to break-out any of the upper 16 ports to 100G. The user may later undo breakout on these upper 16 ports. But the lower 16 front-panel ports will remain static at 16 x (4x100G).
 4. Customers can (but don't have to) change HW SKU before upgrade, if deployment plans have changed. (Requires customers to change configuration - a maintenance activity)
 5. Warm reboot may be used for software upgrade (assuming no other underlying issue)
- When downgrading from an image supporting DPB to an older image that DPB is not supported:
	 1. User needs to remove all "new" configuration (features not supported in the old image) including DPB, before initiating a downgrade.
	 2. The configuration must match the contents of the port-config.ini file that will be used by the downgrade image.

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

**TBD
  

  

### 1.2.2 Container

**TBD. 
**Which container(s) will the code go into? Will there be new container(s)?
**Identify the dependencies on other containers in terms of starting order, restart dependencies, etc.

  

  

  

### 1.2.3 SAI Overview

  
**TBD. 
**Do we expect to need a new SAI API specification? If so, provide rough semantics (information flow across the API)?
**If using existing SAI APIs, then which area?
**For non-hardware features this section will be N/A

# 2 Functionality

  

  

## 2.1 Target Deployment Use Cases

  

  

**TBD. 
**Wordy description, with diagrams if possible

  

  

## 2.2 Functional Description

  

  
**TBD.
**Wordy description

  

  

  

# 3 Design

  

  

## 3.1 Overview

  In community version,  DPB is supported only through CLICK management infrastructure. The SONiC YANG configuration dependency generation and cleanup are done through Python YANG (libYANG, pyang) tools. The dependency cleanup is directly applied to CONFIG_DB in controlled way.

  
**TBD
**big picture view of the actors involved.

  

  

## 3.2 DB Changes

  

  
**TBD
**Describe changes to existing DBs or any new DB being added.

  

  

### 3.2.1 CONFIG DB

#### 3.2.1.1 QoS handling multiple interface limitation
 
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
  

### 3.2.2 APP DB

  

  

### 3.2.3 STATE DB

  

  

### 3.2.4 ASIC DB

  

  

### 3.2.5 COUNTER DB

  

  

  

## 3.3 Switch State Service Design

  

  

### 3.3.1 Orchestration Agent

  

  

### 3.3.2 Other Process

  

  
**TBD
**Describe changes to other process within SwSS if applicable.

  

  

  

## 3.4 SyncD

  

  
**TBD
**Describe changes to syncd if applicable.

  

  

  

## 3.5 SAI

  

  
**TBD
**Describe [new/existing] SAI APIs used by this feature.

  

  

  

## 3.6 User Interface

  

  

### 3.6.1 Data Models

  

  

**TBD
**Can be reference to YANG if applicable. Also cover gNMI here.

  

  

  

### 3.6.2 CLI



#### 3.6.2.1 Configuration Commands

After break-out, new ports get created with consecutive number. The original port is termed as the base port.

To breakout to 4x10G:

```

sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet 8
sonic-cli(conf-if-Ethernet8)# breakout 4x10G
Breakout mode change will result into dependent configuration clean-up and causes traffic disruption. Continue? [y/N]:N 

```

To breakout to 4x25G:

```

sonic-cli# configure terminal
sonic-cli(config)# interface Ethernet 20
sonic-cli(conf-if-Ethernet20)# breakout 4x25G
Breakout mode change will result into dependent configuration clean-up and causes traffic disruption. Continue? [y/N]:N 

```
 

To revert back the breakout mode:

```

sonic-cli(conf-if-Ethernet20)# no breakout
Breakout mode change will result into dependent configuration clean-up and causes traffic disruption. Continue? [y/N]:y 

```

More configuration commands:

```

sonic-cli(conf-if-Ethernet32)# breakout 2x50G 
Breakout mode change will result into dependent configuration clean-up and causes traffic disruption. Continue? [y/N]:y
%Error: operation failed 

sonic-cli(conf-if-Ethernet32)# interface Ethernet 33 
%Error: Interface Ethernet33 cannot be configured.

```

#### 3.6.2.2 Show Commands

To display port dependencies

```
 show interface breakout dependencies Ethernet 0
```

To display port speeds (port Ethernet52 is breakout to 4x10G, ports Ethernet56, Ethernet60, etc remain as 100G).

```

sonic-cli(config)# interface breakout Ethernet 52 4x10G
sonic-cli(config)# do show interface status
------------------------------------------------------------------------------------------
Name                Description         Admin          Oper           Speed          MTU            
------------------------------------------------------------------------------------------
<snip>
Ethernet52          -                   down           down           10000          9100          
Ethernet53          -                   down           down           10000          9100          
Ethernet54          -                   down           down           10000          9100          
Ethernet55          -                   down           down           10000          9100          
Ethernet56          -                   down           down           100000         9100          
Ethernet60          -                   down           down           100000         9100          
Ethernet64          -                   down           down           100000         9100          
Ethernet68          -                   down           down           100000         9100          
<snip>

```

#### 3.6.2.3 Debug Commands

  

  

#### 3.6.2.4 IS-CLI Compliance

  

**TBD

The following table maps SONIC CLI commands to corresponding IS-CLI commands. The compliance column identifies how the command comply to the IS-CLI syntax:

  

  

  

- **IS-CLI drop-in replace** ? meaning that it follows exactly the format of a pre-existing IS-CLI command.

  

  

- **IS-CLI-like** ? meaning that the exact format of the IS-CLI command could not be followed, but the command is similar to other commands for IS-CLI (e.g. IS-CLI may not offer the exact option, but the command can be positioned is a similar manner as others for the related feature).

  

  

- **SONIC** - meaning that no IS-CLI-like command could be found, so the command is derived specifically for SONIC.

  

  

  

|CLI Command|Compliance|IS-CLI Command (if applicable)| Link to the web site identifying the IS-CLI command (if applicable)|

  

  

|:---:|:-----------:|:------------------:|-----------------------------------|

  

  

| | | | |

  

  

| | | | |

  

  

| | | | |

  

  

| | | | |

  

  

| | | | |

  

  

| | | | |

  

  

| | | | |

  

  

  

**Deviations from IS-CLI:** If there is a deviation from IS-CLI, Please state the reason(s).

  

  

  

### 3.6.3 REST API Support

REST request to breakout Ethernet20 to 4x25G:

```

PATCH /restconf/data/openconfig-platform:components/component=Ethernet20/port/openconfig-platform-port:breakout-mode/config
data={"openconfig-platform-port:config": {"num-channels": 4, "channel-speed": "SPEED_25GB"}}

``` 

REST request to undo breakout Ethernet20:

```

DELETE /restconf/data/openconfig-platform:components/component=Ethernet20/port/openconfig-platform-port:breakout-mode/config

```

### 3.6.4 Service and Docker Management

  

  

**TBD


If a new service and/or a new docker is introduced, please follow the guidelines below:

  

  

- Identify the dependencies on other services. This includes the starting order, restart dependencies on other services, etc. Please take the multiple images into consideration where this feature can be included/excluded.

  

  

- If a new docker is introduced, identify all the processes in this docker and their starting order, and specify restartability needs and dependencies.

  

  

  

# 4 Flow Diagrams

  

  
**TBD

**Provide flow diagrams for inter-container and intra-container interactions.

  

  

  

# 5 Error Handling

  

**TBD 

**Provide details about incorporating error handling feature into the design and functionality of this feature.

  

  

  

# 6 Serviceability and Debug

  

  

**TBD
**Logging, counters, stats, trace considerations. Please make sure you have incorporated the debugging framework feature. e.g., ensure your code registers with the debugging framework and add your dump routines for any debug info you want to be collected.

  

  

  

# 7 Warm Boot Support

  

  
**TBD
Describe expected behavior and any limitation.

  

  

  

# 8 Scalability

  

  
**TBD
Describe key scaling factor and considerations.

  

  

  

# 9 Unit Test

  

  
**TBD
List unit test cases added for this feature including warm boot.

  

  

  

# 10 Internal Design Information

  

  
**TBD
Internal BRCM information to be removed before sharing with the community.


