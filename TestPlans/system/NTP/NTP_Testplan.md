#  SQA Test Plan
#  SONiC NTP
#  SONiC 3.1 Project and BuzznikPlus Release
[TOC]
# Test Plan Revision History
| Rev  |    Date    |        Author        |      Change Description      |
| :--: | :--------: | :------------------: | :--------------------------: |
| 0.1  | 18/05/2020 | Nagarjuna Suravarapu   |      Initial version         |

# List of Reviewers
| Function |         Name         |
| :------: | :------------------: |
|   Dev    |        Bing Sun   |
|   QA     | Anil Kumar Kolkaleti |
|   QA     |   Giri Babu Sajja    |
|   Auto-infra     |   Kalyan     |

# List of Approvers
| Function  |           Name         | Date Approved |
| :-------: | :--------------------: | :-----------: |
|   Dev     |         Bing Sun      |               |
|   QA      |  Anil Kumar Kolkaleti  |               |
|   QA      |  Giri Babu Sajja       |               |

# Definition/Abbreviation
| **Term** | **Meaning**                                           |
| -------- | ----------------------------------------------------- |
| NTP      | Network Time Protocol          |
| ntpd                       | NTP Daemon |



# Feature Overview
NTP stands for Network Time Protocol. It is used to synchronize the time of a computer or server to another server or refernce time source.

SONiC click CLI provides commands to add/delete the IP address of a remote NTP server. Multiple NTP servers can be configured, and both IPv4 an IPv6 are supported. SONiC click CLI also provides the show command to display status of NTP peers.

With this feature, users will be provided the same capabilities via Management CLI, REST and gNMI using OpenConfig Yang models. It also provides users to configure NTP source and NTP vrf (Management VRF for now?) for addtional ntp control.


# 1 Test Focus Areas
## 1.1 Functional Testing 
  - configure NTP server
  - configure NTP source-interface
  - configure NTP vrf
 
## 1.2 Reboot/Config Testing  
   - Config save and reload

## 1.3 Configuration and Management Requirements
   - CLI style configuration and show commands
   - REST API support
   - gNMI Support

# 2 Test  Case and Objectives
## 2.1 Functional

### 2.1.1 Verify NTP servers(ipv4) are installed correctly in the configDB and reflected in the NTP peers.

| **Test ID**    | **FtOpSoSysNTPFn026**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify NTP servers(ipv4) are installed correctly in the configDB and reflected in the NTP peers.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                      |
| **Steps**      | **Procedure:<br/>1.Configure NTP server.<br/>2.Issue the command "show ntp associations".<br/>3.Issue the command "show clock".<br/>4.Delete NTP server.<br/>Expected Behavior:<br/>1.Verify NTP servers are installed correctly in the configDB.<br/>2.verify NTP servers are reflected in the NTP peers.<br/>3.verify that present time is synced in the Dut. <br/>4.verify NTP servers are deleted succesfully.** |



### 2.1.2 Verify NTP servers(ipv6) are installed correctly in the configDB and reflected in the NTP peers. 

| **Test ID**    | **FtOpSoSysNTPFn027**                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify NTP servers(ipv6) are installed correctly in the configDB and reflected in the NTP peers.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                      |
| **Steps**      | **Procedure:<br/>1.Configure NTP server.<br/>2.Issue the command "show ntp associations".<br/>3.Issue the command "show clock".<br/>4.Delete NTP server.<br/>Expected Behavior:<br/>1.Verify NTP servers are installed correctly in the configDB.<br/>2.verify NTP servers are reflected in the NTP peers.<br/>3.verify that present time is synced in the Dut.<br/>4.verify NTP servers are deleted succesfully.** |



### 2.2.3 Verify NTP functionality with interface/Loopback/vlan/portchannel/management. 
| **Test ID**    | **FtOpSoSysNTPFn028**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify NTP functionality with interface/Loopback/vlan/portchannel/management.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.configure an ntp v4/v6 server.<br/>2.Configure NTP source-interface as (interface/Loopback/vlan/portchannel/management).<br/> 3.Add  v4/v6  ip address to source interface.<br/>4.Issue the command "show ntp associations".<br/>5.Issue the command "show clock".<br/>6.Delete NTP source-interface.<br/>Expected Behavior:<br/>1.Verify NTP server  installed correctly in the configDB.<br/>2.verify that source interface is added correctly.<br/>3.verify that v4/v6 address is added to source-interface. <br/>4.verify NTP source reflected in the NTP peers.<br/>5.verify that present time is synced in the Dut.<br/>6.verify NTP source  deleted succesfully.** |


### 2.1.4   Verify NTP VRF are installed correctly in the configDB and reflected in the NTP peers.

| **Test ID**    | **FtOpSoSysNTPFn029**                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify NTP VRF are installed correctly in the configDB and reflected in the NTP peers.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.Configure NTP VRF.<br/>2.Issue the command "show ntp associations".<br/>3.Delete NTP VRF.<br/>Expected Behavior:<br/>1.Verify NTP VRF are installed correctly in the configDB.<br/>2.verify NTP VRF are reflected in the NTP peers.<br/>3.verify NTP VRF are deleted succesfully.** |


### 2.1.5 . Verify ntp associations are displayed correctly.  

| **Test ID**    | **FtOpSoSysNTPFn030**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify ntp associations are displayed correctly.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.Configure NTP VRF/source/server.<br/>2.Issue the command "show ntp associations".<br/>3.Delete NTP VRF/source/server.<br/>4.Issue the command "show ntp associations".<br/>Expected Behavior:<br/>1.Verify NTP VRF/source/server are installed correctly in the configDB.<br/>2.verify NTP VRF/source/server are reflected in the NTP peers.<br/>3.verify NTP VRF/source/server are deleted succesfully.<br/>4.verify NTP VRF/source/server deletetion are reflected in the NTP peers.**  |


