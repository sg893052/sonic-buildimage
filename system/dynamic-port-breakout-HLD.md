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
| 0.6 | 06/21/2020 | Vishnu Shetty | Update CLI output and review comments |
| 0.7 | 09/11/2020 | Vishnu Shetty | Update CLI, migration and design details |
| 0.8 | 09/14/2020 | Vishnu Shetty | Add flow diagram |
| 0.9 | 09/15/2020 | Vishnu Shetty | Add REST API and examples |
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
| SPB | Static Port Breakout |

# 1 Feature Overview

Ports can be broken out to different sub-ports and speeds with subset of lanes in most hardware today. In SONiC Buzznik or older release, the port breakouts are only made possible by hard-coding the hardware profiles that are loaded at boot-up time. If a different port breakout config is desired, a new image must be loaded or at least services need to be restarted which impacts the services. The required hardware profiles either achieved through spinning off different HWSKU or using static breakout (SPB).  The DPB feature in Buzznik+ will address these limitations, ie

- No system or config reload required.
- No traffic impact on other ports which are not subject to DPB, and
- Users have option to choose a different break out modes.

A high-level view of the operation:
1. DPB request is initiated through SONiC management interface (KLISH).
2. Management framework infrastructure derives port dependencies from the SONiC YANG model, and updates the CONFIG DB. (e.g. VLAN member removal followed by port delete).
3. Based on the CONFIG_DB update (ports delete followed by create), Port Manager (back end application) cleanups dependent resources (configuration and dynamic dependencies) and deletes ports. Port manager will then create new ports in default state. Here, "back end" primarily refers to SWSS and syncd components (orchagent, SAI etc). System has other dependency as well like protocols, platform services like xcvrd, led, pmon, etc. For some of the components these config cleanup triggers are "no op".

