#  SQA Test Plan
# Source Intf and Vrf Support for Syslog 
#  SONiC 3.1 Project and Buzznik+ Release
[TOC]



# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 05/20/2020 | Manisha Joshi | Initial version |
| 0.2 | 07/12/2020 | Hafis Saly | Added review comments|
| 0.3 | 07/15/2020 | Manisha Joshi | Addressed all the review comments |

# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
| Remote syslog with source-interface and vrf support | Suresh Babu |

# List of Approvers
|  Function | Name | Date Approved|
|:---:|:-----------:|:------------------:|
|  |   |  |

# Definition/Abbreviation
| **Term** | **Meaning**                     |
| -------- | ------------------------------- |
|     |        |



# Feature Overview

Configure switch using REST and KLISH cli to forward messages to remote syslog server. Also provide source-ip and VRF to be used while sending out syslog messages. Below is the ling for the High level design of the feature

<http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/21020/>

# 1 Test Focus Areas
This document provides test cases for the configuration and verification of remote syslog server using KLISH CLI and REST/OCYANG Method


# 2 Topologies
## 2.1 Topology 1

![Dynamic](syslog.png "Topology 1")

# 3 Test  Case and Objectives
## 3.1 **CLI**
## 3.2 **Functional**
#### 3.2.1 Verify remote syslog server over management interface

| Test    ID | FtOpSoRoSyslogFun001                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server over management interface        |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure the remote syslog server with host address as management interface. 2) Verify using show logging servers command. 3) Re-verify the messages are sent after delete/re-add the server configuration and  shut/no-shut of the management interface. |

#### 3.2.2 Verify remote syslog server with physical interface as a source-interface

| Test    ID | FtOpSoRoSyslogFun002                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with physical interface as a source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1)Configure an IPv4 address on a physical interface connected to the server 2) Configure the remote syslog server with host address as IPv4 address and physical interface as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration and  shut/no-shut of the interface. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.3 Verify remote syslog server with vlan as a source-interface

| Test    ID | FtOpSoRoSyslogFun003                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with vlan as a source-interface  |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a vlan interface connected to the server 2) Configure the remote syslog server with host address as IPv4 address and vlan as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration and remove/add of the vlan. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.4 Verify remote syslog server with portchannel as a source-interface

| Test    ID | FtOpSoRoSyslogFun004                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with port channel as a source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a portchannel connected to the server 2) Configure the remote syslog server with host address as IPv4 address and portchannel as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration, remove/add member ports and add/delete portchannel. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.5 Verify remote syslog server with loopback as a source-interface

| Test    ID | FtOpSoRoSyslogFun005                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with loopback as a source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a loopback connected to the server 2) Configure the remote syslog server with host address as IPv4 address and loopback as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration and add/delete loopback. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address 8) Modify the configuration to use ethernet/vlan interface as the source interface and repeat steps 4-4 |

#### 3.2.6 Verify remote syslog server with physical interface as a source-interface over a user-defined VRF

| Test    ID | FtOpSoRoSyslogFun006                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with physical interface as a source-interface over a user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1)Configure an IPv4 address on a physical interface bound to a VRF  2) Configure the remote syslog server with host address as IPv4 address and physical interface as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration, bind/unbind the interface and remove/add vrf. 5) Modify the address to another IPv4 address, non default syslog port  and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.7 Verify remote syslog server with vlan as a source-interface over a user-defined VRF

| Test    ID | FtOpSoRoSyslogFun007                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with vlan as a source-interface over a user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a vlan bound to a vrf 2) Configure the remote syslog server with host address as IPv4 address and vlan as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after  remove/add of the vrf and bind/unbind the vlan. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.8 Verify remote syslog server with portchannel as a source-interface over a user-defined VRF

| Test    ID | FtOpSoRoSyslogFun008                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with port channel as a source-interface as a source-interface over a user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a portchannel bound to a vrf 2) Configure the remote syslog server with host address as IPv4 address and portchannel as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after bind/unbind of portchannel. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Verify Deletion of VRF is not allowed 8) Reverify the steps with ipv6 address |

#### 3.2.9 Verify remote syslog server with loopback as a source-interface over user-defined VRF

