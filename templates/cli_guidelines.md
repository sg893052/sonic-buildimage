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

## Acronym usage
All acronyms must be spelled out on the first use, then used as the acronym moving forward. For example, access-control list (ACL). 

|Acronym|Description|
|-------|-----------|
|AAA|authentication, authorization, and accounting|
|ACL|access-control list|
|BGP|border gateway protocol|
|DDL|Dell digital locker|
|DF|don't fragment|
|DHCP|dynamic host configuration protocol|
|DNS|domain name server|
|EEE|energy-efficient Ethernet|
|FEC|forward error detection|
|FEFD|far-end failure detection|
|IGMP|Internet group management protocol|
|L1|layer 1|
|L2|layer 2|
|L3|layer 3|
|LACP|link aggregation control protocol|
|LAG|link aggregation group|
|LLDP|link layer discovery protocol|
|LPI|low power idle|
|MAC|media access control|
|MTU|maximum transmission unit|
|MLDP|multicast listener discovery protocol|
|NOS|network operating system|
|NTP|network time protocol|
|OOB|out-of-band|
|POST|power-on self-test|
|PMTU|path maximum transmission unit|
|PTP|precision time protocol|
|QoS|quality of service|
|RMA|return material authorization|
|RTT|round-trip time|
|SNMP|simple network management protocol|
|STP|spanning-tree protocol|
|TTL|time-to-live
|USB|universal serial bus|
|VLAN|virtual local area network|
|VRF|virtual routing and forwarding|
|VxLAN|virtual extensible local area network|
|ZTD|zero-touch deployment|

## Terminology
|Usage|Description|
|-------|-----------|
|`x:x:x:x::x`|IPv6 format|
|A.B.C.D|IPv4 dotted-decimal format|

## Hyphenation
You must hyphenate these words in CLI commands and command descriptions.
- access-control
- access-list
- as-path
- auto-configuration
- auto-negotiation
- community-list
- control-plane
- destination-ip
- destination-mac
- destination-port
- extcommunity-list
- ingress-port
- link-aggregation
- load-balance/load-balancing
- management-route
- next-hop
- node-id
- password-attributes
- policy-map
- port-channel
- prefix-list
- qos-map
- radius-server
- route-map
- running-configuration
- scale-profile
- source-ip
- spanning-tree
- startup-configuration
- system-qos
- tacacs-server
- trust-map
- uplink-state-group
- virtual-network
- vlan-id
- vrf-name
- wred-profile

## Non-hypenations
These words are never hyphenated.
- preempt
- multicast