This document is complementary to the community DPB HLD. Link to the community HLD is [here](https://github.com/Azure/SONiC/blob/439944c120d59edf7f62ed28a902b1a3c07a35f8/doc/dynamic-port-breakout/sonic-dynamic-port-breakout-HLD.md). Please refer design section for major difference with community.

## 1.1 Requirements

High Level Requirements:

- SONiC to support port breakout dynamically. User should be able to change port breakout mode at run time without affecting the traffic on other ports. The traffic on the remaining ports of the switch should not be disrupted.
  
- Configuration dependencies and respective resources of the ports that are subject to DPB should be removed automatically. User should be able to query configuration dependencies on these ports. 

- After the breakout, the newly created ports shall be initialized with default port configurations. e.g., admin down, default MMU buffer, etc.

The management framework generates port configuration dependencies and automatically deletes those dependencies in the CONFIG_DB. e.g., portchannel member remove followed by a port deletion. The SONiC YANG model is used to generate configuration dependencies.  Please refer to management framework design section and additional external documentations for details.
    

### 1.1.1 Functional (detail) Requirements

- Only allow valid break-out modes. This is achieved by a platform-specific file "platform.json". It contains port capabilities, lane mapping, alias naming, speed and mode constraints. Refer design section for an example.
- Users should be able to migrate from a previous Broadcom sonic release. 
- If a given platform does not have the platform.json file, the DPB request shall error out. The system falls back to non-DPB release behavior (in such cases "port_config.ini" used).
- All of the configuration dependency checking logic for a about to delete port is implemented in the SONiC Management Framework. In order for this logic to work correctly, the following must be true.
	- All of the affected configuration is in the CONFIG_DB.
	- A matching SONiC YANG file is available.
	- Where necessary (complex cases only), custom override functions are provided to delete the configuration.
- For the respective User Interface (UI)
	- The above is always expected to be true to the features supported in the Management Framework (including FRR features).
	- For FRR, configuration applied directly (through either vtysh or *.conf startup files in split mode) is not present in the CONFIG_DB, and so will not be cleaned up by a DPB operation. However, configuration applied to FRR through the Management framework is correctly cleaned up in FRR after a DPB operation. In reality this mean that vyhsh can be used for show but not configuration before and after a DPB operation.
- To support automatic configuration cleanup SONiC YANG model adoption is a must.
- The back-end components, CONFIG_DB listeners (majority of SWSS components, protocols) act on DB updates and clean-up all configurations. This intern cleanup run time dependencies and resources.
- DPB interface (REST or CLI) is supported in platform open-config YANG. DPB is an update request.
- DPB operation result in port deletion and creation events. The ports will be shutdown in the back-end before deletion. New ports created as part of "create" operation will be in the default state. The user need to take care of new desired configuration and any topology changes.  
- DBP configuration dependency checking is implemented in the SONiC Management Framework in broadcom-sonic builds. Community builds have a separate mechanism that is not used in Broadcom builds. However both methods depend upon SONiC YANG files, and this is required to be common between the two design. 
- "config reload" functionality shall remain available to users. Users shall be able to apply port breakout configuration in config DB. This is another method of loading DPB configuration. It is not dynamic, config reload will restart the services.
- No custom MMU settings are applied as part of DPB.

- When upgrading from a prior release into the new release supporting DPB:
     1. All HWSKUs that are defined in prior images must be supported in the new image (platform.json).
     2. All HWSKUs are upgraded into the same exact HWSKU after upgrade into the new image. 
     3. After upgrade, it is advised to migrate to **base** HWSKU to avoid managing multiple HWSKUs in future. The same CONFIG_DB can be used with minor changes (change in hwsku name field).
     4. There are two classes of HWSKU, one is base (or default) and other one is derived from the base. Later one is called non-default HWSKU. The non-default HWSKU require two additional files ("platform_hwsku.json" and "hwsku.json") to achieve exact set of ports as defined by virtue of HWSKU and to migrate to DPB enabled release. These two files are used in upgrade to achieve DPB during boot up.
     5. In general downgrading from an image supporting DPB to an older non-DPB image is not supported. If the port set matches that of HWSKU default (same as in non-DPB release) downgrade is allowed. During the downgrade to detect mismatch in port set, check is added to flag the error. User can always downgrade by deleting DPB config or using default configuration profiles.

### 1.1.2 Scalability Requirements

Port breakout are supported on set of ports defined in platform.json file. The platform.json file is prepared by keeping number of platform and device restriction (number of logical port, ports per pipe etc). This may limit number of break-out capable ports.The multiple breakout commands can be (one command per port) requested together. This will be buffered and executed one by one in back end. Each break out command is expensive operation (with default config takes 1 to 1.5 sec). The additional configuration on the port will take more time to complete breakout operation.

### 1.1.3 Warm Boot Requirements

No change in warm-boot behavior. DPB capable port should support warm-boot feature.

## 1.2 Design Overview

## 1.2.1 DPB Management Framework

![alt text](images/management-framework.png)

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
 - SWSS (orchagent) updates ASIC_DB for port delete/create request after port dependent resource cleanup is over (e.g. MAC, VLAN etc).
 - Syncd (syncd) calls SAI api to carry the port operation.


### 1.2.2 Container

Front End - mgmt-framework
Back End - swss and syncd
Configuration  Cleanup -  All application containers e.g BGP, LLDP etc  

### 1.2.3 SAI Overview

sai_port_api_t create_port and  remove_port.

# 2 Functionality


## 2.1 Target Deployment Use Cases

- The DPB on Broadcom SONiC version is supported only through management framework (REST and CLI).  The CLICK "config reload" works with DPB configuration, "config load" is not supported.  
- After DPB request, when port delete and create is in progress, any configuration on these ports should be avoided. During this period REST/CLI will respond with an error. The DPB status can be queried via CLI.

## 2.2 Functional Description

Based on hardware platform various port speeds are supported. 
- As part of DPB operation the port configuration will be cleaned up. The services on DPB involved ports will be down. Newly created ports will be ready for configuration after DPB operation is complete. New ports created will be in default state (admin down). 
- The port speed change command is unchanged. The port and dependent configuration is unchanged in speed command where as in DPB the port configuration will be deleted.  

# 3 Design  

## 3.1 Overview (community comparison)

  In community version, DPB is supported only through CLICK infrastructure. The SONiC YANG configuration dependency generation and cleanup are done through Python YANG (libYANG, pyang) tools. The dependency cleanup is directly applied to CONFIG_DB in controlled way. The CLICK DPB commands blocks CLI till DPB operation is complete. This is to make sure not accept any additional configuration during DPB. This way community achieved DPB in synchronous way.
      
In Broadcom version (Buzznik+) DPB is supported only in management infrastructure.  Due to some device restriction (master port to be deleted last), asynchronous config DB update from management infrastructure and DB update ordering challenges across the back end application,  DPB operation being staged in back end port manager.  To achieve synchronization between back and front end there are some additional CONFIG_DB and STATE_DB tables.   

## 3.2 DB Changes 


CONFIG_DB and STATE_DB being updated as part of DPB. Below is an example when default 100G port being broken into 4x10G. Here Ethernet48 is master port.


### 3.2.1 CONFIG DB

**BREAKOUT_CFG:**

    "BREAKOUT_CFG": {
        "Ethernet48": {
            "brkout_mode": "4x10G", 
            "lanes": "41,42,43,44", 
            "port": "1/49"
        }   
    }

**BREAKOUT_PORTS:**

    "BREAKOUT_PORTS": {
        "Ethernet48": {
            "master": "Ethernet48"
        }, 
        "Ethernet49": {
            "master": "Ethernet48"
        }, 
        "Ethernet50": {
            "master": "Ethernet48"
        }, 
        "Ethernet51": {
            "master": "Ethernet48"
        }
    }

### 3.2.2 APP DB 

  None

### 3.2.3 STATE DB

**PORT_BREAKOUT:**
	
	"PORT_BREAKOUT|Ethernet48": {
	"type": "hash", 
	"value": {
	  "phase": "created", 
	  "status": "Completed"
	}
	"PORT_BREAKOUT|Ethernet49": {
	"type": "hash", 
	"value": {
	  "phase": "created"
	}	
	"PORT_BREAKOUT|Ethernet50": {
	"type": "hash", 
	"value": {
	  "phase": "created"
	}
	"PORT_BREAKOUT|Ethernet51": {
	"type": "hash", 
	"value": {
	  "phase": "created"
	}


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

/**
 * @brief Create port
 *
 * @param[out] port_id Port id
 * @param[in] switch_id Switch id
 * @param[in] attr_count Number of attributes
 * @param[in] attr_list Array of attributes
 *
 * @return #SAI_STATUS_SUCCESS on success, failure status code on error
 */
typedef sai_status_t (*sai_create_port_fn)(
        _Out_ sai_object_id_t *port_id,
        _In_ sai_object_id_t switch_id,
        _In_ uint32_t attr_count,
        _In_ const sai_attribute_t *attr_list);

/**
 * @brief Remove port
 *
 * @param[in] port_id Port id
 *
 * @return #SAI_STATUS_SUCCESS on success, failure status code on error
 */
typedef sai_status_t (*sai_remove_port_fn)(
        _In_ sai_object_id_t port_id);


## 3.6 User Interface

  

### 3.6.1 Data Models  

YANG: OC Yang
sonic-mgmt-common/models/yang/openconfig-platform.yang

JSON: platform.json

    "Ethernet0": {
        "alias_at_lanes": "Eth1/1/1,Eth1/1/2,Eth1/1/3,Eth1/1/4,Eth1/1/5,Eth1/1/6,Eth1/1/7,Eth1/1/8",
        "breakout_modes": "1x400G,2x200G[100G],4x100G[50G]",
        "default_brkout_mode": "1x400G",
        "index": "1,1,1,1,1,1,1,1",
        "lanes": "33,34,35,36,37,38,39,40"
    }

JSON: platform_hwsku.json

    "Ethernet0": {
        "alias_at_lanes": "Eth1/1/1",
        "breakout_modes": "1x100G[50G]",
        "default_brkout_mode": "1x100G[50G]",
        "index": "1",
        "lanes": "33,34"
    },
    "Ethernet2": {
        "alias_at_lanes": "Eth1/1/2",
        "breakout_modes": "1x100G[50G]",
        "default_brkout_mode": "1x100G[50G]",
        "index": "1",
        "lanes": "35,36"
    },
    "Ethernet4": {
        "alias_at_lanes": "Eth1/1/3",
        "breakout_modes": "1x100G[50G]",
        "default_brkout_mode": "1x100G[50G]",
        "index": "1",
        "lanes": "37,38"
    },
    "Ethernet6": {
        "alias_at_lanes": "Eth1/1/4",
        "breakout_modes": "1x100G[50G]",
        "default_brkout_mode": "1x100G[50G]",
        "index": "1",
        "lanes": "39,40"
    }

JSON: hwsku.json

    "BREAKOUT_CFG": {
        "Ethernet0": {
            "brkout_mode": "4x100G", 
            "lanes": "33,34,35,36,37,38,39,40", 
            "port": "1/1"
       } 
    
    "BREAKOUT_PORTS": {
        "Ethernet0": {
            "master": "Ethernet0"
        }, 
        "Ethernet2": {
            "master": "Ethernet0"
        }, 
        "Ethernet4": {
            "master": "Ethernet0"
        }, 
        "Ethernet6": {
            "master": "Ethernet0"
        }

### 3.6.2 CLI

#### 3.6.2.1 Configuration Commands

**interface breakout port <slot/port> mode \<option\>**
**no interface breakout port <slot/port>**

slot - for future use (fixed to 1 now for rack unit/pizza box)
port - Front panel port number (cage number)

To breakout to 4x25G:

```
sonic-cli# configure terminal
sonic-cli(config)# interface breakout port 1/49 mode 4x25G
```

To undo breakout 4x25G (back to default mode, e.g. 1x100G):

```
sonic-cli# configure terminal
sonic-cli(config)# no interface breakout port 1/49
```

#### 3.6.2.2 Show Commands

To display broken out ports and status.

**show interface breakout port <slot/port>**
```
sonic# show interface breakout port 1/49
-----------------------------------------------
Port  Breakout Mode  Status        Interfaces          
-----------------------------------------------
1/49   4x25G          Completed    Ethernet48          
                                   Ethernet49          
                                   Ethernet50          
                                   Ethernet51          
sonic#
```
To display breakout mode.

**show interface breakout port <slot/port>**
```
sonic# show interface breakout modes 
-------------------------------------------------------------------------
Port  Interface   Supported Modes                           Default Mode
-------------------------------------------------------------------------
1/49  Ethernet48  1x100G, 1x40G, 4x25G, 4x10G               1x100G
1/56  Ethernet76  1x100G, 1x40G, 4x25G, 4x10G               1x100G

sonic(config)# interface-naming standard
sonic# show interface breakout modes 
-------------------------------------------------------------------------
Port  Interface   Supported Modes                           Default Mode
-------------------------------------------------------------------------
1/49  Eth1/49     1x100G, 1x40G, 4x25G, 4x10G               1x100G
1/56  Eth1/56     1x100G, 1x40G, 4x25G, 4x10G               1x100G

```
To display port dependent configurations.

**show interface breakout dependencies port <slot/port>**

```
sonic# show interface breakout dependencies port 1/49
----------------------------------
Dependent Configurations 
----------------------------------
VLAN|Vlan10         
VLAN_MEMBER|Vlan10|Ethernet49
sonic#

```

#### 3.6.2.3 Debug Commands


No specific debug commands available.


#### 3.6.2.4 IS-CLI Compliance


NA


### 3.6.3 REST API Support

**The REST request for configuring breakout out:**

PUT /restconf/data/openconfig-platform:components/component

curl -X PUT "https://10.59.142.114/restconf/data/openconfig-platform:components/component" -H "accept: */*" -H "Authorization: Basic YWRtaW46YnJvYWRjb20=" -H "Content-Type: application/yang-data+json" -d "{\"openconfig-platform:component\":[{\"name\":\"1/1\",\"port\":{\"openconfig-platform-port:breakout-mode\":{\"config\":{\"num-channels\":4,\"channel-speed\":\"SPEED_10GB\"}}}}]}"

Body:
{
  "openconfig-platform:component": [
    {
      "name": "1/1",
      "port": {
        "openconfig-platform-port:breakout-mode": {
          "config": {
            "num-channels": 4,
            "channel-speed": "SPEED_10GB"
          }
        }
      }
    }
  ]
}

**The REST request to get the breakout config and state:**

GET /restconf/data/openconfig-platform:components/component=1%2F1/port/openconfig-platform-port:breakout-mode

curl -X GET "https://10.59.142.114/restconf/data/openconfig-platform:components/component=1%2F1/port/openconfig-platform-port:breakout-mode" -H "accept: application/yang-data+json" -H "Authorization: Basic YWRtaW46YnJvYWRjb20="

Output:
{
  "openconfig-platform-port:breakout-mode": {
    "config": {
      "channel-speed": "openconfig-if-ethernet:SPEED_10GB",
      "num-channels": 4
    },
    "state": {
      "openconfig-port-breakout-ext:members": [
        "Ethernet0",
        "Ethernet1",
        "Ethernet2",
        "Ethernet3"
      ],
      "openconfig-port-breakout-ext:status": "Completed"
    }
  }
}

**GET at platform component level:**

GET /restconf/data/openconfig-platform:components/component=1/1

curl -X GET "https://10.59.142.114/restconf/data/openconfig-platform:components/component=1%2F1" -H "accept: application/yang-data+json" -H "Authorization: Basic YWRtaW46YnJvYWRjb20="

Output:
{
  "openconfig-platform:component": [
    {
      "name": "1/1",
     "port": {
        "openconfig-platform-port:breakout-mode": {
          "config": {
            "channel-speed": "openconfig-if-ethernet:SPEED_10GB",
            "num-channels": 4
          },
          "state": {
            "openconfig-port-breakout-ext:members": [
              "Ethernet0",
              "Ethernet1",
              "Ethernet2",
              "Ethernet3"
            ],
            "openconfig-port-breakout-ext:status": "Completed"
          }
        }
      }
    }
  ]
}

**The REST request to get the breakout configuration:**

GET /restconf/data/openconfig-platform:components/component=1/1/port/openconfig-platform-port:breakout-mode/config

curl -X GET "https://10.59.142.114/restconf/data/openconfig-platform:components/component=1%2F1/port/openconfig-platform-port:breakout-mode/config" -H "accept: application/yang-data+json" -H "Authorization: Basic YWRtaW46YnJvYWRjb20="

Output:
{
  "openconfig-platform-port:config": {
    "channel-speed": "openconfig-if-ethernet:SPEED_10GB",
    "num-channels": 4
  }
}

**The REST request to get the state of breakout configurations:**

GET /restconf/data/openconfig-platform:components/component=1/1/port/openconfig-platform-port:breakout-mode/state

curl -X GET "https://10.59.142.114/restconf/data/openconfig-platform:components/component=1%2F1/port/openconfig-platform-port:breakout-mode/state" -H "accept: application/yang-data+json" -H "Authorization: Basic YWRtaW46YnJvYWRjb20="

Output:
{
  "openconfig-platform-port:state": {
    "openconfig-port-breakout-ext:members": [
      "Ethernet0",
      "Ethernet1",
      "Ethernet2",
      "Ethernet3"
    ],
    "openconfig-port-breakout-ext:status": "Completed"
  }
}

### 3.6.4 Service and Docker Management

  NA

# 4 Flow Diagrams
![alt text](images/dpb-flow.png)

 DPB Sequence:

 1. User: requests DPB command (REST or CLI).
 2. MgmtInfra: issues config delete (port dependent config cleanup).
 3. MgmtInfra: updates PORT, BREAKOUT_CFG, BREAKOUT_PORTS config tables (port entry gets created in CONFIG_DB).
 4. PortMgr: listens to PORT and BREAKOUT config table updates.
 5. PortMgr: receives delete port and sets "In-progress" state for master port in STATE_DB. 
 6. PortMgr: after all update received from config DB (master/sub ports), starts breakout.
 7. PortMgr: deletes APP_DB port entry, this intern deletes port HOST interface. At this stage syncd/SAI dependencies cleaned up, port object reference count zero. 
 8. orchagent: host interface delete results in kernel netlink event. swss/portsyncd deletes port entry in STATE_DB (netlink async event).
 9. PortMgr: waits for port delete completion in hardware (ASIC_DB). port config cleanup is happening in parallel (step 2).
 10. PortMgr: adds port to APP_DB.
 11. orchagent: creates port HOST interface results in netlink event. swss/portsyncd creates port entry in STATE_DB (netlink async event).
 12. PortMgr: sets DPB state to "complete" for master port in STATE_DB.


# 5 Error Handling

In DPB there is no easy way to handle the error cases.  For now, in case of error need to recover using reboot. Use DPB show command to check the progress.   

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

**Summary of tests:**

 - Performs all breakout combination by parsing platform.json.  
 - Tries invalid mode - negative test. 
 - On two node setup breaks out ports and validates the traffic. 
 - After breakout , does warm-boot and fast-boot.

# 10 Internal Design Information

 None

