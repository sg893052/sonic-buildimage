![](images/Broadcom.png)

<br>
<br>
<br>
# Broadcom SONiC 3.0.0 
### Configuration Database Manual
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

Table of Contents
=================

   * [INTRODUCTION](#introduction)
   * [Configuration](#configuration)
   * [<strong>Config Load and Save</strong>](#config-load-and-save)  
         * [Incremental Configuration](#incremental-configuration)  
   * [<strong>Redis and Json Schema</strong>](#redis-and-json-schema)  
         * [ACL and Mirroring](#acl-and-mirroring)  
         * [BGP Sessions](#bgp-sessions)  
         * [BGP Error Handling](#bgp-error-handling)  
         * [Bidirectional Forwarding Detection (BFD)](#bfd)  
         * [BUFFER_PG](#buffer_pg)   
         * [Buffer pool](#buffer-pool)  
         * [Buffer profile](#buffer-profile)  
         * [Buffer queue](#buffer-queue)  
         * [Cable length](#cable-length)  
         * [COPP_TABLE](#copp_table)  
         * [CORE DUMP](#core-dump)  
         * [EVPN_NVO](#evpn_nvo)  
         * [Export Service](#export-service)  
         * [CRM](#crm)  
         * [Data Plane L3 Interfaces](#data-plane-l3-interfaces)  
         * [Device Metadata](#device-metadata)   
         * [Device neighbor metada](#device-neighbor-metada)  
         * [DHCP Relay](#dhcp-relay)
         * [DOT1P_TO_TC_MAP](#dot1p_to_tc_map)  
         * [DSCP_TO_TC_MAP](#dscp_to_tc_map)  
         * [FLEX_COUNTER_TABLE](#flex_counter_table)  
         * [IP Helper](#ip-helper)  
         * [IPv4 Unnumbered Interface](#ipv4-unnumbered-interface)  
         * [KDUMP](#kdump)  
         * [L2 Neighbors](#l2-neighbors)  
         * [Loopback Interface](#loopback-interface)  
         * [Management Interface](#management-interface)  
         * [Management port](#management-port)  
         * [MAP_PFC_PRIORITY_TO_QUEUE](#map_pfc_priority_to_queue)  
         * [MCLAG](#mclag)
         * [NAT](#nat)  
         * [NTP and SYSLOG servers](#ntp-and-syslog-servers)  
         * [Port](#port)  
         * [Port Channel](#port-channel)  
         * [Portchannel member](#portchannel-member)  
         * [Port Mirroring](#port-mirroring)  
         * [Port QoS Map](#port-qos-map)  
         * [Queue](#queue)  
         * [Scheduler](#scheduler)  
         * [Shaping (port and queue)](#shaping)  
         * [SNMP CLI](#snmp-cli)  
         * [Spanning Tree (PVST)](#stp-pvst)  
         * [Spanning Tree (RPVST)](#stp-rpvst)  
         * [Tacplus Server](#tacplus-server)  
         * [TC to Priority group map](#tc-to-priority-group-map)  
         * [TC to Queue map](#tc-to-queue-map)  
         * [UDLD](#udld)  
         * [Versions](#versions)  
         * [VLAN](#vlan)  
         * [VLAN_MEMBER](#vlan_member)  
         * [VXLAN_TUNNEL](#vxlan_tunnel)  
         * [VXLAN_TUNNEL_MAP](#vxlan_tunnel_map)  
         * [WRED_PROFILE](#wred_profile)    
         * [Threshold](#threshold)  
         * [Syslog Server](#syslog-server)  
         * [IPv6 Link-local](#ipv6-link-local)  
         * [IFA](#ifa)  
         * [PTP](#ptp)  
         * [sFlow](#sflow)  
         * [BUM Storm Control](#BUM-Storm-Control)  
         * [IGMP Snooping](#igmp-snooping)  
         * [Drop Monitor](#drop-monitor)  
         * [Tail Timestamping](#tail-timestamping)  
   * [For Developers](#for-developers)  
      * [Generating Application Config by Jinja2 Template](#generating-application-config-by-jinja2-template)  
      * [Incremental Configuration by Subscribing to ConfigDB](#incremental-configuration-by-subscribing-to-configdb)  

# Document History

| #    | Date         | Document Version | Details                      |
| ---- | ------------ | ---------------- | ---------------------------- |
| 1    | Sep 20, 2019 | v1               | Initial version              |
| 2    | Nov 22, 2019 | v2               | Broadcom SONiC 2.1.0 Release |
| 3    | Feb 16, 2020 | v3               | Broadcom SONiC 3.0.0 Release |

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

### ACL_TABLE

Stores information about ACL tables on the switch.

```
key           = ACL_TABLE:name            ; acl_table_name must be unique
;field        = value
policy_desc   = 1*255VCHAR                ; name of the ACL policy table description
stage         = "INGRESS"/"EGRESS"        ; stage of ACL lookup
type          = "l2"/"l3"/"l3v6"/"mirror" ; type of acl table, every type of
                                          ; table defines the match/action a
                                          ; specific set of match and actions.
ports         = [0-max_ports]*port_name   ; the ports to which this ACL
                                          ; table is applied, can be entry
                                          ; value annotations

;value annotations
max_ports     = 1*5DIGIT                ; number of ports supported on the chip
port_name     = 1*64VCHAR               ; name of the port, must be unique
                                        ; port name can be Ethernetxxx or
                                        ; PortChannelxxx, Vlanxxxx or Switch
```

### ACL_RULE_TABLE

Stores rules associated with a specific ACL table on the switch.

#### ACL_RULE_TABLE for type L3, L3v6 and Mirror

```
key: ACL_RULE_TABLE:table_name:rule_name   ; key of the rule entry in the table,
                                           ; seq is the order of the rules
                                           ; when the packet is filtered by the
                                           ; ACL "policy_name".
                                           ; A rule is always assocaited with a
                                           ; policy.

;field        = value
priority      = 1*3DIGIT  ; rule priority. Valid values range
                          ; could be platform dependent

packet_action = "forward"/"drop"      ; an action when the fields are matched
redirect_action = 1*255CHAR           ; redirect parameter
            ; This parameter defines a destination for redirected packets
            ; it could be:
            ; name of physical port.          Example: "Ethernet10"
            ; name of LAG port                Example: "PortChannel5"
            ; next-hop ip address (in global) Example: "10.0.0.1"
            ; next-hop ip address and vrf     Example: "10.0.0.2@Vrf2"
            ; next-hop ip address and ifname  Example: "10.0.0.3@Ethernet1"
            ; next-hop group set of next-hop  Example: "10.0.0.1,10.0.0.3@Ethernet1"

mirror_action = 1*255VCHAR                 ; refer to the mirror session (by default it will be ingress mirror action)
mirror_ingress_action = 1*255VCHAR         ; refer to the mirror session
mirror_egress_action = 1*255VCHAR          ; refer to the mirror session

ether_type    = h16                        ; Ethernet type field
ip_type       = ip_types                   ; options of the l2_protocol_type
                                           ; field.
ip_protocol   = h8                         ; options of the l3_protocol_type field

src_ip        = ipv4_prefix                ; options of the source ipv4
                                           ; address (and mask) field

dst_ip        = ipv4_prefix                ; options of the destination ipv4
                                           ; address (and mask) field

src_ipv6      = ipv6_prefix                ; options of the source ipv6
                                           ; address (and mask) field

dst_ipv6      = ipv6_prefix                ; options of the destination ipv6
                                           ; address (and mask) field

l4_src_port   = port_num                   ; source L4 port or the
l4_dst_port   = port_num                   ; destination L4 port

l4_src_port_range = port_num_L-port_num_H  ; source ports range of L4 ports field
l4_dst_port_range = port_num_L-port_num_H  ; destination ports range of L4 ports field

tcp_flags     = h8/h8                      ; TCP flags field and mask
DSCP          = dscp_val["/"dscp_val]  ; DSCP field. Valid range is 0-63
ICMP_TYPE     = h8/dec-octet         ; ICMP Type Value
ICMP_CODE     = h8/dec-octet         ; ICMP Code value
VLAN          = vlan_id              ; VLAN ID. Supported range is 1-4094. This is
                                     ; valid only if the ACL is applied to Port or
                                     ; LAG or Switch. For VLAN binding this will be
                                     ; ignored.
;value annotations
ip_types = any | ip | ipv4 | ipv4any | non_ipv4 | ipv6any | non_ipv6
port_num      = 1*5DIGIT   ; a number between 0 and 65535
port_num_L    = 1*5DIGIT   ; a number between 0 and 65535,
                           ; port_num_L < port_num_H
port_num_H    = 1*5DIGIT   ; a number between 0 and 65535,
                           ; port_num_L < port_num_H
ipv6_prefix   =                 6( h16 ":" ) ls32
   /                       "::" 5( h16 ":" ) ls32
   / [               h16 ] "::" 4( h16 ":" ) ls32
   / [ *1( h16 ":" ) h16 ] "::" 3( h16 ":" ) ls32
   / [ *2( h16 ":" ) h16 ] "::" 2( h16 ":" ) ls32
   / [ *3( h16 ":" ) h16 ] "::"    h16 ":"   ls32
   / [ *4( h16 ":" ) h16 ] "::"              ls32
   / [ *5( h16 ":" ) h16 ] "::"              h16
   / [ *6( h16 ":" ) h16 ] "::"
h8          = 1*2HEXDIG
h16         = 1*4HEXDIG
ls32        = ( h16 ":" h16 ) / IPv4address
ipv4_prefix = dec-octet "." dec-octet "." dec-octet "." dec-octet “/” %d1-32
dec-octet   = DIGIT                     ; 0-9
                / %x31-39 DIGIT         ; 10-99
                / "1" 2DIGIT            ; 100-199
                / "2" %x30-34 DIGIT     ; 200-249
dscp_val    = DIGIT / %x31-36 %x30-33
vlan_id      = %x31-39                     ; 1-9
               / %x31-39 DIGIT             ; 10-99
               / %x31-39 2DIGIT            ; 100-999
               / %x31-33 3DIGIT            ; 1000-3999
               / %x34 %x30 %x30-39 %x30-34 ; 4000 - 4094
```

#### ACL_RULE_TABLE for type L2

```
key = ACL_RULE:table_name:rule_name  ; key of the rule entry in the table,
                                     ; seq is the order of the rules
                                     ; when the packet is filtered by the
                                     ; ACL "policy_name".
                                     ; A rule is always assocaited with a
                                     ; policy.
;field        = value
PRIORITY      = acl_priority         ; rule priority. Supported range is 1-65535
PACKET_ACTION = "forward"/"drop"
redirect_action = 1*255CHAR           ; redirect parameter
            ; This parameter defines a destination for redirected packets
            ; it could be:
            ; name of physical port.          Example: "Ethernet10"
            ; name of LAG port                Example: "PortChannel5"


SRC_MAC       = mac_addr ["/" mac_mask ] ; Source MAC address
DST_MAC       = mac_addr ["/" mac_mask ] ; Destination MAC address
ETHER_TYPE    = ethtype                  ; Ethernet type field
PCP           = pcp_val [ "/"" pcp_val]  ; PCP Value in range of 0-7
DEI           = BIT ["/" BIT ]           ; DEI Value. 0 or 1.
VLAN          = vlan_id                  ; VLAN ID. Supported range is 1-4094.
                                         ; valid only if the ACL is applied to Port or
                                         ; LAG or Switch. For VLAN binding this will be
                                         ; ignored.
    
;value annotations
acl_priority = 1*4DIGIT / %x31-36 %x30-35 %x30-35 %x30-33 %x30-35
ethtype      = "0x"3*4HEXDIG ; Must be in range 1536 to 65535 in decimal
mac_addr     = 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG "-" 2HEXDIG 
               / 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG
vlan_id      = %x31-39                     ; 1-9
               / %x31-39 DIGIT             ; 10-99
               / %x31-39 2DIGIT            ; 100-999
               / %x31-33 3DIGIT            ; 1000-3999
               / %x34 %x30 %x30-39 %x30-34 ; 4000 - 4094
pcp_val      = %x30-37
```

#### Example

```
{
  "ACL_TABLE": {
    "l2_ACL_0_INGRESS": {
      "policy_desc": "Description of l2_ACL_0_INGRESS",
      "type": "l2",
      "stage": "INGRESS",
      "ports": [
        "Ethernet0"
      ]
    },
    "l3_ACL_0_INGRESS": {
      "policy_desc": "Description of l3_ACL_0_INGRESS",
      "type": "l3",
      "stage": "INGRESS",
      "ports": [
        "Ethernet4"
      ]
    },
    "l3v6_ACL_0_INGRESS": {
      "policy_desc": "Description of l3v6_ACL_0_INGRESS",
      "type": "l3v6",
      "stage": "INGRESS",
      "ports": [
        "Ethernet8"
      ]
    }    
  },
  "ACL_RULE": {
    "l2_ACL_0_INGRESS|l2_ACL_0_RULE_0": {
      "PRIORITY": "1",
      "PACKET_ACTION": "DROP",
      "SRC_MAC": "00-00-11-00-00-00",
      "DST_MAC": "00-00-22-00-00-00"
    },
    "l2_ACL_0_INGRESS|l2_ACL_0_RULE_1": {
      "PRIORITY": "2",
      "PACKET_ACTION": "FORWARD",
      "SRC_MAC": "00-00-11-00-00-01",
      "DST_MAC": "00-00-22-00-00-01"
    },
    "l2_ACL_0_INGRESS|l2_ACL_0_RULE_2": {
      "PRIORITY": "3",
      "PACKET_ACTION": "DROP",
      "SRC_MAC": "00-00-11-00-00-02",
      "DST_MAC": "00-00-22-00-00-02"
    },
    "l2_ACL_0_INGRESS|l2_ACL_0_RULE_3": {
      "PRIORITY": "4",
      "PACKET_ACTION": "FORWARD",
      "SRC_MAC": "00-00-11-00-00-03",
      "DST_MAC": "00-00-22-00-00-03"
    },
    "l3_ACL_0_INGRESS|l3_ACL_0_RULE_0": {
      "PRIORITY": "1",
      "PACKET_ACTION": "FORWARD",
      "SRC_IP": "10.0.0.0/32",
      "DST_IP": "20.0.0.0/32"
    },
    "l3_ACL_0_INGRESS|l3_ACL_0_RULE_1": {
      "PRIORITY": "2",
      "PACKET_ACTION": "DROP",
      "SRC_IP": "10.0.0.1/32",
      "DST_IP": "20.0.0.1/32"
    },
    "l3_ACL_0_INGRESS|l3_ACL_0_RULE_2": {
      "PRIORITY": "3",
      "PACKET_ACTION": "FORWARD",
      "SRC_IP": "10.0.0.2/32",
      "DST_IP": "20.0.0.2/32"
    },
    "l3_ACL_0_INGRESS|l3_ACL_0_RULE_3": {
      "PRIORITY": "4",
      "PACKET_ACTION": "DROP",
      "SRC_IP": "10.0.0.3/32",
      "DST_IP": "20.0.0.3/32"
    },
    "l3v6_ACL_0_INGRESS|l3v6_ACL_0_RULE_0": {
      "PRIORITY": "1",
      "PACKET_ACTION": "FORWARD",
      "SRC_IPV6": "1000::0000:0000/128",
      "DST_IPV6": "2000::0000:0000/128",
      "IP_PROTOCOL": "6",
      "L4_SRC_PORT": "100",
      "L4_DST_PORT": "10100"
    },
    "l3v6_ACL_0_INGRESS|l3v6_ACL_0_RULE_1": {
      "PRIORITY": "2",
      "PACKET_ACTION": "DROP",
      "SRC_IPV6": "1000::0000:0001/128",
      "DST_IPV6": "2000::0000:0001/128",
      "IP_PROTOCOL": "6",
      "L4_SRC_PORT": "200",
      "L4_DST_PORT": "10200"
    },
    "l3v6_ACL_0_INGRESS|l3v6_ACL_0_RULE_2": {
      "PRIORITY": "3",
      "PACKET_ACTION": "FORWARD",
      "SRC_IPV6": "1000::0000:0002/128",
      "DST_IPV6": "2000::0000:0002/128",
      "IP_PROTOCOL": "6",
      "L4_SRC_PORT": "300",
      "L4_DST_PORT": "10300"
    },
    "l3v6_ACL_0_INGRESS|l3v6_ACL_0_RULE_3": {
      "PRIORITY": "4",
      "PACKET_ACTION": "DROP",
      "SRC_IPV6": "1000::0000:0003/128",
      "DST_IPV6": "2000::0000:0003/128",
      "IP_PROTOCOL": "6",
      "L4_SRC_PORT": "400",
      "L4_DST_PORT": "10400"
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

### BGP Error Handling

```
"BGP_ERROR_CFG_TABLE": {
	"config": {
		"enable": "true"
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
BFD_PEER_MULTI_HOP and BFD_PEER_SINGLE_HOP table can be used to configure BFD multihop and single hop sessions respectively as below:

```
{
    "BFD_PEER_MULTI_HOP": {
        "10.1.1.1|null|default|10.1.1.2": {
                "desired-minimum-tx-interval": "200",
                "enabled": "true",
                "required-minimum-receive": "250"
        },
        "11.1.1.1|Ethernet0|default|11.1.1.2": {
        },
        "12.1.1.1|Ethernet0|Vrf1|12.1.1.2": {
        }                
    },
    "BFD_PEER_SINGLE_HOP": {
        "2.2.2.2|Ethernet0|default|null": {
            "desired-minimum-tx-interval":"400",
            "required-minimum-receive":"300",
            "echo-active":"true",
            "desired-minimum-echo-receive":"44",
            "detection-multiplier":"5"
        },
        "5.5.5.5|Ethernet0|default|1.1.1.1": {
        },
        "6.6.6.6|Ethernet0|Vrf1|2.2.2.2": {
        },
        "7.7.7.7|Ethernet0|Vrf1|null": {
        }
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

### CLASSIFIER_TABLE

A classifier is used to setup the match criterion to identify a traffic flow. A flow can be either identified by an ACL or part of L2-L4 header.

```
key           = CLASSIFIER_TABLE:name      ; name must be unique
                                           ; name must be 1-63 chars long

;field            = value
DESCRIPTION       = 1*255VCHAR
MATCH_TYPE        = "acl" / "fields"       ; Match on ACL or Match using individual
                                           fields.
ACL_NAME          = 1*255VCHAR             ; ACL name as present in ACL_TABLE
                                           ; valid when match_type = "acl"
SRC_MAC       = mac_addr ["/" mac_mask ]   ; Source MAC address
DST_MAC       = mac_addr ["/" mac_mask ]   ; Destination MAC address
ETHER_TYPE    = h16                        ; Ethernet type field
IP_PROTOCOL   = h8                         ; options of the l3_protocol_type field
SRC_IP        = ipv4_prefix                ; options of the source ipv4
                                           ; address (and mask) field
DST_IP        = ipv4_prefix                ; options of the destination ipv4
                                           ; address (and mask) field
SRC_IPV6      = ipv6_prefix                ; options of the source ipv4
                                           ; address (and mask) field
DST_IPV6      = ipv6_prefix                ; options of the destination ipv4
L4_SRC_PORT   = port_num                   ; source L4 port. Valid if IP Protocol is TCP
                                           ; or UDP 
L4_SRC_PORT_RANGE = port_num_L-port_num_H  ; source ports range of L4 ports field
L4_DST_PORT   = port_num                   ; destination L4 port. Valid if IP Protocol is
                                           ; TCP or UDP 
L4_DST_PORT_RANGE = port_num_L-port_num_H  ; destination ports range of L4 ports field
TCP_FLAGS     = h8/h8                      ; TCP flags field and mask
DSCP          = dscp_val[/dscp_val]        ; DSCP field. Valid range is 0-63
                                           ; address (and mask) field

;value annotations
h16           = 1*4HEXDIG
mac_addr      = 2HEXDIG "." 2HEXDIG "." 2HEXDIG "." 2HEXDIG "." 2HEXDIG "." 2HEXDIG 
                / 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG ":" 2HEXDIG
port_num      = 1*5DIGIT   ; a number between 0 and 65535
port_num_L    = 1*5DIGIT   ; a number between 0 and 65535,
                           ; port_num_L < port_num_H
port_num_H    = 1*5DIGIT   ; a number between 0 and 65535,
                           ; port_num_L < port_num_H
dscp_val      = DIGIT / %x31-36 %x30-33
ipv4_prefix = dec-octet "." dec-octet "." dec-octet "." dec-octet "/" %d1-32

ipv6_prefix   =                               6( h16 ":" ) ls32
                 /                       "::" 5( h16 ":" ) ls32
                 / [               h16 ] "::" 4( h16 ":" ) ls32
                 / [ *1( h16 ":" ) h16 ] "::" 3( h16 ":" ) ls32
                 / [ *2( h16 ":" ) h16 ] "::" 2( h16 ":" ) ls32
                 / [ *3( h16 ":" ) h16 ] "::"    h16 ":"   ls32
                 / [ *4( h16 ":" ) h16 ] "::"              ls32
                 / [ *5( h16 ":" ) h16 ] "::"              h16
                 / [ *6( h16 ":" ) h16 ] "::"
   
dec-octet   = DIGIT                   ; 0-9
              / 2DIGIT                ; 10-99
              / "1" 2DIGIT            ; 100-199
              / "2" %x30-34 DIGIT     ; 200-249
h8          = 1*2HEXDIG
h16         = 1*4HEXDIG
ls32        = ( h16 ":" h16 ) / IPv4address
```

Example

```
{
    "CLASSIFIER_TABLE": {
        "class0": {
            "ACL_NAME": "l3_ACL_0", 
            "DESCRIPTION": "", 
            "MATCH_TYPE": "acl"
        }, 
        "class1": {
            "ACL_NAME": "l2_ACL_0", 
            "DESCRIPTION": "", 
            "MATCH_TYPE": "acl"
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

### Errdisable recovery

```json

"ERRDISABLE": {
    "RECOVERY": {
        "interval": "40", 
            "udld": "enable"
    }
} 

```

### EVPN_NVO 

EVPN NVO object is used to associate the EVPN source IP with a configured
VXLAN_TUNNEL object.

```
    "EVPN_NVO": {
        "nvo1": {
            "source_vtep": "vtep1"
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

### DHCP Relay

```
"INTERFACE": {
    "Ethernet24": {
        "dhcp_servers": ["31.1.0.2", "2.2.2.3", "11.19.0.144"],
        "dhcpv6_servers": ["2001::2", "3366::1"]
    }
}

"VLAN": {
    "Vlan10": {
        "dhcp_servers": ["1.2.0.1"],
        "dhcpv6_servers": ["2001::2", "3366::1"]
    }
}

"PORTCHANNEL_INTERFACE": {
    "PortChannel10": {
        "dhcp_servers": ["31.1.0.2", "2.2.2.3", "11.19.0.144"],
        "dhcpv6_servers": ["2001::2", "3366::1"]
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

### IP Helper

IP Helper global configuration is defined in **UDP_BROADCAST_FORWARDING** table. The IP Helper addresses configuration is part of **INTERFACE** table, the INTERFACE table is modified to add a new key-value pair where the value is a comma separated list of "vrf-name|ipv4-relay-address.

```
{
    "UDP_BROADCAST_FORWARDING": {
        Ports: {
            "admin_mode" : "enable"
            "exclude_default_ports" : ["69"]
            "include_ports" : ["334", "2095"]
            "rate_limit_pps" : "600"
        }
    },
    "INTERFACE": {
        "Ethernet24": {
            "helper_addresses": ["31.1.0.2" , "2.2.2.3" , "vrf20|11.19.0.144"]
    },
}
```

### IPv4 Unnumbered Interface

The IPv4 unnumbered interface configuration is part of **INTERFACE** and **PORTCHANNEL_INTERFACE** table.

```
{
	"INTERFACE": {
		"Ethernet0": {
			"unnumbered": "Loopback1"
		}
	},
	"PORTCHANNEL_INTERFACE": {
		"PortChannel1": {
			"unnumbered": "Loopback1"
		}
	},
}
```

### KDUMP

The KDUMP configuration is part of the **KDUMP** table.

```
{
    "KDUMP": {
        "config": {
            "enabled": "true",
            "num_dumps": "3",
            "memory": "0M-2G:256M,2G-4G:320M,4G-8G:384M,8G-:448M"
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

### MIRROR_SESSION_TABLE

Mirror session table Stores information about mirror sessions and their properties.

```
key       = MIRROR_SESSION_TABLE:mirror_session_name ; mirror_session_name is
                                                     ; unique session
                                                     ; identifier
; field   = value
status    = "active"/"inactive"   ; Session state.
src_ip    = ipv4_address          ; Session souce IP address
dst_ip    = ipv4_address          ; Session destination IP address
gre_type  = h16                   ; Session GRE protocol type
dscp      = h8                    ; Session DSCP
ttl       = h8                    ; Session TTL
queue     = h8                    ; Session output queue
policer   = policer_name          ; Session policer name

;value annotations
mirror_session_name = 1*255VCHAR
policer_name        = 1*255VCHAR
h8                  = 1*2HEXDIG
h16                 = 1*4HEXDIG
ipv4_address        = dec-octet "." dec-octet "." dec-octet "." dec-octet “/” %d1-32
dec-octet           = DIGIT                     ; 0-9
                       / %x31-39 DIGIT         ; 10-99
                       / "1" 2DIGIT            ; 100-199
                       / "2" %x30-34 DIGIT     ; 200-249
```



### MCLAG

MCLAG Domain configuration is defined in MCLAG_DOMAIN table. MCLAG interface configuration is defined in MCLAG_INTERFACE table. Schema and examples for both tables are given below



**Schema** 

```
;MCLAG Domain Table
key               = MCLAG_DOMAIN|domain_id ; DIGIT 1-4095 

;field             = value
source_ip          = IP               ; local ip address 
peer_ip            = IP               ; peer  ip address 
peer_link          = port_name        ; peer link name
keepalive_interval = time_in_secs     ; keepalive time in seconds 
session_timeout    = time_in_secs     ; session timeout in multiple of 
									  ; keepalive_interval.

;value annotations
port_name         = 1*64VCHAR        ; name of the port, must be unique
time_in_secs      = dec-octet        ; time in seconds
IP                = IPV4address
IPv4address        = dec-octet "." dec-octet "." dec-octet "." dec-octet "

 dec-octet        = DIGIT            ; 0-9
                / %x31-39 DIGIT      ; 10-99
                / "1" 2DIGIT         ; 100-199
                / "2" %x30-34 DIGIT  ; 200-249
                / "25" %x30-35       ; 250-255


;MCLAG Interface Table
key              = MCLAG_INTERFACE|domain_id|ifname ; 

;value annotations
domain_id 	   = DIGIT 1-4095     ; domain_id for the MCLAG
ifname         = 1*64VCHAR        ; name of the MCLAG Interface (PortChannel)

```



**Example**

```
"MCLAG_DOMAIN": {
        "10": {
            "peer_ip": "10.10.10.2",
            "peer_link": "PortChannel2",
            "source_ip": "10.10.10.1"
        }
    },
    "MCLAG_INTERFACE": {
        "10|PortChannel3": {
            "if_type": "PortChannel"
        },
        "10|PortChannel4": {
            "if_type": "PortChannel"
        },
        "10|PortChannel10": {
            "if_type": "PortChannel"
        }
    }


```

### 

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

### POLICY_TABLE

Policy table is used to configure the policy parameters. 

```
key           = POLICY_TABLE:name            ; name must be unique
                                             ; name must be 1-63 chars long

;field      = value
TYPE        = "qos"               ; Only QoS is supported now.
DESCRIPTION = 1*255VCHAR          ; Policy Description

;value annotations
```

Example

```
{
	"POLICY_TABLE": {
        "policy0": {
            "DESCRIPTION": "", 
            "TYPE": "qos"
        }
    }
}
```

### POLICY_SECTIONS_TABLE

Policy details table provides information on the classifiers to use and their corresponding actions. A policy can have up to 128 classifiers

```
key           = POLICY_SECTIONS_TABLE:policy_name:classifier_name ; name must be unique
                                                       ; name must be 1-63 chars long

;field            = value
PRIORITY          = 1*3DIGIT            ; Valid Range is 0-999
DESCRIPTION       = 1*255VCHAR          ; Policy Description
SET_DSCP          = dscp_val            
SET_PCP           = pcp_val
SET_POLICER_CIR   = 1*12DIGIT
SET_POLICER_CBS   = 1*12DIGIT
SET_POLICER_PIR   = 1*12DIGIT
SET_POLICER_PBS   = 1*12DIGIT

;value annotations
dscp_val      = DIGIT / %x31-36 %x30-33
pcp_val       = %x30-37
```

Example

```
{
    "POLICY_SECTIONS_TABLE": {
        "policy0|class0": {
            "PRIORITY": "200", 
            "SET_DSCP": "15", 
            "SET_PCP": "5"
        }, 
        "policy0|class1": {
            "PRIORITY": "100", 
            "SET_DSCP": "30", 
            "SET_PCP": "2"
        }
    }
}
```

### POLICY_BINDING_TABLE

This table provides information on Policy application on ports

```
key           = POLICY_BINDING_TABLE:port_name   ; port_name is the name of the Port or
                                                 ; LAG or VLAN or "switch"

;field                     = value
INGRESS_QOS_POLICY         = 1*63VCHAR
INGRESS_MONITORING_POLICY  = 1*63VCHAR
EGRESS_QOS_POLICY          = 1*63VCHAR
EGRESS_MONITORING_POLICY   = 1*63VCHAR

;value annotations
```

Example:

```
{
    "POLICY_BINDING_TABLE": {
        "Ethernet0": {
            "INGRESS_QOS_POLICY": "policy0"
        }, 
        "Ethernet4": {
            "INGRESS_QOS_POLICY": "policy0"
        }, 
        "Ethernet8": {
            "EGRESS_QOS_POLICY": "policy0"
        }
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
        "static": "true",
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

### Port Mirroring

Mirroring related configurations are defined in **MIRROR_SESSION** tables.
```
{
    "MIRROR_SESSION": {
        "Mirror1": {
            "direction": "rx",
            "dscp": "10",
            "dst_ip": "10.1.1.1",
            "gre_type": "0x88ee",
            "queue": "5",
            "src_ip": "11.1.1.1",
            "src_port": "Ethernet0",
            "ttl": "63"
        },
        "Mirror2": {
            "direction": "both",
            "dst_port": "Ethernet4",
            "src_port": "Ethernet0"
        }
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

### Shaping

Shaper attributes are defined as part of scheduler profile in **SCHEDULER** Table

#### Port Shaping

Shaping is applied to a port(or set of ports) by setting the scheduler profile name to the scheduler attribute of an interface(or set of interfaces) in **PORT_QOS_MAP** Table.

In the example below, Port "Ethernet52" will be limited to 8Gbps (1 GB/sec)
```
{
    "SCHEDULER": {
        "scheduler.port": {
            "meter_type": "bytes",
            "pir": "1000000000",
            "pbs": "8192"
        }
    },
    "PORT_QOS_MAP": {
        "Ethernet52": {
            "scheduler": "[SCHEDULER|scheduler.port]"
        }
    }
}
```

#### Queue Shaping

Shaping is applied to queue(or set of queues) by setting the scheduler profile name to the scheduler attribute of the queue(or queue range) of an interface in the **QUEUE** Table.
Queue shaping can be configured on any front panel port egress queue.

In the example below, Port "Ethernet52" egress queues 0 through 5 will be limited to 10Gbps each (1.25 GB/sec)
```
{
    "SCHEDULER": {
        "scheduler.queue": {
            "meter_type": "bytes",
            "pir": "1250000000",
            "pbs": "8192"
        }
    },
    "QUEUE": {
        "Ethernet52|0-5": {
            "scheduler": "[SCHEDULER|scheduler.queue]"
        }
    }
}
```

### SNMP CLI


The SNMP CLI feature configuration is defined in **SNMP_AGENT_ADDRESS_CONFIG**, **SNMP_SERVER**,  **SNMP_SERVER_ENGINE**, **SNMP_SERVER_COMMUNITY**, **SNMP_SERVER_GROUP**, **SNMP_SERVER_GROUP_MEMBER**, **SNMP_SERVER_GROUP_ACCESS**, **SNMP_SERVER_PARAMS**, **SNMP_SERVER_TARGET**, **SNMP_SERVER_USER** and **SNMP_SERVER_VIEW**

```
{
    "SNMP_AGENT_ADDRESS_CONFIG": {
        "127.0.0.1|12345|veth0": {}
    },
    "SNMP_SERVER": {
        "SYSTEM": {
            "sysContact": "contact",
            "sysLocation": "location",
            "traps": "enable"
        }
    },
    "SNMP_SERVER_ENGINE": {
        "GLOBAL": {
            "engine-id": "80:00:05:23:01:a9:fe:80:74"
        }
    },
    "SNMP_SERVER_COMMUNITY": {
        "private": {
            "securityName": "privateGroup"
        }
    },
    "SNMP_SERVER_GROUP": {
        "privateGroup": {},
        "DefaultRead": {},
        "SecureRead": {},
        "groupname": {},
    },
    "SNMP_SERVER_GROUP_MEMBER": {
        "privateGroup|private": {
            "securityModel": [
                "v2c"
            ]
        },
        "groupname|username": {
            "securityModel": [
                "usm"
            ]
        }
    }
    "SNMP_SERVER_GROUP_ACCESS": {
        "DefaultRead|Default|any|no-auth-no-priv": {
            "notifyView": "Default",
            "readView": "Default",
            "writeView": "None"
        },
        "SecureRead|Default|usm|auth-priv": {
            "notifyView": "Default",
            "readView": "Default",
            "writeView": "None"
        }
    },
    "SNMP_SERVER_PARAMS": {
        "targetEntry1": {
            "securityNameV2": "public"
        },
        "targetEntry2": {
            "security-level": "auth-priv",
            "user": "David"
        }
    },
    "SNMP_SERVER_TARGET": {
        "targetEntry1": {
            "ip": "1.2.3.4",
            "port": "162",
            "retries": "3",
            "tag": [
                "trapNotify"
            ],
            "targetParams": "targetEntry1",
            "timeout": "1500"
        },
        "targetEntry2": {
            "ip": "80ef:0620:1963::1776",
            "port": "162",
            "retries": "4",
            "tag": [
                "informNotify"
            ],
            "targetParams": "targetEntry2",
            "timeout": "600"
        }
    },
    "SNMP_SERVER_USER": {
        "username": {
            authKeyType: "01:23:45:67:89:ab:cd:ef:01:23:45:67:89:ab:cd:ef"
            privKeyType: "01:23:45:67:89:ab:cd:ef:01:23:45:67:89:ab:cd:ef"
        }
    }
    "SNMP_SERVER_VIEW": {
        "Default": {
            "exclude": [
                "1.3.6.1.6.3.15.1.2",
                "1.3.6.1.6.3.16"
            ],
            "include": [
                "1"
            ]
        }
    },
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
        "rootguard_timeout": "30",
        "bpdu_filter": "true"
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
        "uplink_fast": "false",
        "bpdu_filter": "enable"
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

### Spanning Tree (RPVST+)

Rapid Spanning Tree can be configured Globally, per VLAN, per interface and per VLAN-interface as below:
```
"STP":
{
    "GLOBAL": {
        "forward_delay": "15", 
        "hello_time": "2", 
        "max_age": "20", 
        "mode": "rpvst", 
        "priority": "32768", 
        "rootguard_timeout": "30",
        "bpdu_filter": "true"
    }
}

"STP_INTF":
{
    "Ethernet0": {
        "bpdu_guard": "false", 
        "bpdu_guard_do_disable": "false", 
        "enabled": "true", 
        "edgeport": "true", 
        "root_guard": "false" 
    }, 
    "PortChannel100": {
        "bpdu_guard": "false", 
        "bpdu_guard_do_disable": "false", 
        "enabled": "true", 
        "edgeport": "true", 
        "root_guard": "false", 
        "bpdu_filter": "enable"
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

### UDLD

UDLD has tables for global and per port level configurations
```
{
    "UDLD":{
        "GLOBAL": {
                "admin_enable": "true", 
                "aggressive": "false", 
                "msg_time": "1", 
                "multiplier": "3"
        }
    }
}

{
    "UDLD_PORT": {
        "Ethernet48": {
            "admin_enable": "true", 
            "aggressive": "false"
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

### VXLAN_TUNNEL

The VXLAN_TUNNEL table is used to configure the VTEP. Only a single instance is supported in
this release. This configures the IP address of the VTEP.

```
    "VXLAN_TUNNEL": {
        "vtep1": {
            "src_ip": "1.1.1.1"
        }
    }
```

### VXLAN_TUNNEL_MAP

The VXLAN_TUNNEL_MAP table is used to configure the VLAN VNI mapping. The key 
contains the VTEP name as well as the mapname. When configured through click 
interface the mapname is generated in the format map_vnid_Vlanvlanid.

```
    "VXLAN_TUNNEL_MAP": {
        "vtep1|map_50_Vlan5": {
            "vlan": "Vlan5",
            "vni": "50"
        },
        "vtep1|map_60_Vlan6": {
            "vlan": "Vlan6",
            "vni": "60"
        }
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

### SYSLOG SERVER

Added Priority and Filter_rule attributes to syslog server. Either one of them can be used but not both. Use "priority" to filter log based only on one priority level. User "filter_rule" to filter log based on rsyslog selectors rules with multiple criteria.

```
{
    "SYSLOG_SERVER": {
        "20.0.0.20": {
            "filter_rule": "mail,news.=alert;*.=crit"
        },
        "33.33.100.3": {
            "priority": "*.err"
        },
        "50.50.50.1": {},
        "100.1.100.52": {
            "priority": "*.=warning"
        },
        "221.22.200.21": {
            "priority": "*.*;*.!notice"
        },
        "rmt.syslog.srv": {}
    }
}
```
### IPv6 Link-local

The IPv6 Link-local only configuration is part of **INTERFACE** table.

```
{
    "INTERFACE": {
        "Ethernet0": {
            "ipv6_use_link_local_only": "enable"
        },
        "Ethernet2": {
            "ipv6_use_link_local_only": "disable"
        }
    },
    "VLAN_INTERFACE": {
        "Vlan10": {
            "ipv6_use_link_local_only": "enable"
        }
    },
    "PORTCHANNEL_INTERFACE": {
        "PortChannel10": {
            "ipv6_use_link_local_only": "disable"
        }
    }
}
```


### IFA

IFA configuration is spread into **TAM\_INT\_IFA\_FEATURE\_TABLE**, **TAM\_DEVICE\_TABLE**, **TAM\_COLLECTOR\_TABLE**, **TAM\_INT\_IFA\_FLOW\_TABLE** table.

```
{
    "TAM_COLLECTOR_TABLE": {
        "collector1": {
            "ipaddress": "11.12.13.14",
            "ipaddress-type": "ipv4",
            "port": "9070"
        }
    },
    "TAM_DEVICE_TABLE": {
        "device": {
            "deviceid": "3344"
        }
    },
    "TAM_INT_IFA_FEATURE_TABLE": {
        "feature": {
            "enable": "true"
        }
    },
    "TAM_INT_IFA_FLOW_TABLE": {
        "flow1": {
            "acl-rule-name": "rule1",
            "acl-table-name": "acl1",
            "sampling-rate": "1000"
        },
        "flow2": {
            "acl-rule-name": "rule2",
            "acl-table-name": "acl2",
            "collector": "collector1"
        }
    }
}
```

IFA ingress node and egress node depend on ACL configuration with TAM specific actions INT_INSERT and INT_DELETE respectively

```
{
        "ACL_TABLE": {
            "acl1": {
                "policy_desc" : "IFA Ingress Device Policy",
                "stage" : "INGRESS",
                "type" : "L3" ,
                "ports" : "Ethernet20"
                },
            "acl2": {
                "policy_desc" : "IFA Egress Device Policy",
                "stage" : "INGRESS",
                "type" : "L3" ,
                "ports" : "Ethernet21"
                }

        },

        "ACL_RULE": {
            "acl1|rule1": {
                "PRIORITY" : "55",
                "IP_TYPE" : "ipv4any",
                "IP_PROTOCOL": "17",
                "SRC_IP" : "10.10.0.26/32",
                "DST_IP" : "10.10.1.26/32",
                "PACKET_ACTION" : "int_insert"
            },
            "acl2|rule2": {
                "PRIORITY" : "55",
                "IP_TYPE" : "ipv4any",
                "IP_PROTOCOL": "17",
                "SRC_IP" : "10.10.2.30/32",
                "DST_IP" : "10.10.3.30/32",
                "PACKET_ACTION" : "int_delete"
            }

        }
}
```
### PTP

The global configurations of PTP are part of PTP_CLOCK table.
The port level configurations are part of PTP_PORT table.

```
    "PTP_CLOCK": {
        "GLOBAL": {
            "clock-type": "BC",
            "domain-number": "1",
            "domain-profile": "ieee1588",
            "log-announce-interval": "0",
            "network-transport": "UDPv4",
            "priority1": "110",
            "priority2": "128",
            "slave-only": "0",
            "two-step-flag": "1",
            "udp6-scope": "0xe",
            "unicast-multicast": "unicast"
        }
    },
    "PTP_PORT": {
        "GLOBAL|Ethernet48": {
            "underlying-interface": "Ethernet48",
            "unicast-table": "192.168.64.1"
        },
        "GLOBAL|Ethernet60": {
            "underlying-interface": "Ethernet60",
            "unicast-table": "192.168.56.1"
        }
    }
```

### sFlow

sFlow global configuration attributes admin_state, agent_id and polling_interval are defined in **SFLOW\_TABLE** 

```
"SFLOW": {
        "global": {
            "admin_state": "up",
            "agent_id": "Ethernet8",
            "polling_interval": "100"
        }
    },
```

sFlow Collector configuration is defined in **SFLOW\_COLLECTOR\_TABLE**
```
    "SFLOW_COLLECTOR": {
        "collector1": {
            "collector_ip": "10.0.0.1",
            "collector_port": "6343"
        },
        "collector2": {
            "collector_ip": "20.0.0.1",
            "collector_port": "9898"
        }
    },
```

Interface specific sFlow configuration attributes are defined in **SFLOW\_SESSION\_TABLE**
```
    "SFLOW_SESSION": {
        "Ethernet0": {
            "admin_state": "up",
            "sample_rate": "1000"
        },
        "Ethernet4": {
            "admin_state": "up"
        }
    }
```

### IGMP Snooping

IGMP Snooping configuration is defined in **CFG_L2MC_TABLE**, static mrouter configuration is defined in **CFG_L2MC_MROUTER_TABLE**, and static IGMP group configuration is defined in **CFG_L2MC_STATIC_MEMBER_TABLE** table. And, all these table configurations are per VLAN.



```
{
    "CFG_L2MC_TABLE": {
        "Vlan200": {
            "query-interval": "125", 
            "enabled": "true", 
            "version": "2", 
            "last-member-query-interval": "1000", 
            "querier": "false", 
            "query-max-response-time": "10", 
            "fast-leave": "false"
        }
    },
    
    "CFG_L2MC_MROUTER_TABLE": {
        "Vlan200|Ethernet0": {
            "mrouter_port": "Ethernet0"
        }
    }, 
    
    "CFG_L2MC_STATIC_MEMBER_TABLE": {
        "Vlan200|225.0.0.1|Ethernet4": {
            "port": "Ethernet4"
        }
    }
}
```

### BUM Storm Control
Broadcast, Unknown-multicast and Unknown-unicast storm-control configuration is supported on Ethernet interfaces and the rate is configured in kbps.

```
{
    "PORT_STORM_CONTROL": {
        "Ethernet0:broadcast": {
            "kbps": "100000"
        },
        "Ethernet0:unknown-multicast": {
            "kbps": "300000"
        },
        "Ethernet0:unknown-unicast": {
            "kbps": "200000"
        }
    }
}
```

### DROP MONITOR

The drop monitor feature configuration is defined in **TAM_DEVICE_TABLE**, **SAMPLE_RATE_TABLE**, **TAM_COLLECTOR_TABLE**, **TAM_DROP_MONITOR_FEATURE_TABLE**,  **TAM_DROP_MONITOR_FLOW_TABLE** and **TAM_DROP_MONITOR_AGING_INTERVAL_TABLE**. The ACL configuration for drop monitor comes via **ACL_TABLE** and **ACL_RULE**.

```
{
    "TAM_DEVICE_TABLE": {
        "device": {
            "deviceid": "2345"
        }
    },
    "SAMPLE_RATE_TABLE": {
        "s1": {
            "sampling-rate": "10"
        }
    },
    "TAM_COLLECTOR_TABLE": {
        "cr1": {
            "ipaddress": "10.10.10.10",
            "ipaddress-type": "ipv4",
            "port": "9070"
        }
    },
    "TAM_DROP_MONITOR_FEATURE_TABLE": {
        "feature": {
             "enable": "true"
        }
    },
    "TAM_DROP_MONITOR_FLOW_TABLE": {
        "flow1": {
            "acl-rule-name": "R1",
            "acl-table-name": "A1",
            "collector": "cr1",
            "sample": "s1",
            "flowgroup-id": "1"
        },
    }
    "TAM_DROP_MONITOR_AGING_INTERVAL_TABLE": {
        "aging ": {
            "aging-interval ": "10"
        }
    }
    "ACL_TABLE": {
        "A1": {
            "policy_desc" : "Drop monitor policy",
            "stage" : "INGRESS",
            "type" : "TAM" ,
            "ports" : "Ethernet20"
        }
    }
    "ACL_RULE": {
        "A1|R1": {
            "PRIORITY" : "55",
            "IP_TYPE" : "ipv4any",
            "IP_PROTOCOL": "17",
            "SRC_IP" : "10.10.0.26/32",
            "DST_IP" : "10.10.1.26/32",
            "PACKET_ACTION" : "MONITOR_DROPS"
        }
    }
}
```

### TAIL TIMESTAMPING

The tail timestamping feature configuration is defined in **TAM_DEVICE_TABLE**, **TAM_INT_IFA_TS_FEATURE_TABLE** and **TAM_INT_IFA_TS_FLOW*. The ACL configuration comes via **ACL_TABLE** and **ACL_RULE**.

```
{
    "TAM_DEVICE_TABLE": {
        "device": {
            "deviceid": "2345"
        }
    },
    "TAM_INT_IFA_TS_FEATURE_TABLE": {
        "feature": {
            "enable": "true"
        }
    },
    "TAM_INT_IFA_TS_FLOW": {
        "F1": {
            "acl-rule-name": "Rule1",
            "acl-table-name": "TS"
        }
    },
    "ACL_TABLE": {
        "TS": {
            "policy_desc" : "IFA - Tail Timestamping Policy - Match IFA packets",
            "stage" : "INGRESS",
            "type" : "TAM" ,
            "ports" : "Ethernet20"
        }
    }
    "ACL_RULE": {
        "TS|Rule1": {
            "PRIORITY" : "55",
            "IP_PROTOCOL": "17",
            "TAM_INT_TYPE" : "IFA",
            "PACKET_ACTION" : "int_insert"
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

