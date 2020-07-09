#  SQA Test Plan
# Source Intf and Vrf Support for Syslog 
#  SONiC 3.1 Project and Buzznik+ Release
[TOC]



# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 05/20/2020 | Manisha Joshi | Initial version |
| 0.2 | 07/07/2020 | Hafis Saly | Added gNMI/RestAPI test cases |

# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
|          | Suresh Babu |

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
This document provides test cases for the configuration and verification of remote syslog server using KLISH CLI


# 2 Topologies
## 2.1 Topology 1

![Dynamic](syslog.png "Topology 1")

# 3 Test  Case and Objectives
## 3.1 **CLI**
## 3.2 **Functional**
#### 3.2.1     Verify remote syslog server with physical interface as a source-interface

| Test    ID | FtOpSoRoSyslogFun001                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with physical interface as a source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1)Configure an IPv4 address on a physical interface connected to the server 2) Configure the remote syslog server with host address as IPv4 address and physical interface as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration and  shut/no-shut of the interface. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.2     Verify remote syslog server with vlan as a source-interface

| Test    ID | FtOpSoRoSyslogFun002                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with vlan as a source-interface  |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a vlan interface connected to the server 2) Configure the remote syslog server with host address as IPv4 address and vlan as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration and remove/add of the vlan. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.3     Verify remote syslog server with portchannel as a source-interface

| Test    ID | FtOpSoRoSyslogFun003                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with port channel as a source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a portchannel connected to the server 2) Configure the remote syslog server with host address as IPv4 address and portchannel as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration, remove/add member ports and add/delete portchannel. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.4     Verify remote syslog server with loopback as a source-interface

| Test    ID | FtOpSoRoSyslogFun004                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with loopback as a source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a loopback connected to the server 2) Configure the remote syslog server with host address as IPv4 address and loopback as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration and add/delete loopback. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.5     Verify remote syslog server with physical interface as a source-interface over a user-defined VRF

```

```

| Test    ID | FtOpSoRoSyslogFun005                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with physical interface as a source-interface over a user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1)Configure an IPv4 address on a physical interface bound to a VRF  2) Configure the remote syslog server with host address as IPv4 address and physical interface as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration, bind/unbind the interface and remove/add vrf. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.6     Verify remote syslog server with vlan as a source-interface over a user-defined VRF

| Test    ID | FtOpSoRoSyslogFun006                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with vlan as a source-interface over a user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a vlan bound to a vrf 2) Configure the remote syslog server with host address as IPv4 address and vlan as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after  remove/add of the vrf and bind/unbind the vlan. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.7     Verify remote syslog server with portchannel as a source-interface over a user-defined VRF

| Test    ID | FtOpSoRoSyslogFun07                                          |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with port channel as a source-interface as a source-interface over a user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a portchannel bound to a vrf 2) Configure the remote syslog server with host address as IPv4 address and portchannel as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after bind/unbind of portchannel. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.8     Verify remote syslog server with loopback as a source-interface over user-defined VRF

| Test    ID | FtOpSoRoSyslogFun008                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with loopback as a source-interface over user-defined VRF |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure an IPv4 address on a loopback bound to a VRF 2) Configure the remote syslog server with host address as IPv4 address and loopback as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after bind/unbind of the loopback. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |

#### 3.2.9      Configure and verify syslog server after modifying the source-interface

| Test    ID | FtOpSoRoSyslogFun009                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server after modifying the source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using with physical interface. 2) Modify the source interface from a physical interface to a vlan followed by a portchannel and loopback |

#### 3.2.10      Configure and verify syslog server without source-interface configuration

| Test    ID | FtOpSoRoSyslogFun0010                                        |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server without source-interface configuration |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using logging server host ip command. 2)  Verify the configuration using the show command 3) Modify the host address to a IPv6 address and reverify |

#### 3.2.11   Configure and verify syslog server without source-interface configuration over a user-defined VRF

| Test    ID | FtOpSoRoSyslogFun0011                                        |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server without source-interface configuration |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using logging server host ip command with the connected interface bound to a VRF. 2)  Verify the configuration using the show command 3) Modify the host address to a IPv6 address and reverify 4)Modify the VRF interface and revierfy |

## 3.3 Negative

#### 3.3.12      Verify remote syslog server when IPv6 host address and IPv4 source-interface address

| Test    ID | FtOpSoRoSyslogFun012                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server when IPv6 host address and IPv4 source-interface address |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server with IPv6 host address and IPv4 source-interface address. 2) Verify this will fail |

#### 3.3.13      Verify remote syslog server when source-interface and vrf values do not match modifying the source-interface

| Test    ID | FtOpSoRoSyslogFun013                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify syslog server when source-interface and vrf values do not match modifying the source-interface |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1) Configure a remote syslog server using with physical interface as part of vrf-red and vrf as vrf-blue. 2) Verify this will fail |

## 3.4 Reboot

#### 3.4.1    Verify remote syslog server across a cold reboot

| **Test  ID** | **FtOpSoRoSyslogFun014**                                     |
| ------------ | ------------------------------------------------------------ |
| Test Name    | Verify remote syslog server across a cold reboot             |
| Test Setup   | Topology1                                                    |
| Type         | Functional                                                   |
| Steps        | 1) Configure a remote syslog server and verify after a cold reboot |

## 3.5 Stress,Scale and Performance

## 3.6 Management

### 3.6.1    SNMP
### 3.6.2    gNMI/RESTAPI 

####      Verify remote syslog server with physical interface on non default vrf  as a source-interface using RESTAPI/gNMI

| Test    ID | FtOpSoRoSyslogFun001                                         |
| ---------- | ------------------------------------------------------------ |
| Test Name  | Verify remote syslog server with RESTAPI/gNMI OCYang call    |
| Test Setup | Topology1                                                    |
| Type       | Functional                                                   |
| Steps      | 1)Configure an IPv4 address on a physical interface connected to the server 2) Configure the remote syslog server with host address as IPv4 address and physical interface as source interface. 3) Verify using show logging servers command. 4) Re-verify the messages are sent after delete/re-add the server configuration and  shut/no-shut of the interface. 5) Modify the address to another IPv4 address and verify the server picks up the address 6) Capture the packets using tcpdump and verify 7) Reverify the steps with ipv6 address |


# 4 Reference Links

HLD link - <http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/21020/>≠