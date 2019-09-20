# SONiC Configuration Database Manual

Table of Contents
=================

   * [INTRODUCTION](#introduction)
   * [Configuration](#configuration)
   * [<strong>Config Load and Save</strong>](#config-load-and-save)  
         * [Incremental Configuration](#incremental-configuration)  
   * [<strong>Redis and Json Schema</strong>](#redis-and-json-schema)  
         * [ACL and Mirroring](#acl-and-mirroring)  
         * [BGP Sessions](#bgp-sessions)           
         * [Bidirectional Forwarding Detection (BFD)](#bfd)  
         * [BUFFER_PG](#buffer_pg)   
         * [Buffer pool](#buffer-pool)  
         * [Buffer profile](#buffer-profile)  
         * [Buffer queue](#buffer-queue)  
         * [Cable length](#cable-length)  
         * [COPP_TABLE](#copp_table)  
         * [CORE DUMP](#core-dump)  
         * [Export Service](#export-service)  
         * [CRM](#crm)  
         * [Data Plane L3 Interfaces](#data-plane-l3-interfaces)  
         * [Device Metadata](#device-metadata)   
         * [Device neighbor metada](#device-neighbor-metada)  
         * [DOT1P_TO_TC_MAP](#dot1p_to_tc_map)  
         * [DSCP_TO_TC_MAP](#dscp_to_tc_map)  
         * [FLEX_COUNTER_TABLE](#flex_counter_table)  
         * [L2 Neighbors](#l2-neighbors)  
         * [Loopback Interface](#loopback-interface)  
         * [Management Interface](#management-interface)  
         * [Management port](#management-port)  
         * [MAP_PFC_PRIORITY_TO_QUEUE](#map_pfc_priority_to_queue)  
         * [NTP and SYSLOG servers](#ntp-and-syslog-servers)  
         * [Port](#port)  
         * [Port Channel](#port-channel)  
         * [Portchannel member](#portchannel-member)  
         * [Port QoS Map](#port-qos-map)  
         * [Queue](#queue)  
         * [Scheduler](#scheduler)  
         * [Spanning Tree (PVST)](#stp-pvst)  
         * [Tacplus Server](#tacplus-server)  
         * [TC to Priority group map](#tc-to-priority-group-map)  
         * [TC to Queue map](#tc-to-queue-map)  
         * [Versions](#versions)  
         * [VLAN](#vlan)  
         * [VLAN_MEMBER](#vlan_member)  
         * [WRED_PROFILE](#wred_profile)
         * [NAT](#nat)
         * [Threshold](#threshold)
   * [For Developers](#for-developers)  
      * [Generating Application Config by Jinja2 Template](#generating-application-config-by-jinja2-template)  
      * [Incremental Configuration by Subscribing to ConfigDB](#incremental-configuration-by-subscribing-to-configdb)  

# Document History

| # | Date    |  Document Version | Details |
| --- | --- | --- | --- |
| 1 |  Sep 20, 2019 |v1 | Initial version |

# INTRODUCTION																																									
This document lists the configuration commands schema applied in the SONiC eco system. All these commands find relevance in collecting system information, analysis and even for trouble shooting. All the commands are categorized under relevant topics with corresponding examples.  																																																																					

# Configuration

SONiC is managing configuration in a single source of truth - a redisDB
instance that we refer as ConfigDB. Applications subscribe to ConfigDB
and generate their running configuration correspondingly.

(Before Sep 2017, we were using an XML file named minigraph.xml to
configure SONiC devices. For historical documentation, please refer to
[Configuration with
Minigraph](https://github.com/Azure/SONiC/wiki/Configuration-with-Minigraph-(~Sep-2017)))

# **Config Load and Save**

In current version of SONiC, ConfigDB is implemented as database 4 of
local redis. When system boots, configurations will be loaded from
/etc/sonic/config_db.json file into redis. Please note that ConfigDB
content won't be written back into /etc/sonic/config_db.json file
automatically. In order to do that, a config save command need to be
manually executed from CLI. Similarly, config load will trigger a force
load of json file into DB. Generally, content in
/etc/sonic/config_db.json can be considered as starting config, and
content in redisDB running config.

We keep a way to load configuration from minigraph and write into
ConfigDB for backward compatibility. To do that, run `config
load_minigraph`.

### Incremental Configuration

The design of ConfigDB supports incremental configuration - application
could subscribe to changes in ConfigDB and response correspondingly.
However, this feature is not implemented by all applications yet. By
 now, the only application that supports incremental configuration
is BGP (docker-fpm-quagga). For other applications, a manual restart is
required after configuration changes in ConfigDB.

# **Redis and Json Schema** 

ConfigDB uses a table-object schema that is similar with
[AppDB](https://github.com/Azure/sonic-swss/blob/4c56d23b9ff4940bdf576cf7c9e5aa77adcbbdcc/doc/swss-schema.md),
and `config_db.json` is a straight-forward serialization of DB. As an
example, the following fragments could be BGP-related configuration in
redis and json, correspondingly:


***Redis format***
```
127.0.0.1:6379[4]> keys BGP_NEIGHBOR:*

1) "BGP_NEIGHBOR:10.0.0.31"
2) "BGP_NEIGHBOR:10.0.0.39"
3) "BGP_NEIGHBOR:10.0.0.11"
4) "BGP_NEIGHBOR:10.0.0.7"

...

127.0.0.1:6379[4]> hgetall BGP_NEIGHBOR:10.0.0.3

1) "admin_status"
2) "up"
3) "peer_addr"
4) "10.0.0.2"
5) "asn"
6) "65200"
7) "name"
8) "ARISTA07T2"
```

***Json format***
```
"BGP_NEIGHBOR": {
	"10.0.0.57": {
		"rrclient": "0", 
		"name": "ARISTA01T1", 
		"local_addr": "10.0.0.56", 
		"nhopself": "0", 
		"holdtime": "10", 
		"asn": "64600", 
		"keepalive": "3"
	}, 
    "10.0.0.59": {
        "rrclient": "0", 
        "name": "ARISTA02T1", 
        "local_addr": "10.0.0.58", 
        "nhopself": "0", 
        "holdtime": "10", 
        "asn": "64600", 
        "keepalive": "3"
	},
}
```

Full sample config_db.json files are availables at
[here](https://github.com/Azure/SONiC/blob/gh-pages/doc/config_db.json)
and
[here](https://github.com/Azure/SONiC/blob/gh-pages/doc/config_db_t0.json).


### ACL and Mirroring

ACL and mirroring related configuration are defined in
**MIRROR_SESSION**, **ACL_TABLE** and **ACL_RULE** tables. Those
tables are in progress of migrating from APPDB. Please refer to their
schema in APPDB
[here](https://github.com/Azure/sonic-swss/blob/4c56d23b9ff4940bdf576cf7c9e5aa77adcbbdcc/doc/swss-schema.md)
and migration plan
[here](https://github.com/Azure/SONiC/wiki/ACL-Configuration-Requirement-Description).

```
{
"MIRROR_SESSION": {
        "everflow0": {
                "src_ip": "10.1.0.32",
                "dst_ip": "2.2.2.2"
        }
    },

"ACL_TABLE": {
        "DATAACL": {
                "policy_desc" : "data_acl",
                "type": "l3",
                "ports": [
                        "Ethernet0",
                        "Ethernet4",
                        "Ethernet8",
                        "Ethernet12"
                ]
        }
    }
}
```

```
{
"ACL_TABLE": {
        "aaa": {
                "type": "L3",
                "ports": "Ethernet0"
        }
   },
"ACL_RULE": {
        "aaa|rule_0": {
        "PRIORITY": "55",
        "PACKET_ACTION": "DROP",
        "L4_SRC_PORT": "0"
        },
        "aaa|rule_1": {
        "PRIORITY": "55",
        "PACKET_ACTION": "DROP",
        "L4_SRC_PORT": "1"
        }
   }
}
```

***Below ACL table added by comparig minigraph.xml & config_db.json***

Note below that REDIRECT clause requires {IP, Interface} instead of just {IP} as IP address alone is not sufficient to decide the egress interface in multi-VRF scenario.

```
{
"ACL_TABLE": {
		"EVERFLOW": {
		"type": "MIRROR",
		"policy_desc": "EVERFLOW",
		"ports": [
		  "PortChannel0001",
		  "PortChannel0002",
		  "PortChannel0003",
		  "PortChannel0004"
		]
	  },
		"EVERFLOWV6": {
        "type": "MIRRORV6",
        "policy_desc": "EVERFLOWV6",
        "ports": [
          "PortChannel0001",
          "PortChannel0002",
          "PortChannel0003",
          "PortChannel0004"
        ]
      },
        "SNMP_ACL": {
          "services": [
            "SNMP"
        ],
        "type": "CTRLPLANE",
        "policy_desc": "SNMP_ACL"
      },
        "SSH_ONLY": {
          "services": [
            "SSH"
          ],
          "type": "CTRLPLANE",
          "policy_desc": "SSH_ONLY"
      }
   },

"ACL_RULE": {
        "SNMP_ACL|DEFAULT_RULE": {
            "PRIORITY": "1",
            "PACKET_ACTION": "DROP",
            "ETHER_TYPE": "2048"
        },
        "SNMP_ACL|RULE_1": {
            "PRIORITY": "9999",
            "PACKET_ACTION": "ACCEPT",
            "SRC_IP": "1.1.1.1/32",
            "IP_PROTOCOL": "17"
        },
        "SNMP_ACL|RULE_2": {
            "PRIORITY": "9998",
            "PACKET_ACTION": "REDIRECT:20.1.1.93|Ethernet10",
            "SRC_IP": "2.2.2.2/32",
            "IP_PROTOCOL": "17"
        },
        "SSH_ONLY|DEFAULT_RULE": {
            "PRIORITY": "1",
            "PACKET_ACTION": "DROP",
            "ETHER_TYPE": "2048"
        },
        "SSH_ONLY|RULE_1": {
            "PRIORITY": "9999",
            "PACKET_ACTION": "REDIRECT:27.3.11.219|Ethernet92,65.31.46.161|Ethernet72",
            "SRC_IP": "4.4.4.4/8",
            "IP_PROTOCOL": "6"
        }
    }
}

```

### BGP Sessions

BGP session configuration is defined in **BGP_NEIGHBOR** table. BGP
neighbor address is used as key of bgp neighbor objects. Object
attributes include remote AS number, neighbor router name, and local
peering address. Dynamic neighbor is also supported by defining peer
group name and IP ranges in **BGP_PEER_RANGE** table.

```
{
"BGP_NEIGHBOR": {
        "10.0.0.61": {
                "local_addr": "10.0.0.60",
                "asn": 64015,
                "name": "ARISTA15T0"
        },
        "10.0.0.49": {
                "local_addr": "10.0.0.48",
                "asn": 64009,
                "name": "ARISTA09T0"
        },
 
        "10.0.0.63": {
                "rrclient": "0", 
				"name": "ARISTA04T1", 
				"local_addr": "10.0.0.62", 
				"nhopself": "0", 
				"holdtime": "10", 
				"asn": "64600", 
				"keepalive": "3"
        }
		
"BGP_PEER_RANGE": {
    "BGPSLBPassive": {
        "name": "BGPSLBPassive",
        "ip_range": [
            "10.250.0.0/27"
        ]
    },
    "BGPVac": {
        "name": "BGPVac",
        "ip_range": [
            "10.2.0.0/16"
        ]
    }
  }
}
```

### Bidirectional Forwarding Detection (BFD)

To enable BFD for a BGP neighbor, it can be configured in BGP_NEIGHBOR table as below:
```
{
"BGP_NEIGHBOR": {
	"10.0.0.61": {
		"local_addr": "10.0.0.60",
		"asn": 64015,
		"name": "ARISTA15T0",
		"bfd": 1
	}
}
```
BFD_PEER table can be used to configure BFD sessions for a peer IP address as below:

```
{
"BFD_PEER": {
	"2.2.2.2|null|null": {
		"transmit_interval":"400", 
		"receive_interval":"300", 
		"echo_mode":"true", 
		"echo_interval":"44", 
		"multiplier":"5",
		"label":"mylabel"
	}, 
	"3.3.3.3|1.1.1.1|null": {
		"admin_status":"down"
	},
	"4.4.4.4|null|Ethernet0": {
	},
	"5.5.5.5|1.1.1.1|Ethernet0": {
		"admin_status":"up"
	},
	"6.6.6.6|1.1.1.1|null|multihop": {
		"admin_status":"up"
	}
}
```

### BUFFER_PG

```
{
"BUFFER_PG": {
    "Ethernet0|3-4": {
        "profile": "[BUFFER_PROFILE|pg_lossless_40000_5m_profile]"
    },
    "Ethernet1|3-4": {
        "profile": "[BUFFER_PROFILE|pg_lossless_40000_5m_profile]"
    },
    "Ethernet2|3-4": {
        "profile": "[BUFFER_PROFILE|pg_lossless_40000_5m_profile]"
    }
  }
}

```

### Buffer pool

```
{
"BUFFER_POOL": {
    "egress_lossless_pool": {
        "type": "egress",
        "mode": "static",
        "size": "15982720"
    },
    "egress_lossy_pool": {
        "type": "egress",
        "mode": "dynamic",
        "size": "9243812"
    },
    "ingress_lossless_pool": {
        "xoff": "4194112",
        "type": "ingress",
        "mode": "dynamic",
        "size": "10875072"
    }
  }
}

```


### Buffer profile

```
{
"BUFFER_PROFILE": {
    "egress_lossless_profile": {
        "static_th": "3995680",
        "pool": "[BUFFER_POOL|egress_lossless_pool]",
        "size": "1518"
    },
    "egress_lossy_profile": {
        "dynamic_th": "3",
        "pool": "[BUFFER_POOL|egress_lossy_pool]",
        "size": "1518"
    },
    "ingress_lossy_profile": {
        "dynamic_th": "3",
        "pool": "[BUFFER_POOL|ingress_lossless_pool]",
        "size": "0"
    },
    "pg_lossless_40000_5m_profile": {
        "xon_offset": "2288",
        "dynamic_th": "-3",
        "xon": "2288",
        "xoff": "66560",
        "pool": "[BUFFER_POOL|ingress_lossless_pool]",
        "size": "1248"
    },
    "pg_lossless_40000_40m_profile": {
        "xon_offset": "2288",
        "dynamic_th": "-3",
        "xon": "2288",
        "xoff": "71552",
        "pool": "[BUFFER_POOL|ingress_lossless_pool]",
        "size": "1248"
    }
  }
}

```


### Buffer queue

```
{
"BUFFER_QUEUE": {
    "Ethernet50,Ethernet52,Ethernet54,Ethernet56|0-2": {
        "profile": "[BUFFER_PROFILE|egress_lossy_profile]"
    },
    "Ethernet50,Ethernet52,Ethernet54,Ethernet56|3-4": {
        "profile": "[BUFFER_PROFILE|egress_lossless_profile]"
    },
    "Ethernet50,Ethernet52,Ethernet54,Ethernet56|5-6": {
        "profile": "[BUFFER_PROFILE|egress_lossy_profile]"
    }
  }
}
 
```


### Cable length

```
{
"CABLE_LENGTH": {
    "AZURE": {
        "Ethernet8": "5m",
        "Ethernet9": "5m",
        "Ethernet2": "5m",
        "Ethernet58": "5m",
        "Ethernet59": "5m",
        "Ethernet50": "40m",
        "Ethernet51": "5m",
        "Ethernet52": "40m",
        "Ethernet53": "5m",
        "Ethernet54": "40m",
        "Ethernet55": "5m",
        "Ethernet56": "40m"
    }
  }
}

```

### COPP_TABLE

```
[
    {
        "COPP_TABLE:trap.group.bfd.lacp.udld.lldp": {
            "trap_ids": "bfd,bfdv6,lacp,udld,lldp",
            "trap_action":"trap",
            "trap_priority":"7",
            "queue": "7",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"6000",
            "cbs":"6000",
            "red_action":"drop"
        },
        "OP": "SET"
    },
    {
        "COPP_TABLE:trap.group.stp.pvrst": {
            "trap_ids": "stp,pvrst",
            "trap_action":"trap",
            "trap_priority":"6",
            "queue": "6",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"16000",
            "cbs":"16000",
            "red_action":"drop"
        },
        "OP": "SET"
    },
    {
        "COPP_TABLE:trap.group.vrrp": {
            "trap_ids": "vrrp",
            "trap_action":"trap",
            "trap_priority":"5",
            "queue": "5",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"6000",
            "cbs":"6000",
            "red_action":"drop"
        },
        "OP": "SET"
    },
    {
        "COPP_TABLE:trap.group.bgp": {
            "trap_ids": "bgp,bgpv6",
            "trap_action":"trap",
            "trap_priority":"4",
            "queue": "4",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"10000",
            "cbs":"10000",
            "red_action":"drop"
        },
        "OP": "SET"
    },
    {
        "COPP_TABLE:trap.group.arp": {
            "trap_ids": "arp_req,arp_resp,neigh_discovery",
            "trap_action":"copy",
            "trap_priority":"3",
            "queue": "3",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"6000",
            "cbs":"6000",
            "red_action":"drop"
        },
        "OP": "SET"
    },
    {
        "COPP_TABLE:trap.group.ip2me.dhcp": {
            "trap_ids": "ip2me,dhcp,dhcpv6",
            "trap_action":"trap",
            "trap_priority":"2",
            "queue": "2",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"6000",
            "cbs":"6000",
            "red_action":"drop"
        },
        "OP": "SET"
    },
    {
        "COPP_TABLE:trap.group.nat.l3mtu": {
            "trap_ids": "src_nat_miss,dest_nat_miss,l3_mtu_error",
            "trap_action":"trap",
            "trap_priority":"1",
            "queue": "1",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"600",
            "cbs":"600",
            "red_action":"drop"
         },
         "OP": "SET"
    },
    {
        "COPP_TABLE:default": {
            "queue": "0",
            "meter_type":"packets",
            "mode":"sr_tcm",
            "cir":"600",
            "cbs":"600",
            "red_action":"drop"
        },
        "OP": "SET"
    }
]
```



### CORE DUMP

```json
"COREDUMP": {
    "config": {
        "enabled": "true"
    }
}
```


### Export Service

```json
"EXPORT": {
    "export": {
        "config": "enable",
        "servername": "11.11.11.11",
        "destdir": "/techsupport",
        "username": "admin",
        "password": "kITEzO3OICpvMXEHMA==",
        "protocol": "scp",
        "interval": "30"
    }
}
```




### CRM

```
{
"CRM": {
    "Config": {
        "acl_table_threshold_type": "percentage",
        "nexthop_group_threshold_type": "percentage",
        "fdb_entry_high_threshold": "85",
        "acl_entry_threshold_type": "percentage",
        "ipv6_neighbor_low_threshold": "70",
        "nexthop_group_member_low_threshold": "70",
        "acl_group_high_threshold": "85",
        "ipv4_route_high_threshold": "85",
        "acl_counter_high_threshold": "85",
        "ipv4_route_low_threshold": "70",
        "ipv4_route_threshold_type": "percentage",
        "ipv4_neighbor_low_threshold": "70",
        "acl_group_threshold_type": "percentage",
        "ipv4_nexthop_high_threshold": "85",
        "ipv6_route_threshold_type": "percentage"
    }
  }
}

```

### Data Plane L3 Interfaces

IP configuration for data plane are defined in **INTERFACE**,
**PORTCHANNEL_INTERFACE**, **VLAN_INTERFACE** and **LOOPBACK_INTERFACE** table. The objects
in all four tables have the interface (could be physical port, port
channel, vlan, or loopback) that IP address is attached to as first-level key, and
IP prefix as second-level key. IP interface objects have VRF name as an
attribute. In the example below, Ethernet0 belongs to Vrf-Edge, Ethernet8 belongs to Vrf-Core and Ethernet4 belongs to default VRF. For every interface bound to the default VRF, an entry is required with empty VRF name like "Vlan1000":{}.

```
{
"INTERFACE": {
        "Ethernet0":{
                "vrf_name":"Vrf-Edge"
        },
        "Ethernet4":{}, // it means this interface belongs to global/default VRF. It is necessary even user doesnt use vrf.
        "Ethernet8":{
                "vrf_name":"Vrf-Core"
        },        
        "Ethernet0|10.0.0.0/31": {},
        "Ethernet4|10.0.0.2/31": {},
        "Ethernet8|10.0.0.4/31": {}
		...
    },
	
"PORTCHANNEL_INTERFACE": {
        "PortChannel01":{},
        "PortChannel02":{
                "vrf_name":"Vrf-Red"
        },
        "PortChannel01|10.0.0.56/31": {},
        "PortChannel01|FC00::71/126": {},
        "PortChannel02|10.0.0.58/31": {},
        "PortChannel02|FC00::75/126": {}
		...
    },
"VLAN_INTERFACE": {
        "Vlan1000":{},
        "Vlan1000|192.168.0.1/27": {}
    },
    
"LOOPBACK_INTERFACE":{
        "Loopback0":{
                "vrf_name":"Vrf-yellow"
        },
        "Loopback23": {},
        "Loopback64": {},
        "Loopback129": {},
        "Loopback531": {},
        "Loopback0|14.14.14.1/32":{},
        "Loopback23|23.45.67.89/32": {},
        "Loopback531|192.168.131.217/32": {}
    },
}
```


### Device Metadata

The **DEVICE_METADATA** table contains only one object named
*localhost*. In this table the device metadata such as hostname, hwsku,
deployment envionment id and deployment type are specified. BGP local AS
number is also specified in this table as current only single BGP
instance is supported in SONiC.

```
{
"DEVICE_METADATA": {
        "localhost": {
        "hwsku": "Force10-S6100",
        "default_bgp_status": "up",
        "docker_routing_config_mode": "unified",
        "hostname": "sonic-s6100-01",
        "platform": "x86_64-dell_s6100_c2538-r0",
        "mac": "4c:76:25:f4:70:82",
        "default_pfcwd_status": "disable",
        "bgp_asn": "65100",
        "deployment_id": "1",
        "type": "ToRRouter"
    }
  }
}

```


### Device neighbor metada

```
{
"DEVICE_NEIGHBOR_METADATA": {
    "ARISTA01T1": {
        "lo_addr": "None",
        "mgmt_addr": "10.11.150.45",
        "hwsku": "Arista-VM",
        "type": "LeafRouter"
    },
    "ARISTA02T1": {
        "lo_addr": "None",
        "mgmt_addr": "10.11.150.46",
        "hwsku": "Arista-VM",
        "type": "LeafRouter"
    }
  }
}

```

### DOT1P_TO_TC_MAP
```
{ 
"DOT1P_TO_TC_MAP": { 
    "AZURE": { 
        "0":"1", 
        "1":"0", 
        "2":"0", 
        "3":"3", 
        "4":"4", 
        "5":"0", 
        "6":"0", 
        "7":"0" 
    } 
  }
}

```

### DSCP_TO_TC_MAP
```
{
"DSCP_TO_TC_MAP": {
    "AZURE": {
        "1": "1",
        "0": "1",
        "3": "3",
        "2": "1",
        "5": "2",
        "4": "4",
        "7": "1",
        "6": "1",
        "9": "1",
        "8": "0"
    }
  }
}

```

### FLEX_COUNTER_TABLE

```
{
"FLEX_COUNTER_TABLE": {
    "PFCWD": {
        "FLEX_COUNTER_STATUS": "enable"
    },
    "PORT": {
        "FLEX_COUNTER_STATUS": "enable"
    },
    "QUEUE": {
        "FLEX_COUNTER_STATUS": "enable"
    }
  }
}

```


### L2 Neighbors

The L2 neighbor and connection information can be configured in
**DEVICE_NEIGHBOR** table. Those information are used mainly for LLDP.
While mandatory fields include neighbor name acting as object key and
remote port / local port information in attributes, optional information
about neighbor device such as device type, hwsku, management address and
loopback address can also be defined.

```
{
"DEVICE_NEIGHBOR": {
        "ARISTA04T1": {
                "mgmt_addr": "10.20.0.163",
                "hwsku": "Arista",
                "lo_addr": null,
                "local_port": "Ethernet124",
                "type": "LeafRouter",
                "port": "Ethernet1"
        },
        "ARISTA03T1": {
                "mgmt_addr": "10.20.0.162",
                "hwsku": "Arista",
                "lo_addr": null,
                "local_port": "Ethernet120",
                "type": "LeafRouter",
                "port": "Ethernet1"
        },
        "ARISTA02T1": {
                "mgmt_addr": "10.20.0.161",
                "hwsku": "Arista",
                "lo_addr": null,
                "local_port": "Ethernet116",
                "type": "LeafRouter",
                "port": "Ethernet1"
        },
        "ARISTA01T1": {
                "mgmt_addr": "10.20.0.160",
                "hwsku": "Arista",
                "lo_addr": null,
                "local_port": "Ethernet112",
                "type": "LeafRouter",
                "port": "Ethernet1"
        }
    }
}
```

### Loopback Interface

Loopback interface configuration lies in **LOOPBACK_INTERFACE** table
and has similar schema with data plane interfaces. The loopback device
name and loopback IP prefix act as multi-level key for loopback
interface objects.

```
{
"LOOPBACK_INTERFACE": {
        "Loopback0|10.1.0.32/32": {},
        "Loopback0|FC00:1::32/128": {}
  }
}

```

### Management Interface

Management interfaces are defined in **MGMT_INTERFACE** table. Object
key is composed of management interface name and IP prefix. Attribute
***gwaddr*** specify the gateway address of the prefix.
***forced_mgmt_routes*** attribute can be used to specify addresses /
prefixes traffic to which are forced to go through management network
instead of data network.

```
{
"MGMT_INTERFACE": {
        "eth0|10.11.150.11/16": {
        "gwaddr": "10.11.0.1"
    },
    "eth0|FC00:2::32/64": {
        "forced_mgmt_routes": [
            "10.0.0.100/31",
            "10.250.0.8",
                "10.255.0.0/28"
        ],
        "gwaddr": "fc00:2::1"
    }
  }
}

```

### Management port

```
{
"MGMT_PORT": {
    "eth0": {
        "alias": "eth0",
        "admin_status": "up"
    }
  }
}
 
```

### MAP_PFC_PRIORITY_TO_QUEUE

```
{
"MAP_PFC_PRIORITY_TO_QUEUE": {
    "AZURE": {
        "1": "1",
        "0": "0",
        "3": "3",
        "2": "2",
        "5": "5",
        "4": "4",
        "7": "7",
        "6": "6"
    }
  }
}
```


### NTP and SYSLOG servers

These information are configured in individual tables. Domain name or IP
address of the server is used as object key. Currently there are no
attributes in those objects.

***NTP server***
```
{
"NTP_SERVER": {
        "2.debian.pool.ntp.org": {},
        "1.debian.pool.ntp.org": {},
        "3.debian.pool.ntp.org": {},
        "0.debian.pool.ntp.org": {}
    },

"NTP_SERVER": {
    "23.92.29.245": {},
    "204.2.134.164": {}
    }
}
```

***Syslogserver***
```
{
"SYSLOG_SERVER": {
    "10.0.0.5": {},
    "10.0.0.6": {},
    "10.11.150.5": {}
    }
}
```

### Port

In this table the physical port configurations are defined. Each object
will have port name as its key, and port name alias and port speed as
optional attributes.

```
{
"PORT": {
        "Ethernet0": {
            "index": "0",
            "lanes": "101,102",
            "description": "fortyGigE1/1/1",
            "mtu": "9100",
            "alias": "fortyGigE1/1/1",
            "speed": "40000"
        },
        "Ethernet1": {
            "index": "1",
            "lanes": "103,104",
            "description": "fortyGigE1/1/2",
            "mtu": "9100",
            "alias": "fortyGigE1/1/2",
            "admin_status": "up",
            "speed": "40000"
        },
		"Ethernet63": {
            "index": "63",
            "lanes": "87,88",
            "description": "fortyGigE1/4/16",
            "mtu": "9100",
            "alias": "fortyGigE1/4/16",
            "speed": "40000"
        }
    }
}

```

### Port Channel

Port channels are defined in **PORTCHANNEL** table with port channel
name as object key and member list as attribute.

```
{
"PORTCHANNEL": {
        "PortChannel0003": {
                "admin_status": "up",
        "min_links": "1",
        "members": [
            "Ethernet54"
        ],
        "mtu": "9100"
    },
    "PortChannel0004": {
        "admin_status": "up",
        "min_links": "1",
        "members": [
            "Ethernet56"
        ],
        "mtu": "9100"
    }
  }
}
```


### Portchannel member

```
{
"PORTCHANNEL_MEMBER": {
    "PortChannel0001|Ethernet50": {}, 
    "PortChannel0002|Ethernet52": {}, 
    "PortChannel0003|Ethernet54": {}, 
    "PortChannel0004|Ethernet56": {}
  }
}
```

### Port QoS Map

```
{
"PORT_QOS_MAP": {
    "Ethernet50,Ethernet52,Ethernet54,Ethernet56": {
        "tc_to_pg_map": "[TC_TO_PRIORITY_GROUP_MAP|AZURE]", 
        "tc_to_queue_map": "[TC_TO_QUEUE_MAP|AZURE]", 
        "pfc_enable": "3,4", 
        "pfc_to_queue_map": "[MAP_PFC_PRIORITY_TO_QUEUE|AZURE]", 
        "dscp_to_tc_map": "[DSCP_TO_TC_MAP|AZURE]",
        "dot1p_to_tc_map": "[DOT1P_TO_TC_MAP|AZURE]"
    }
  }
}  
```

### Queue
```
{
"QUEUE": {
	"Ethernet56|4": {
        "wred_profile": "[WRED_PROFILE|AZURE_LOSSLESS]", 
        "scheduler": "[SCHEDULER|scheduler.1]"
    }, 
    "Ethernet56|5": {
        "scheduler": "[SCHEDULER|scheduler.0]"
    }, 
    "Ethernet56|6": {
        "scheduler": "[SCHEDULER|scheduler.0]"
    }
  }
}
```

### Scheduler

A default scheduler profile with the following behavior is applied to all physical port queues.
- Queue 7 is set to strict priority.
- Queues 0-6 are round robin (WRR with weight of 1)

CPU Tx is configured to send to internal UC queue 9 with strict priority scheduling above all data queues 0-7 for devices that support 10 physical port UC queues. Otherwise, CPU Tx is sent to queue 7. CPU Tx queue is not user configurable.

```
{
"SCHEDULER": {
    "scheduler.0": {
        "type": "STRICT"
    }, 
    "scheduler.1": {
        "type": "WRR"
        "weight": "1"
    }
  }
}
```

### Spanning Tree (PVST+)

Spanning Tree can be configured Globally, per VLAN, per interface and per VLAN-interface as below:
```
"STP":
{
    "GLOBAL": {
        "forward_delay": "15", 
        "hello_time": "2", 
        "max_age": "20", 
        "mode": "pvst", 
        "priority": "32768", 
        "rootguard_timeout": "30"
    }
}

"STP_INTF":
{
    "Ethernet0": {
        "bpdu_guard": "false", 
        "bpdu_guard_do_disable": "false", 
        "enabled": "true", 
        "portfast": "true", 
        "root_guard": "false", 
        "uplink_fast": "false"
    }, 
    "PortChannel100": {
        "bpdu_guard": "false", 
        "bpdu_guard_do_disable": "false", 
        "enabled": "true", 
        "portfast": "true", 
        "root_guard": "false", 
        "uplink_fast": "false"
    }
}

"STP_VLAN":
{
    "Vlan100": {
        "enabled": "true", 
        "forward_delay": "15", 
        "hello_time": "2", 
        "max_age": "20", 
        "priority": "32768"
    },
    "Vlan200": {
        "enabled": "true", 
        "forward_delay": "15", 
        "hello_time": "2", 
        "max_age": "20", 
        "priority": "32768"
    }
}

"STP_VLAN_INTF":
{
    "Vlan100|Ethernet0": {
        "path_cost": "100", 
        "priority": "10"
    }, 
    "Vlan200|PortChannel100": {
        "path_cost": "100", 
        "priority": "10"
    }
}
```

### Tacplus Server

```
{
"TACPLUS_SERVER": {
    "10.0.0.8": {
        "priority": "1", 
        "tcp_port": "49"
    }, 
    "10.0.0.9": {
        "priority": "1", 
        "tcp_port": "49"
    }
  }
}
```


### TC to Priority group map

```
{
"TC_TO_PRIORITY_GROUP_MAP": {
    "AZURE": {
        "1": "1", 
        "0": "0", 
        "3": "3", 
        "2": "2", 
        "5": "5", 
        "4": "4", 
        "7": "7", 
        "6": "6"
    }
  }
}  
```

### TC to Queue map

```
{
"TC_TO_QUEUE_MAP": {
    "AZURE": {
        "1": "1", 
        "0": "0", 
        "3": "3", 
        "2": "2", 
        "5": "5", 
        "4": "4", 
        "7": "7", 
        "6": "6"
    }
  }
}  
```

### Versions

This table is where the curret version of the software is recorded.
```
{
    "VERSIONS": {
        "DATABASE": {
            "VERSION": "version_1_0_1"
        }
    }
}
```

### VLAN

This table is where VLANs are defined. VLAN name is used as object key,
and member list as well as an integer id are defined as attributes. If a
DHCP relay is required for this VLAN, a dhcp_servers attribute must be
specified for that VLAN, the value of which is a list that must contain
the domain name or IP address of one or more DHCP servers.

```
{
"VLAN": {
	"Vlan1000": {
		"dhcp_servers": [
			"192.0.0.1",
			"192.0.0.2",
			"192.0.0.3",
			"192.0.0.4"
		],
		"members": [
			"Ethernet0",
			"Ethernet4",
			"Ethernet8",
			"Ethernet12"
		],
		"vlanid": "1000"
	}
  }
}
```

### VLAN_MEMBER

VLAN member table has Vlan name together with physical port or port
channel name as object key, and tagging mode as attributes.

```
{
"VLAN_MEMBER": {
	"Vlan1000|PortChannel47": {
		"tagging_mode": "untagged"
	},
	"Vlan1000|Ethernet8": {
		"tagging_mode": "untagged"
	},
	"Vlan2000|PortChannel47": {
		"tagging_mode": "tagged"
	}
  }
}
```

### VRF

The VRF table allows to insert or update a new virtual router
instance. The key of the instance is VRF name. The attributes in the
table allow to change properties of a VRF. Attributes:

- 'fallback' contains boolean value 'true' or 'false'. This knob dictates the forwarding behavior when route lookup fails in an user VRF. This attribute is not yet supported.

```
{
         "VRF": {
                "Vrf-Core": {
                        "fallback": "false"
                },
                "Vrf-Green": {
                        "fallback": "false"
                },
                "Vrf-Yellow": {
                        "fallback": "false"
                }
        }
}
```

### VRRP

The VRRP table allows to insert or update a VRRP instance entry.
The key of the entry is {<interface_name>, <vrid>}. Interface name could be any Layer-3 interface in systems, like Ethernet12, Vlan43, PortChannel37. The attributes in the
table allow to change properties of a VRRP instance. Attributes:

- 'adv_interval' contains integer value between 1 and 255. Default is 1 and the unit is in seconds. This attributes decides how frequently VRRP hellos will be sent out.
- 'priority' conatins an integer value between 1 and 254. Default is 100. This attribute control the election of Master VRRP node for an instance. Highest priority node becomes the master.
- 'pre_empt' contains boolean value 'true' or 'false'. Default is 'true'. This attribute controls if a Master node can be premepted when a higher priority VRRP node comes online.
- 'state' conatins a string to denote the current state of VRRP instance. Currently this is not used.
- 'version' contains an integer value to denote the VRRP version. Currently only Version 2 is supported and hence this attribute is always 2.
- 'vip' contains a list of IP address in dotted decimal format. This attribute is virtual IP address for a given VRRP instance
- 'vrid' is an integer and specifies the VRID of the associated VRRP instance.
- 'track_interface' is a list of tuples {<interface_name>, weight, <weight>}. This attribute enables user to configure track interface for a VRRP instance.

```
{    
    "VRRP": {
        "Vlan23|37": {
            "adv_interval": "1",
            "pre_empt": "True",
            "priority": "140",
            "state": "",
            "track_interface": "Ethernet7|weight|10,PortChannel17|weight|10,",
            "version": "2",
            "vip": "4.1.1.100,",
            "vrid": "37"
        },
        "PortChannel29|64": {
            "adv_interval": "1",
            "pre_empt": "True",
            "priority": "80",
            "state": "",
            "track_interface": "",
            "version": "2",
            "vip": "4.1.2.100,",
            "vrid": "64"
        },
}
```

### WRED_PROFILE

```
{
"WRED_PROFILE": {
    "AZURE_LOSSLESS": {
        "red_max_threshold": "2097152", 
        "wred_green_enable": "true", 
        "ecn": "ecn_all", 
        "green_min_threshold": "1048576", 
        "red_min_threshold": "1048576", 
        "wred_yellow_enable": "true", 
        "yellow_min_threshold": "1048576", 
        "green_max_threshold": "2097152", 
        "green_drop_probability": "5", 
        "yellow_max_threshold": "2097152", 
        "wred_red_enable": "true", 
        "yellow_drop_probability": "5", 
        "red_drop_probability": "5"
    }
  }
}
```
### NAT

NAT configuration is defined in **NAT_GLOBAL**, **NAT_POOL**, **NAT_BINDINGS**, **STATIC_NAT**, **STATIC_NAPT** tables. The NAT zone configuration is part of **INTERFACE** table.

```
{
    "NAT_GLOBAL": {
        "Values": {
            "admin_mode": "enabled",
            "nat_tcp_timeout": "6000",
            "nat_udp_timeout": "300"
        }
    },
    "NAT_POOL": {
        "nat1": {
            "nat_ip": "2.0.0.5",
            "nat_port": "10-200"
        }
    },
    "NAT_BINDINGS": {
        "bind1": {
            "access_list": "",
            "nat_pool": "nat1"
        }
    },
    "STATIC_NAT": {
        "65.54.0.1": {
            "local_ip": "10.0.0.1"
        },
        "112.0.0.1": {
            "local_ip": "111.0.0.2",
            "nat_type": "dnat",
            "twice_nat_id": "1"
        },
        "112.0.0.2": {
            "local_ip": "111.0.0.3",
            "nat_type": "snat",
            "twice_nat_id": "1"
        }
    },
    "STATIC_NAPT": {
        "112.0.0.1|UDP|250": {
            "local_ip": "10.0.0.1",
            "local_port": "111"
        }
    },
    "INTERFACE": {
        "Ethernet0": {
            "nat_zone": "1"
        },
        "Ethernet2": {
            "nat_zone": "2"
        },
    },
}
```

### Threshold

Threshold configuration is defined in **THRESHOLD\_TABLE** table. The key for a **THRESHOLD\_TABLE** entry is {buffer, buffer\_type, port\_alias, buffer\_index}. The attributes are {threshold: value in percentage}.

```
{
    "THRESHOLD_TABLE": {
        "priority-group|headroom|Ethernet0|7": {
            "threshold": "40"
        },
        "priority-group|shared|Ethernet8|7": {
            "threshold": "2"
        },
        "queue|multicast|Ethernet16|7": {
            "threshold": "20"
        },
        "queue|unicast|Ethernet12|0": {
            "threshold": "10"
        }
    }
}
```


For Developers
==============

Generating Application Config by Jinja2 Template
------------------------------------------------

To be added.

Incremental Configuration by Subscribing to ConfigDB
----------------------------------------------------

Detail instruction to be added. A sample could be found in this
[PR](https://github.com/Azure/sonic-buildimage/pull/861) that
implemented dynamic configuration for BGP.

