# CLI guidelines
This page includes the guidelines for creating CLI command pages.

## Command structure
All CLI commands should follow these guidelines:
- Command help descriptions must be parallel and start with a plural action such as configures, imports, clears, and so on
- Command help text must follow acronym usage (below)
- Optional parameters must be preceeded by the word (Optional) in descriptions
- Commands must include ranges and the default value (0 to 128; default 10)
- Maximum parameters values are stated as (up to 32 characters; 9 characters minimum)
- Underscores cannot be used for commands as they are reserved for command parameters, such as `interface_name`
- All command descriptions to follow sentence case (only capitalize the first word)
- Always write in the first person (you can configure...)
- Show command descriptions always starts with the word displays (Displays the startup configuration)

## Hyphenation usage
You must hyphenate these words in CLI commands and command descriptions.
- access-control
- access-list
- access-path
- agent-id
- aging-interval
- announce-timeout
- anycast-gateway
- as-path
- auth-type
- auto-breakout
- auto-configuration
- auto-negotiation
- community-list
- control-plane
- dhcp-relay
- dial-in
- edge-port
- egress-port
- equal-cost
- extcommunity-list
- forward-protocol
- helper-address
- high-power
- import-check
- in-band
- ingress-port
- interface-level
- keepalive-interval
- link-aggregation
- link-bundle
- link-type
- load-balance/load-balancing
- local-preference
- low-power
- mac-address
- management-route
- master-table
- max-age
- maximum-paths
- maximum-prefix
- message-time
- mirror-session
- multi-chassis
- multi-path
- network-transport
- next-hop
- num-dumps
- override-capability
- password-attributes
- persistent-watermark
- policy-based
- policy-map
- polling-interval
- port-channel
- port-priority
- prefix-list
- priority-group
- qos-map
- radius-server
- rate-limit
- read-quanta
- remote-as
- round-trip
- route-map
- route-reflector
- running-configuration
- sample-rate
- scale-profile
- self-originate
- service-policy
- source-ip
- source-protocol
- spanning-tree
- startup-configuration
- system-level
- system-qos
- tacacs-server
- tcp-timeout
- trust-map
- udp-timeout
- unsuppress-map
- update-delay
- update-source
- uplink-state-group
- virtual-network
- vlan-id
- vrf-name
- warm-restart
- wred-profile
- write-quanta
- zero-touch

## Non-hypenated usage
These words are never hyphenated.
- multicast
- preempt
- unidirectional

## Parameter usage
Parameters to indicate if the option is required or optional, and appear in italics. Underscores are reserved for command parameters.
- _destination_ip_
- _destination_mac_
- _destination_port_
- _domain_id_
- _interface_name_
- _loopback_id_
- _portchannel_id_
- _rx_power_
- _source_ip_
- _tx_power_
- _vlan_id_
- _vrf_name_

## Acronym usage
All acronyms must be spelled out on the first use, then used as the acronym moving forward. For example, access-control list (ACL). 

|Acronym|Description|
|-------|-----------|
|AAA|authentication, authorization, and accounting|
|ACL|access-control list|
|ARP|address resolution protocol|
|BFD|bidirectional forwarding detection|
|BGP|border gateway protocol|
|CoPP|control plane policing|
|DHCP|dynamic host configuration protocol|
|DNS|domain name server|
|DOM|digital optical monitoring|
|ECMP|equal-cost multi-path
|ECN|explicit congestion notification|
|EEE|energy-efficient Ethernet|
|FCoE|fibre channel over Ethernet|
|FEC|forward error detection|
|FEFD|far-end failure detection|
|FIPS|FCoE initialization protocol snooping|
|IFA|in-band flow analyzer|
|IGMP|Internet group management protocol|
|LACP|link aggregation control protocol|
|LAG|link aggregation group|
|LLDP|link layer discovery protocol|
|LPI|low-power idle|
|MAC|media access control|
|MCLAG|multi-chassis link aggregation group|
|MLDP|multicast listener discovery protocol|
|MTU|maximum transmission unit|
|NAT|network address translation|
|NDP|network discovery protocol|
|NOS|network operating system|
|NTP|network time protocol|
|OOB|out-of-band|
|OSPF|open shortest path first|
|OTM|object tracking manager|
|PBR|policy-based routing|
|PIM|protocol independent multicast|
|PMTU|path maximum transmission unit|
|PTP|precision time protocol|
|QoS|quality of service|
|RADIUS|remote authentication dial-in user service|
|RTT|round-trip time|
|SNMP|simple network management protocol|
|STP|spanning-tree protocol|
|TACACS|terminal access controller access-control system|
|TTL|time-to-live|
|UDLD|unidirectional link detection|
|UFD|uplink failure detection|
|UFT|unified forwarding table|
|USB|universal serial bus|
|VLAN|virtual local area network|
|VRF|virtual routing and forwarding|
|VRRP|virtual router redundancy protocol|
|VxLAN|virtual extensible local area network|
|ZTD|zero-touch deployment|
