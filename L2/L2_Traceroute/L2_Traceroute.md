# L2 Traceroute

#### Rev 0.1





# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev  |    Date    |       Author        | Change Description                                           |
|:--:|:--------:|:-----------------:|:------------------------------------------------------------:|
| 0.1  | 05/03/2021 |   Dhanasekar Rathinavel   | Initial version                                              |
|      |            |                      |                    |

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term** | **Meaning**                    |
| -------- | ------------------------------ |
| LAN      | Local Area Network             |
| MAC      | Media Access Control addresses |
| VLAN     | Virtual Local Area Network     |

# About this Manual

This document provides general overview of L2 Traceroute feature(Trace-L2).



# 1 Introduction and Scope

Trace-L2 can trace all the L2 traffic paths for a given VLAN or to a particular destination device in the given VLAN. The destination device is identified based on its IP, DNS name or MAC. The main use of the Trace-L2 feature is to provide flexibility to administrators to determine the L2 paths in a L2 topology.
Trace-L2 is a proprietary feature and it cannot interop with any other vendors. FYI, cisco has its own proprietary L2-traceroute.

# 2 Feature Requirements

Trace-L2 feature supports the following:

- Traces all the available paths to a particular IP, MAC or hostname in a VLAN.
- Probes the entire Layer 2 topology.
- Displays the input or output ports of each hop in the path.
- Displays the round trip travel time of each hop.
- Displays hops in a VLAN that form a loop.
- Displays each hop's Layer 2 protocol such as STP, RSTP, 802.1w, SSTP or route-only.

The resulting trace displays a report that provides information about a packet’s path to a device, such as hop and port information and travel time. It also can locate any Layer 2 loop in a VLAN. The probed Layer 2 information is discarded when a new command is issued again.

For each hop in the path, trace-l2 displays its input/output port, L2 protocols of the input port, and the microsecond travel time between hop and hop. It also prints out the hops which form a loop, if any. Displaying L2 topology lets a user easily obtain information of all hops.

The devices that will participate in the trace-l2 protocol must be assigned to a VLAN and all devices on that VLAN must be SONiC devices that support the trace-l2 protocol.
Devices that do not support the trace-l2 protocol will forward the trace-l2 packets without a reply. Hence, these devices are transparent to the trace-l2 protocol.
The destination for the packet with the trace-l2 protocol must be a device that supports the trace-l2 protocol and the destination cannot be a client, such as a personal computer, or devices from other vendors.
Trace-l2 will follow the xSTP path if enabled in the system.


## 2.1 Functional Requirements


## 2.2 Configuration and Management Requirements
- All ports must be part of same Vlan.
- trace-l2 first tries to use the IP address of the virtual routing interface that is associated with a VLAN. If the virtual routing interface is not available, it then uses the loopback address. If both addresses are not available, it displays MAC address only.
- One way time is not available because the trace-l2 protocol does not synchronize the clocks between hops.


## 2.3 Scalability Requirements
The trace-l2 command cannot reach any device beyond 60 hops. The maximum hop number is 60 which is the number of hub informations that can be stored in a 1500 bytes Ethernet packet. 


## 2.4 Warm Boot Requirements

This is a utility command. Will not support WB.


## 5 CLI


### 5.1 Configuration Commands

### 5.2 Show Commands

- trace-l2 show
```
 The probed topology is displayed using this command.

```

### 5.3 Exec Commands

- trace-l2 vlan <vlan_id> <destination_address>

```
This command is used to trace traffic path of a packets in a L2 topology for a specific vlan

The <destination address> can be a MAC address, an IP address, or a host name. You can enter the
destination-address in one of the following formats:

- HHHH.HHHH.HHHH – Destination MAC address
- A.B.C.D – Destination IP address
- ASCII string – Destination host name

If a destination address is not specified as shown below or the destination does not exists, trace-l2 collects L2 topology information which can be displayed by issuing a ‘trace-l2 show’ command.
```

- trace-l2 vlan <vlan_id>
```
This command is used to probe L2 traffic path of a packets in a L2 topology for a specific vlan.
```


## 6 Serviceability and Debug

The existing logging mechanisms shall be used. Proposed debug framework shall be used for internal state dump.



## 7 Unit Test cases

|      |      |      |
| ---- | ---- | ---- |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
|      |      |      |