### 2.1.6  Verify that NTP feature is working fine after save and reboot.

| **Test ID**    | **FtOpSoSysNTPFn031**            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that NTP feature is working fine after save and reboot.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure::<br/>1.Configure NTP VRF/source/server.<br/>2.Issue the command "show ntp associations".<br/>3.Issue the command "config save -y and reboot the device."<br/>Expected Behavior:<br/>1.Verify NTP VRF/source/server are installed correctly in the configDB.<br/>2.verify NTP VRF/source/server are reflected in the NTP peers.<br/>3.verify after reboot  NTP VRF/source/server configuration is retained and working fine.** |



### 2.1.7   Verify that NTP configuraton is working fine through the restserver. 


| **Test ID**    | **FtOpSoSysNTPFn032**            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that NTP configuraton is working fine through the restserver.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.Configure NTP VRF/source/server through Rest server.<br/>2.Issue the command "show ntp associations".<br/>3.Issue the command "show clock".<br/>4.Delete NTP VRF/source/server configuration through rest server".<br/>Expected Behavior:1.Verify NTP VRF/source/server are installed correctly in the configDB.<br/>2.verify NTP VRF/source/server are reflected in the NTP peers.<br/>3.verify that  present time is synced in the Dut.<br/>4.verify  that deletion of NTP VRF/source/server are reflected in the configDB.** |



### 2.1.8 Verify that NTP configuraton is working fine through the gnmi.
| **Test ID**    | **FtOpSoSysNTPFn033**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that NTP configuraton is working fine through the gnmi.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.Configure NTP VRF/source/server through gnmi.<br/>2.Issue the command "show ntp associations".<br/>3.Delete NTP VRF/source/server configuration through gnmi".<br/>Expected Behavior:1.Verify NTP VRF/source/server are installed correctly in the configDB.<br/>2.verify NTP VRF/source/server are reflected in the NTP peers.<br/>3.verify  that deletion of NTP VRF/source/server are reflected in the configDB.**  |



### 2.1.9 Verify the behaviour when source-interface is configured but it has no ip4 or ipv6 ntp server address. 
| **Test ID**    | **FtOpSoSysNTPFn035**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the behaviour when source-interface is configured but it has no ip4 or ipv6 ntp server address.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.configure an ntp v4/v6 server.<br/>2.Configure NTP source-interface as (interface/Loopback/vlan/portchannel/management).<br/> 3.Add an v4/v6 ntp server ip to source interface.<br/>4.Issue the command "show ntp associations".<br/>5.Issue the command "show clock".<br/>6.Delete NTP source-interface.<br/>Expected Behavior:<br/>1.Verify NTP source are installed correctly in the configDB.<br/>2.verify that source interface is added correctly.<br/>3.verify that v4/v6 address is added to source- interface. <br/>4.verify NTP source are reflected in the NTP peers.<br/>5.verify that present time is synced in the Dut.<br/>6.verify NTP source are deleted succesfully.** |


### 2.2.0 Verify that the ntp command accepts invalid IPv4 or IPv6 address. 
| **Test ID**    | **FtOpSoSysNTPFn036**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the ntp command accepts invalid IPv4 or IPv6.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.Configure NTP server with invalid IPv4/IPv6 address.<br/>Expected Behavior:<br/>1.Verify  that invalid ipv4/v6 is accepted or not.** |


### 2.2.1 Verify when multiple ntp servers are configured. Does it try to connect in sequence i.e. what happens when first one is not reachable. 
| **Test ID**    | **FtOpSoSysNTPFn039**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify when multiple ntp servers are configured. Does it try to connect in sequence i.e. what happens when first one is not reachable.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.Configure multiple NTP servers.<br/> 2.Delete the first ntp server.<br/>3.check whether it connected to  ntp server in sequence.<br/>Expected Behavior:<br/>1.Verify multiple NTP servers are  created.<br/>2.verify it is deleted or not. <br/>3.verify that is connected in sequence or random.** |

### 2.2.2 Verify NTP functionality when IPv6 address is used as source-ip, and only v4 ntp server is configured and vice versa.
| **Test ID**    | **FtOpSoSysNTPFn038**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify NTP functionality when IPv6 address is used as source-ip, and only v4 ntp server is configured and vice versa.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.configure an ntp v6 server<br/>2.Configure NTP source-interface as (interface/Loopback/vlan/portchannel/management).<br/> 3.Add  v4 ip address to source interface.<br/>4.Issue the command "show ntp associations".<br/>5.Issue the command "show clock".<br/>6.Delete NTP source-interface.<br/>Expected Behavior:<br/>2.Verify NTP source are installed correctly in the configDB.<br/>3.verify that v4/v6 address is added to source-interface. <br/>4.verify NTP source are reflected in the NTP peers.<br/>5.verify that present time is synced in the Dut.<br/>6.verify NTP source are deleted succesfully.** |

### 2.2.3 Check wheather memory leaks happen when  the NTP dont sync for a long time and all the NTP packets are dropped.
| **Test ID**    | **FtOpSoSysNTPFn039**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Check wheather memory leaks happen when  the NTP dont sync for a long time and all the NTP packets are dropped..** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1.configure an  non reachable ntp server.<br/>Expected Behavior:<br/>1.verify that ntp could not get syncd and also check wheather it has got any memory leaks** |

## 4 Reference Links
<https://github.com/project-arlo/SONiC/pull/69/files/dd4ccdf6f82fa2b791d15f51a156a4ec46a1f9d1>