| Test    ID | FtOpSoRoSyslogFun009                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with loopback as a source-interface over user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a loopback bound to a VRF 2) Configure the remote syslog server with host address as IPv4 address and loopback as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after bind/unbind of the loopback. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.10 Configure and verify syslog server after modifying the source-interface

| Test    ID | FtOpSoRoSyslogFun010                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server after modifying the source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using with physical interface. 2) Modify the source interface from a physical interface to a vlan followed by a portchannel and loopback |

#### 3.2.11 Configure and verify syslog server with non default remote port configuration

| Test    ID | FtOpSoRoSyslogFun0011                                        |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server without source-interface configuration |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using a non default remote-port using logging server host ip command. 2)  Verify the configuration using the show command |

#### 3.2.12 Configure and verify syslog server without source-interface configuration over a user-defined VRF

| Test    ID | FtOpSoRoSyslogFun0012                                        |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server without source-interface configuration |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using logging server host ip command with the connected interface bound to a VRF. 2)  Verify the configuration using the show command 3) Modify the host address to a IPv6 address and reverify 4)Modify the VRF interface and revierfy |

#### 3.2.13 Configure and verify syslog server over a management VRF

| Test    ID | FtOpSoRoSyslogFun0013                                        |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server over a management VRF            |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using logging server host ip command with the management interface bound to management VRF. 2)  Verify the configuration using the show command 3) Modify the host address to a IPv6 address and reverify |

#### 3.2.14 Configure and verify syslog server over management interface with DHCP enabled

| Test    ID | FtOpSoRoSyslogFun0014                                        |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server over a management VRF with DHCP enabled |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using logging server host ip command with the management interface where the IP is from a DHCP server. 2)  Verify the configuration using the show command 3) Modify the host address to a IPv6 address and reverify |

## 

## 3.3 Negative

#### 3.3.15 Verify remote syslog server with IPv6 host address and IPv4 source-interface address

| Test    ID | FtOpSoRoSyslogFun015                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with IPv6 host address and IPv4 source-interface address |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server with IPv6 host address and IPv4 source-interface address. 2) Verify this will fail |

#### 3.3.16 Verify remote syslog server with mismatched source-interface and vrf values

| Test    ID | FtOpSoRoSyslogFun016                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with mismatched source-interface and vrf values |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using with physical interface as part of Vrf-red and Vrf-blue in the remote server combination. 2) Verify this will fail |

#### 3.3.17 Verify remote syslog server without IP in source-interface

| Test    ID | FtOpSoRoSyslogFun017                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server without IP in source interface   |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server without IP source-interface. 2) Verify this will fail 3) Try adding a non-existing vlan/portchannel as source-interface and verify it will fail |

#### 3.3.18 Delete a vrf when remote syslog server is configured with it

| Test    ID | FtOpSoRoSyslogFun018                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Delete a vrf when remote syslog server is configured with it |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using with physical interface as part of Vrf-red and Vrf-Red in the remote server combination. 2) Unbind the physical interface/delete the Vrf and verify. 3)This should fail |

#### 3.3.19 Delete a source-interface when remote syslog server is configured with it

| Test    ID | FtOpSoRoSyslogFun019                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Delete a source-interface when remote syslog server is configured with it |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using with vlan/portchannel as source-interface. 2) Delete the vlan and verify. 3) Delete the portchannel and verify 4) This should fail |

## 

## 3.4 Reboot

#### 3.4.1 Verify multiple remote syslog server across a cold reboot

| **Test  ID** | **FtOpSoRoSyslogFun020**                                     |
| ------------ | ------------------------------------------------------------ |
| Test Name    | Verify remote syslog server across a cold reboot             |
| Test Setup   | Topology1                                                    |
| Type         | Functional                                                   |
| Steps        | 1) Configure multiple syslog servers with  multiple configurations like with/without source-interface, with/without  VRF 2) Verify the show running config and verify the functionality 3) Verify after a cold reboot that the configs are saved |

## 3.5 Stress,Scale and Performance

## 3.6 Management

### 3.6.1    SNMP
### 3.6.2    gNMI/RESTAPI 


# 4 Reference Links

HLD link - <http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/21020/>≠