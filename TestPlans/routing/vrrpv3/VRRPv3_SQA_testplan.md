#  SQA Test Plan
# VRRPv3  IPv4 and IPv6
#  SONiC 3.0 Project and Buzznik Release
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 10/1/2019 | Raghukumar Rampur | Initial version |

# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
|  |   |

# List of Approvers
|  Function | Name | Date Approved|
|:---:|:-----------:|:------------------:|
|  |   |  |

# Definition/Abbreviation
| **Term** | **Meaning**                     |
| -------- | ------------------------------- |
| VRRPv3 | Virtual Router Redundancy Protocol version 3 |
| VR   | Virtual Router |
| VRID  | Virtual Router Identifier |
|          |                                              |


# Feature Overview
The Virtual Router Redundancy Protocol  (VRRP)  functionality is designed to eliminate the single point of failure inherent in the static default routed environment.  VRRP specifies an election protocol that dynamically assigns responsibility of gateway router to one of the VRRP routers on a LAN.  The VRRP router controlling the IP address(es) associated with a virtual router is called the Master, and routes the traffic.  The election process provides dynamic fail-over in the forwarding responsibility should the Master become unavailable.  Any of the virtual router's IP addresses on a LAN can then be used as the default first hop router by end-hosts.  The advantage gained from using VRRP is a higher availability default path without requiring configuration of dynamic routing or router discovery protocols on every end-host.

The Virtual Router Redundancy Protocol version 3 (VRRPv3) functionality is same as like VRRPv2 and it supports IPV6 router redundancy and sub-second timer interval for advertisements.



# 1 Test Focus Areas
## 1.1 Functional Testing 
  - All CLI and debug commands
  - Scaling with max supported of ipv4 routes and 32k ipv6 routes with different prefix sizes
  - Route scaling with all dynamic protocols  + static routes
  - Test on default and user VRFs
## 1.2 Negative Testing 
  -	Link flaps
  -	Reload/kill daemon
  -	Withdraw/advertise routes
  -	Clear bgp neighbors/clear ipv4/v6 route table/clear arp/clear mac
  -	Warm reboot
  -	Orchagt/Syncd restart and upgrade
  -	Fast reboot
  -	Config save and reload

## 1.3 Scale and Performance Testing

# 2 Topologies
## 2.1 Topology 1
![VRRPv3](vrrpv3-topology.png "Figure 1: VRRPv3")



# 3 Test  Case and Objectives
## 3.1 CLI
### 3.1.1     Verify cli for Advertisement-interval, priority, Virtual IP, pre-empt

| **Test ID** | **FtOpSoRoVrrpv3Cli001** |
|--------|:----------------|
| **Test Name** | **To Test the consistency of CLI features of the command Advertisement interval, priority, virtual IP and preempt** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | 1) Verify help strings are displayed while configuring VRRP in cli mode <br/>2) Give the inputs valid and invalid configuration input like complete and incomplete Command, valid and invalid input, within and out of range value <br/> 3) Above step is repeated for “no” form of the command<br /> 4)Try executing half cmd and look for type of info/error message |


### 3.1.2     Verify show vrrp output

| **Test ID**    | **FtOpSoRoVrrpv3Cli002**                                     |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify show vrrp CLI and its output**                      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **CLI**                                                      |
| **Steps**      | 1) Configure VRRPv3 on the DUT1 <br />2) Configure all the required parameters of VRRPv3 and execute show vrrp output<br />3) Make sure all the values are shown in the output <br />4) Above step is repeated by removing the vrrp cofig from the device and verify the show output |

## 3.2 Functional

### 3.2.1	IPv4: Verify VRRP Advertisement interval

| **Test ID**    | **FtOpSoRoVrrpv3Fn003**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Test the default and configured VRRP Advertisement interval ** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure VRRP on the DUT1 and DUT2 with same VRID<br />2) Configure DUT1 with higher priority value so that it becomes Master<br />3) Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4) Verify the V-MAC address learnt on master should be 00-00-5E-00-01-VRID  for IPv4<br />5) Verify the default advertisement interval is 1 sec and default master down interval is 4 sec <br />6)Now change the Advertisement interval to 100 mili-seconds on all the vrrp sessions<br />7)Verify in show vrrp, the Advertisement interval should be 100 mili-seconds<br />8)Revert back Advertisement interval to default value and verif |
### 3.2.2	IPv4: Verify VRRP Priority and track priority -default values

| **Test ID**    | **FtOpSoRoVrrpv3Fn004**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the Default VRRP Priority & user configured priority on a VRRP router** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure VRRP on the DUT1 and DUT2 with same VRID<br />2)Once the VRRP sessions are Established, Verify ‘show vrrp’ , the default priority value should be 100 on all vrrp routers<br />3)Change the priority value to no-default value ‘200’ and verify step 2<br />4)Now revert back the priority to default and verify priority set to 100 on all vrrp routers<br />5)Change virtual IP address to interface ip address of DUT1, Now DUT1 becomes owner Verify the priority is set to 255<br />6)Configure tracking an interface (up-link) and verify default track interface priority<br />7)Verify that the interfaces are tracked are shown in show vrrp with priority<br />8)Shutdown the interface and verify the tracked interface is shown as state =down and priority of VR should be reduced<br />9)Bring-up the interface and verify the tracked interface is shown as state=UP and priority of virtual router is incremented |
### 3.2.3	IPv4: Verify VRRP Owner State

| **Test ID**    | **FtOpSoRoVrrpv3Fn005**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the Default VRRP Priority & user configured priority on a VRRP router** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure VRRP on the DUT1 and DUT2 with same VRID and interface IP address of DUT1 as virtual IP address of VR <br/>2) Once the VRRP sessions are Established, DUT1 becomes master and DUT2 is in backup state <br/>3) Verify ‘show vrrp’ , the  priority value should be 255 on DUT1 <br/>4) Try tracking a port on the owner session, user should be thrown an error message since port tracking is not supported on owners session <br/> 5) Validate the end to end traffic, DUT1 should forward the traffic as it is in Mater state |
### 3.2.4	IPv4: Verify VRRP state with multiple IP

| **Test ID**    | **FtOpSoRoVrrpv3Fn006**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check whether the Master comes up while assigning the secondary IP address of the DUT as the Virtual IP address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure DUT1 interface with primary and secondary IP address <br/>2) Configure  VRRP  group on the DUT1 and DUT2 <br/>3)  Assign the DUT1 secondary IP address of an interface as the Virtual IP address on both the nodes<br/>4) Once the vrrp sessions are established, verify that the highest priority node should be the Master and the rest in backup state <br/>5) Verify that high priority router becomes master and forward the traffic, also verify that the virtual mac-address in show vrrp output<br/>6)  Now delete secondary IP address assigned to DUT1 interface <br/>7) There should not be any master/backup toggle and traffic should not be disturbed<br/>8) Try adding back the deleted secondary ip address and verify that result should be same as mentioned in step7 <br/> |
### 3.2.5	IPv4: To check the load Balancing and redundancy behavior of the virtual routers

| **Test ID**    | **FtOpSoRoVrrpv3Fn007**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the load Balancing and redundancy behavior of the virtual routers** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 and DUT2  with a VRRP group - 100. Assign the DUT1 interface address as the virtual IP address for the VRRP group 100 as a result DUT1 will become Master and DUT2 becomes backup for VRRP group 100<br/>2. Configure the Default route for the Host A has the DUT1 interface <br/>3. Configure the DUT1 and DUT2 with another VRRP group say 200 <br/>4. Assign the DUT2 interface address as the virtual IP address for the VRRP group 200 as a result DUT2 will become Master and DUT1 becomes backup for VRRP 200<br/>5. Configure the Default route for the Host B has the DUT2 interface<br/>6. Send continuous packets from both Host A and Host B  <br/>7. Debug the packets and check the path it takes  <br/>8. Shutdown the DUT1 interface, now debug the packets from Host A and check the path it takes to reach Host B<br/>9. Bring back the DUT1 interface and now shutdown the DUT2 interface <br/>10. Debug the packets from Host A and check the path it takes to reach Host B<br/>11. Bring back the DUT2  interface and check the packets transmitted from both Host A to Host B<br/>12. Increase the  priority on DUT1 such that it becomes master for both VRRP group<br/>13. Disable VRRP on the session VRRP 200 ,  verify that only VRRP group 200 is disabled and DUT1 continues to be master  for VRRP 100<br/>14. Enable the session and verify that DUT1 becomes master for VRRP 200 and continues to be the master for VRRP 100 <br/>15. Restart the VRRP docker on the Master, during the docker restart make sure the Back up takes over <br/>16. Once the docker is restarted verify the original Master is back and should start forwarding the traffic<br/>17. Similarly try config reload on both Master and backup, make sure during the config reload process backup should become master and traffic should be forwarded |
### 3.2.6	IPv4: Verify VRRP on an interface which has both IPv4 & IPv6 Ip address (dual stack)

| **Test ID**    | **FtOpSoRoVrrpv3Fn008**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify VRRP state by configuring an interface with both ipv4 and ipv6 ip address ** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | Configure DUT1 and DUT2 interfaces with both ipv4 and ipv6 address<br/>Configure  VRRP  group on the DUT1 and DUT2 interfaces having dual stack<br/>Once the vrrp sessions are established, verify that the highest priority node should be the Master and the rest in backup state<br/>Verify that high priority router becomes master and forward the traffic, also verify that the virtual mac-address in show vrrp output<br/>Change the priority value to no-default value ‘200’ on DUT2<br/>The backup router should become Master as it is having higher priority than DUT1<br/>Now revert back the priority to default on DUT2 and verify priority set to 100 on DUT2<br/>Traffic is forwarded by DUT1 as it becomes Master node<br/>Change virtual IP address to interface ip address of DUT1, Now DUT1 becomes owner,Verify the priority is set to 255<br/>Validate end to end traffic<br/>Validate the VRRP state by triggers like Docker restart, reboot, warm boot and config reload<br/>Verify the traffic stats with above triggers<br/> |
### 3.2.7	IPv4: Verify VRRP advertisement interval

| **Test ID**    | **FtOpSoRoVrrpv3Fn009**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Test the configured VRRP sub sec Advertisement-interval**  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID<br/>2. Configure DUT1 with higher priority value so that it becomes Master<br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state  and other node should be in backup state<br/>4. Verify the V-MAC address learnt on master should be 00-00-5E-00-02-VRID  for IPv6  V-MAC 00-00-5E-00-01-VRID  for IPv4<br/>5. Verify the default advertisement interval is 1 sec and default master down interval is 4 sec<br/>6. Now change the Advertisement interval to 100 seconds on all the vrrp sessions<br/>7. Verify in show vrrp, the Advertisement interval should be 100 seconds <br/>8. Revert back Advertisement interval to default value and verify<br /> |
### 3.2.8	IPv4: Verify VRRP on both default and non_default vrf

| **Test ID**    | **FtOpSoRoVrrpv3Fn010**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify vrrp function with user defined vrf (non-default vrf)** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 and DUT2 interface with non-default vrf<br/>2. Configure  VRRP  group on the DUT1 and DUT2 interfaces having vrf config<br />3. Once the vrrp sessions are established, verify that the highest priority node should be the Master and the rest in backup state<br/>4. Verify that high priority router becomes master and forward the traffic, also verify that the virtual mac-address in show vrrp output<br/>5. Change the priority value to no-default value ‘200’ on DUT2<br/>6. The backup router should become Master as it is having higher priority than DUT1<br/>7. Now revert back the priority to default on DUT2 and verify priority set to 100 on DUT2<br/>8. Traffic is forwarded by DUT1 as it becomes Master node<br/>9. Change virtual IP address to interface ip address of DUT1, Now DUT1 becomes owner <br/>10. Verify the priority is set to 255<br/>11. Validate end to end traffic <br/>12. Validate the VRRP state by triggers like Docker restart, reboot, warm boot and config reload <br/>13. Verify the traffic stats with above triggers<br/> |
### 3.2.9	IPv4: Validate Default VRRP Priority of VRRP owner router

| **Test ID**    | **FtOpSoRoVrrpv3Fn011**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the Default VRRP Priority of VRRP owner router**  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1.Configure VRRP on the DUT1 and DUT2 with same VRID and interface IP address of DUT1 as virtual IP address of VR<br />2. Once the VRRP sessions are Established, DUT becomes master and DUT2 is in backup state<br />3. Verify ‘show vrrp’ , the  priority value should be 255 on DUT1<br />4. Try tracking a port on the owner session , user should be thrown an error message since port tracking is not supported on owners session<br />5. Validate the end to end traffic, DUT1 should forward the traffic as it is in Mater state |
### 3.2.10	IPv4: Validate whether the Master sends the Gratuitous ARP at the beginning

| **Test ID**    | **FtOpSoRoVrrpv3Fn012**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To test whether the Master send the Gratuitous ARP**       |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. Debug the VRRP packets on DUT1 and capture pkt on the spirent, Master should be sending gARP<br />5. Disable VRRP session on Master and Enable it again<br />6. Verify that master should be sending gARP once it comes up<br />7. The backup router should not respond request to Gratuitous ARP |
### 3.2.11	IPv4: Verify whether the Master respond to ARP request for VRIP

| **Test ID**    | **FtOpSoRoVrrpv3Fn013**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To test whether the Master respond to ARP request for VRIP** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. Send ARP request from Spirent destined to Virtual router IP address (Master)<br />5. Capture the ARP response and verify it has vMAC<br />6. Send same ARP request to backup device virtual IP address<br />7. There should not be any response from backup router<br />8. Create 2 different VRRP sessions(10, 20) on DUT1 , for one session it is master for others it is in backup state<br />9. Now send ARP request to DUT1 for both the IP address associated with VR session 10 and 20<br />10. Verify that DUT1 responds only to VR 10 and does not respond to VRRP 20 since it is in backup state |
### 3.2.12	IPv4: The Master should accepts packets destined to IP addresses associated even if it is not the IP address owner

| **Test ID**    | **FtOpSoRoVrrpv3Fn014**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **The Master should accepts packets destined to IP addresses associated even if it is not the IP address owner** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. Send ICMP echo request to the Master VR<br />5. Verify whether Master VR reply to that echo request<br />6. The Master VR should accept the packets and reply to the ICMP echo request<br />7. Verify the Virtual IP address should be programmed in the routing table with /32 subnet |
### 3.2.13	IPv4:  Mater Switchover To Backup state  (after receiving higher priority advertisement)

| **Test ID**    | **FtOpSoRoVrrpv3Fn015**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **On receipt of Advertisement interval with a higher priority move to Backup state** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. The Master node should forward the traffic<br />5. If the Master node receives an Advertisement packet with priority higher than its local priority, it should transitioned to backup state<br />6. On receipt of Advertisement with a Lower priority, Master should remain in Master State if priority in the Advertisement is less than local priority<br />7. On receipt of Advertisement with an equal priority and a higher Primary IP address the Master should move to Backup state<br />8. On receipt of Advertisement with an equal priority and a lesser Primary IP address the Master should remain in Master State |
### 3.2.14	IPv4: Validate whether Backup router respond to ARP request for the IP address associated with the Virtual router

| **Test ID**    | **FtOpSoRoVrrpv3Fn016**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To test whether the Backup respond to ARP request for the IP address associated with the Virtual router** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. Send ARP request from Spirent destined to Virtual router IP address of DUT2 (backup node)<br />5. The backup virtual router should neither respond nor forward the ARP request for ip address associated with virtual router |
### 3.2.15	IPv4: The Backup router should not accept packet destined to IP address associated to Virtual IP address

| **Test ID**    | **FtOpSoRoVrrpv3Fn017**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **The Backup should not accept packet destined to IP address associated to Virtual IP address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. Enable VRRP debug on both the nodes<br />5. The backup router should not accept packets destined to IP address associated with VR<br />6. Verify the MAC table<br />7. The virtual IP address should not present in the ip routing table since it is a backup router<br />8. Verify only Mater node should forward the traffic |
### 3.2.16	IPv4: Preemption-enable:: VRRP Backup router Switchover To Mater state

| **Test ID**    | **FtOpSoRoVrrpv3Fn018**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Preemption-enable: The Backup should transition to Master state on reception of the Advertisement with Priority less than local priority and Preemption mode true** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure 2 VRRP sessions on the DUT1 and DUT2<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. Verify that by default preempt mode is in Enabled state on both the nodes<br />5. Change the priority of 2 vrrp sessions to 200 in backup node (DUT2)<br />6. Verify that DUT2 becomes master for all the vrrp sessions<br />7. Send the traffic , now DUT2 should forward the traffic<br />8. Revert back priority on DUT2 to default value (100)<br />9. Verify that DUT1 becomes master for all the vrrp sessions<br />10. Send traffic, Now DUT1 should forward the traffic |
### 3.2.17	IPv4: Preemption-disable:: VRRP Backup router should not Switchover To Mater state even though it has the higher priority

| **Test ID**    | **FtOpSoRoVrrpv3Fn019**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Preemption-Disable: The Backup should not transition to Master state on reception of the Advertisement with Priority less than local priority and Preemption mode is in disabled state** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure 2 VRRP sessions on the DUT1 and DUT2<br />2. Configure DUT1 with higher priority value so that it becomes Master<br />3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state<br />4. On DUT2 disable the preempt mode (by default it is True)<br />5. Verify that preempt mode is in Disabled state on DUT2<br />6. Change the priority of 2 vrrp sessions to 200 in backup node<br />7. Verify that DUT2 still in the backup state for all the vrrp sessions (Preempt is disabled)<br />8. Send the traffic, DUT1 should forward the traffic as DUT2 is in Backup state |
### 3.2.18	IPv4: Validate whether Master comes up while assigning the secondary IP address of the DUT as the Virtual IP address

| **Test ID**    | **FtOpSoRoVrrpv3Fn020**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check whether the Master comes up while assigning the secondary IP address of the DUT as the Virtual IP address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 interface with primary and secondary IP address<br/>2. Configure  VRRP  group on the DUT1<br/>3.  Assign the DUT1 secondary IP address of an interface as the Virtual IP address <br/>4. Enable vrrp debug<br/>5. Capture the Advertisement packet on Spirent and verify the source IP address<br/>6. Though the Virtual IP address is assigned as secondary IP address, but still the source IP address of the Master should be the primary IP address<br/>7. Execute clear VRRP statistics and verify vrrpv3 counters are cleared<br/>8. Delete the secondary IP address assigned on DUT1 interface<br/>9. After deleting,  DUT1 should remain in Master state<br/>10. Check vrrp packet and verify source IP address<br/>11. Now delete the primary Ip address on DUT1 interface, verify DUT1 stops participating in the vrrp session |

### 3.2.19	IPv4: Validate whether the DUT allows configuring same Virtual IP address for different VRRP group

| **Test ID**    | **FtOpSoRoVrrpv3Fn021**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check whether the DUT allows configuring same Virtual IP address for different VRRP group** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      |                                                              |
| **Steps**      | 1. Assign Virtual IP address as 10.10.10.1 <br/>2. Enable vrrp debug<br/>3. Configure another VRRP group on DUT1 and assign same IP address 10.10.10.1 as Virtual IP address<br/>4. The VRRP node should not allow configuring the same virtual IP address on different VRRP group<br/>5. User should get an error/ info message while assigning the same virtual IP address to different vrrp group<br/> |

### 3.2.20 IPv4: To verify the behavior of the virtual routers when the Virtual IP address owner keeps going down while the traffic is continuously on

| **Test ID**    | **FtOpSoRoVrrpv3Fn022**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify the behavior of the virtual routers when the Virtual IP address owner keeps going down while the traffic is continuously on** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRRP group<br/>2.  Assign the DUT1 interface address as the Virtual IP Address for both DUT<br/>3. Once the VRRP session is enabled with DUT1 has the master and DUT2  has the Backup state<br/>4. The IP packets transmitted from Host A should be forwarded by DUT1 to Host B<br/>5. Ping continuously from Host A and Debug the packets on both interfaces<br/>6. Make sure there is only one ARP response<br/>7. Now shutdown the master and observe the path the packet takes<br/>8. Bring back the Master after say 5 seconds and observe the packet path<br/>9. When the master is shutdown the IP packets transmitted from Host A to Host B should be forwarded by DUT2<br/>10. once the Master comes back the DUT1 should start forwarding the packets<br/>11. now again shutdown the Master, Bring back the master again, like this  keep on shutting down and bring back the Master for every 5 seconds |

### 3.2.21	IPv4: To verify VRRP protocol on Ve , port-channel & physical interfaces

| **Test ID**    | **FtOpSoRoVrrpv3Fn023**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify VRRP protocol on Ve & physical interfaces**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 and DUT2  with a VRRP group say 100<br/>2.  Assign the DUT1 interface to vlan 100, create a Ve 100 interface assign the IP address say 10.10.10.10/24 and the same address as the virtual IP address so that DUT1 will become Master and DUT2 becomes backup for VRRP 100<br/>3. Configure the DUT1 and DUT2 with another VRRP group say 200.<br/>4. Assign the DUT2 interface address as the virtual IP address for the VRRP group 200 as a result DUT2  will become Master and DUT1 becomes backup for VRRP 200.<br/>5. Enable the VRRP session<br/>6. Send continuous packets from both Host A and Host B to Host C.<br/>7. Debug the packets and check the path it takes to reach Host C.<br/>8.  Shutdown the DUT1 interfaces (port assigned to ve) and verifies that DUT2 becomes the master for both the VRRP group’s 100 & 200.<br/>9. Bring back the DUT1 interface and verify that DUT1 regains its mastership and DUT2 continues to be master for VRRP group 200<br/>10. Similarly, do shut/no shut on Ve interface and verify the same.<br/>11. During these process, let the continuous traffic be running and verify that master should transmit the packets to the host<br/>12. Repeat the above steps from 5 – 10 by enabling tracking on the physical interface assigned to ve interface |

### 3.2.22	IPv4: To verify track port with different weight while flapping the track port

| **Test ID**    | **FtOpSoRoVrrpv3Fn024**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify track port with different weight while flapping the track port** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure few VRRP session on both DUT1 & DUT2 in such a way that DUT1 is master for few sessions whereas DUT2 is master for rest of the sessions<br/>2. Track the uplink ports which includes a Port-channel, physical & Ve interface.<br/>3. Verify the priority are shown as configured since all the track ports are up<br/>4. Start the traffic and make sure Master is forwarding the traffic for all the sessions<br/>5. Start shutting down the track port one by one in such a way that the priority is reduced below backup and back up takes over <br/>6. Verify the new master is forwarding the traffic and the priority on the back up is reduced to the weight on the track interface.<br/>7. Bring back the track interface up one by one and verify the backup becomes master Flap the track interface multiple times and verify the priority is reverted back as expected |

### 3.2.23 IPv4: To verify track port with higher weight and invalid interface

| **Test ID**    | **FtOpSoRoVrrpv3Fn025**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify track port with higher weights and invalid interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1.	Configure few VRRP session on both DUT1 & DUT2 in such a way that DUT1 is master for few sessions whereas DUT2 is master for rest.<br/>2.Track the uplink ports which includes a Port-channel, physical & Vlan interface which are not present in the  system<br/>3.	Verify the priority is reduced by the weight since the ports are considered down due to non-existent<br/>4.	Bring the ports online by assigning the ports part of port-channel and vlan interface  and verify the current priority is same as configured priority since the ports are up.<br/> 5.Start the traffic and make sure Master is forwarding the traffic for all the sessions<br/>6.	Try configuring the weight greater than configured priority, the cli should throw an error since bring the port down will set the priority to negative<br/>7.	Track port greater than 8 numbers and verify suitable errors are shown. <br/>8.	Track both port which is part of port-channel and port-channel it self, removing the port from port-channel will bring the port-channel down but the physical port will be up<br/>9.	Make sure only the weight w.r.t port-channel is reduced <br/>10.	Similarly try the same experiment on the interface part of vlan interface too |

### 3.2.24 IPv4: To verify the docker restart, reboot, config reload , warm boot case

| **Test ID**    | **FtOpSoRoVrrpv3Fn026**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **verify the docker restart, reboot, config reload , warm boot case** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 and DUT2  with a VRRP group say 100<br/>2. Assign the DUT1 interface address as the virtual IP address for the VRRP group 100 as a result DUT1 will become Master and DUT2 becomes backup for VRRP 100.<br/>3. Configure the Default route for the Host A has the DUT1 interface.<br/>4. Configure the DUT1 and DUT2 with another VRRP group say 200.<br/>5. Assign the DUT2 interface address as the virtual IP address for the VRRP group 200 as a result DUT2 will become Master and DUT1 becomes backup for VRRP 200.<br/>6. Configure the Default route for the Host B has the DUT2 interface<br/>7. Send continuous packets from both Host A and Host B <br/>8. Start triggers like warm boot, reload , config reload and Docker restart<br/>9. Validate the VRRP functionality after each trigger <br/>10. Verify Traffic end to end after each trigger |

### 3.2.25	IPv6: Verify VRRP Advertisement interval -default values

| **Test ID**    | **FtOpSoRoVrrpv3Fn027**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Test the default and configured VRRP Advertisement interval** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure VRRP on the DUT1 and DUT2 with same VRID <br/>2) Configure DUT1 with higher priority value so that it becomes Master <br/>3) Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state <br/>4) Verify the V-MAC address learnt on master should be 00-00-5E-00-02-VRID  for IPv6 <br/>5) Verify the default advertisement interval is 1 sec and default master down interval is 4 sec <br/>6) Now change the Advertisement interval to 100 mili-seconds on all the vrrp sessions <br/>7) Verify in show vrrp, the Advertisement interval should be 100 mili-seconds <br/>8) Revert back Advertisement interval to default value and verify <br/>9) Now switch the VRRP version to 2 and validate only IPv4 address family is supported<br/>10) Again revert back to version 3 and validate both IPv4/IPv6 address family are supported |

### 3.2.26	IPv6: Verify VRRP Priority and track priority -default values

| **Test ID**    | **FtOpSoRoVrrpv3Fn028**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the Default VRRP Priority & user configured priority on a VRRP router** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure VRRP on the DUT1 and DUT2 with same VRID <br/>2)Once the VRRPv3 sessions are Established, Verify ‘show vrrp’ , the default priority value should be 100 on all vrrp routers<br/> 3)Change the priority value to no-default value ‘200’ and verify step 2<br/>4) Now revert back the priority to default and verify priority set to 100 on all vrrp routers<br/>5) Change virtual IP address to interface ip address of DUT1, Now DUT1 becomes owner Verify the priority is set to 255 <br/>6)Configure tracking an interface (up-link) and verify default track interface priority <br/>7)Verify that the interfaces are tracked are shown in show vrrp with priority <br/>8)Shutdown the interface and verify the tracked interface is shown as state =down and priority of VR should be reduced<br/>9) Bringup the interface and verify the tracked interface is shown as state=UP and priority of virtual router is incremented |

### 3.2.27	IPv6: Verify VRRP Owner State

| **Test ID**    | **FtOpSoRoVrrpv3Fn029**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the Default VRRP Priority & user configured priority on a VRRP router** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure VRRP on the DUT1 and DUT2 with same VRID and interface IP address of DUT1 as virtual IP address of VR <br/>2) Once the VRRP sessions are Established, DUT1 becomes master and DUT2 is in backup state<br/> 3) Verify ‘show vrrp’ , the  priority value should be 255 on DUT1 <br/>4) Try tracking a port on the owner session, user should be thrown an error message since port tracking is not supported on owners session<br/>5) Validate the end to end traffic, DUT1 should forward the traffic as it is in Mater state |

### 3.2.28	IPv6: Verify VRRP state with multiple IP

| **Test ID**    | **FtOpSoRoVrrpv3Fn030**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check whether the Master comes up while assigning the secondary IP address of the DUT as the Virtual IP address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure DUT1 interface with primary and secondary IP address <br/>2) Configure  VRRP  group on the DUT1 and DUT2 <br/>3)  Assign the DUT1 secondary IP address of an interface as the Virtual IP address on both the nodes <br/>4) Once the vrrp sessions are established, verify that the highest priority node should be the Master and the rest in backup state <br/>5) Verify that high priority router becomes master and forward the traffic, also verify that the virtual mac-address in show vrrp output <br/>6)  Now delete secondary IP address assigned to DUT1 interface <br/>7) There should not be any master/backup toggle and traffic should not be disturbed <br/>8) Try adding back the deleted secondary ip address and verify that result should be same as mentioned in step7 |

### 3.2.29	IPv6: To check the load Balancing and redundancy behavior of the virtual routers

| **Test ID**    | **FtOpSoRoVrrpv3Fn031**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the load Balancing and redundancy behavior of the virtual routers** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1.Configure the DUT1 and DUT2  with a VRRP group - 100 <br/>2.Assign the DUT1 interface address as the virtual IP address for the VRRP group 100 as a result DUT1 will become Master and DUT2 becomes backup for VRRPv3 group 100 <br/>3.Configure the Default route for the Host A has the DUT1 interface <br/>4.Configure the DUT1 and DUT2 with another VRRP group say 200 <br/>5.Assign the DUT2 interface address as the virtual IP address for the VRRP group 200 as a result DUT2 will become Master and DUT1 becomes backup for VRRP 200 <br/>6.Configure the Default route for the Host B has the DUT2 interface. <br/>7.Enable the VRRP session <br/>8.Send continuous packets from both Host A and Host B <br/>9.Debug the packets and check the path it takes to reach Host C. <br/>10.	Shutdown the DUT1 interface, now debug the packets from Host A and check the path it takes to reach Host C <br/>11.	Bring back the DUT1 interface and now shutdown the DUT2 interface. <br/>12.	Debug the packets from Host A and check the path it takes to reach Host C  <br/>13.	Bring back the DUT2  interface and check the packets transmitted from both Host A and Host B <br/>14.Increase the  priority on DUT1 such that it becomes master for both VRRPv3 group <br/>15.Disable VRRPv3 on the session VRRP 200 ,  verify that only VRRP group 200 is disabled and DUT1 continues to be master  for VRRPv3 100 <br/>16.Enable the session and verify that DUT1 becomes master for VRRPv3 200 and continues to be the master for VRRP 100. <br/>17.	Restart the VRRP docker on the Master, during the docker restart make sure the Back up takes over <br/>18.Once the docker is restarted verify the original Master is back and should start forwarding the traffic <br/>19.	Similarly try config reload on both Master and backup, make sure during the config reload process backup should become master and traffic should be forwarded |

### 3.2.30	IPv6: Verify VRRP on an interface which has both IPV4 & IPV6 Ip address (dual stack)

| **Test ID**    | **FtOpSoRoVrrpv3Fn032**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify VRRP state by configuring an interface with both ipv4 and ipv6 ip address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 and DUT2 interfaces with both ipv4 and ipv6 address <br/>2. Configure  VRRP  group on the DUT1 and DUT2 interfaces having dual stack <br/>3. Once the vrrp sessions are established, verify that the highest priority node should be the Master and the rest in backup state <br/>4. Verify that high priority router becomes master and forward the traffic, also verify that the virtual mac-address in show vrrp output <br/>5.  Change the priority value to no-default value ‘200’ on DUT2 <br/>6. The backup router should become Master as it is having higher priority than DUT1  <br/>7. Now revert back the priority to default on DUT2 and verify priority set to 100 on DUT2 <br/>8. Traffic is forwarded by DUT1 as it becomes Master node <br/>9. Change virtual IP address to interface ip address of DUT1, Now DUT1 becomes owner  Verify the priority is set to 255  <br/>10.Validate end to end traffic   <br/>11.Validate the VRRP state by triggers like Docker restart, reboot, warm boot and config reload <br/>12.Verify the traffic stats with above triggers |

### 3.2.31	IPv6: Verify VRRP advertisement interval

| **Test ID**    | **FtOpSoRoVrrpv3Fn033**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Test the configured VRRP Advertisement-interval**          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID <br/>2. Configure DUT1 with higher priority value so that it becomes Master <br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state <br/>4. Verify the V-MAC address learnt on master should be 00-00-5E-00-02-VRID  for IPv6 <br/>5. Verify the default advertisement interval is 1 sec and default master down interval is 4 sec <br/>6. Now change the Advertisement interval to 100 seconds on all the vrrp sessions <br/>7. Verify in show vrrp, the Advertisement interval should be 100 seconds  <br/>8. Revert back Advertisement interval to default value and verify |

### 3.2.32	IPv6: Verify VRRP on both default and non_default vrf

| **Test ID**    | **FtOpSoRoVrrpv3Fn034**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify vrrp function with user defined vrf (non-default vrf)** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 and DUT2 interface with non-default vrf <br/>2. Configure  VRRP  group on the DUT1 and DUT2 interfaces having vrf config <br/>3. Once the vrrp sessions are established, verify that the highest priority node should be the Master and the rest in backup state <br/>4. Verify that high priority router becomes master and forward the traffic, also verify that the virtual mac-address in show vrrp output  <br/>5.  Change the priority value to no-default value ‘200’ on DUT2 <br/>6. The backup router should become Master as it is having higher priority than DUT1  <br/>7. Now revert back the priority to default on DUT2 and verify priority set to 100 on DUT2 <br/>8. Traffic is forwarded by DUT1 as it becomes Master node <br/>9. Change virtual IP address to interface ip address of DUT1, Now DUT1 becomes owner Verify the priority is set to 255  <br/>10. Validate end to end traffic   <br/>11. Validate the VRRP state by triggers like Docker restart, reboot, warm boot and config reload<br/>12. Verify the traffic stats with above triggers |

### 3.2.33	IPv6: Validate Default VRRP Priority of VRRP owner router

| **Test ID**    | **FtOpSoRoVrrpv3Fn035**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check the Default VRRP Priority of VRRP owner router**  |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID and interface IP address of DUT1 as virtual IP address of  VR <br/>2. Once the VRRP sessions are Established, DUT becomes master and DUT2 is in backup state<br/> 3. Verify ‘show vrrp’ , the  priority value should be 255 on DUT1 <br/>4. Try tracking a port on the owner session , user should be thrown an error message since port tracking is not supported on owners session  <br/>5. Validate the end to end traffic, DUT1 should forward the traffic as it is in Mater state |

### 3.2.34	IPv6: Verify whether the Master respond to nd request for VRIP

| **Test ID**    | **FtOpSoRoVrrpv3Fn036**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To test whether the Master respond to ARP request for VRIP** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID <br/>2. Configure DUT1 with higher priority value so that it becomes Master <br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state  <br/>4. Send ND request from Spirent destined to Virtual router IP address (Master) <br/>5. Capture the ND response and verify it has vMAC   <br/>6. Send same ND request to backup device virtual IP address  <br/>7. There should not be any response from backup router  <br/>8. Create 2 different VRRP sessions(10, 20) on DUT1 , for one session it is master for others it is in backup state <br/>9.Now send ND request to DUT1 for both the IP address associated with VR session 10 and 20  <br/>10. Verify that DUT1 responds only to VR 10 and does not respond to VRRP 20 since it is in backup state |

### 3.2.35	IPv6: The Master should accepts packets destined to IP addresses associated even if it is not the IP address owner

| **Test ID**    | **FtOpSoRoVrrpv3Fn037**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **The Master should accepts packets destined to IP addresses associated even if it is not the IP address owner** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID  <br/>2. Configure DUT1 with higher priority value so that it becomes Master <br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state  <br/>4. Send ICMPv6 echo request to the Master VR  <br/>5. Verify whether Master VR reply to that echo request  <br/>6. The Master VR should accept the packets and reply to the ICMPv6 echo request  <br/>7. Verify the Virtual IP address should be programmed in the routing table with /64 subnet |

### 3.2.36	IPv6: VRRP  Mater Switchover To Backup state  (by changing priority)

| **Test ID**    | **FtOpSoRoVrrpv3Fn038**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **On receipt of Advertisement interval with a higher priority move to Backup state** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID <br/>2. Configure DUT1 with higher priority value so that it becomes Master <br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state  <br/>4.The Master node should fwd the traffic  <br/>5. If the Master node receives an Advertisement packet with priority higher than its local priority, it should transitioned to backup state  <br/>6. On receipt of Advertisement with a Lower priority, Master should remain in Master State if priority in the Advertisement is less than local priority  <br/>7. On receipt of Advertisement with an equal priority and a higher Primary IP address the Master should move to Backup state  <br/>8. On receipt of Advertisement with an equal priority and a lesser Primary IP address the Master should remain in Master State |

### 3.2.37	IPv6: The Backup router should not accept packet destined to IP address associated to Virtual IP address

| **Test ID**    | **FtOpSoRoVrrpv3Fn039**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **The Backup should not accept packet destined to IP address associated to Virtual IP address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRID <br/>2. Configure DUT1 with higher priority value so that it becomes Master <br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state <br/>4. Enable VRRP debug on both the nodes  <br/>5. The backup router should not accept packets destined to IP address associated with VR  <br/>6. Verify the MAC table  <br/>7. The virtual IP address should not present in the ip routing table since it is a backup router  <br/>8. Verify only Mater node should forward the traffic |

### 3.2.38	IPv6: Preemption-enable:: VRRP Backup router Switchover To Mater state

| **Test ID**    | **FtOpSoRoVrrpv3Fn040**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Preemption-enable: The Backup should transition to Master state on reception of the Advertisement with Priority less than local priority and Preemption mode true** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure 2 VRRP sessions on the DUT1 and DUT2 <br/>2. Configure DUT1 with higher priority value so that it becomes Master <br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state  <br/>4. Verify that by default preempt mode is in Enabled state on both the nodes  <br/>5. Change the priority of 2 vrrp sessions to 200 in backup node (DUT2)  <br/>6. Verify that DUT2 becomes master for all the vrrp sessions <br/>7. Send the traffic , now DUT2 should forward the traffic <br/>8. Revert back priority on DUT2 to default value (100)  <br/>9. Verify that DUT1 becomes master for all the vrrp sessions  <br/>10. Send traffic, Now DUT1 should forward the traffic |

### 3.2.39	IPv6: Preemption-disable:: VRRP Backup router should not Switchover To Mater state even though it has the higher priority

| **Test ID**    | **FtOpSoRoVrrpv3Fn041**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Preemption-Disable: The Backup should not transition to Master state on reception of the Advertisement with Priority less than local priority and Preemption mode is in disabled state** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure 2 VRRP sessions on the DUT1 and DUT2  <br/>2. Configure DUT1 with higher priority value so that it becomes Master <br/>3. Once the VRRP sessions are Established, Verify that the highest priority node should be the master state and other node should be in backup state  <br/>4. On DUT2 disable the preempt mode (by default it is True)  <br/>4. Verify that preempt mode is in Disabled state on DUT2  <br/>5. Change the priority of 2 vrrp sessions to 200 in backup node <br/>6. Verify that DUT2 still in the backup state for all the vrrp sessions (Preempt is disabled)   <br/>7. Send the traffic, DUT1 should forward the traffic as DUT2 is in Backup state |

### 3.2.40	IPv6: Validate whether Master comes up while assigning the secondary IP address of the DUT as the Virtual IP address

| **Test ID**    | **FtOpSoRoVrrpv3Fn042**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check whether the Master comes up while assigning the secondary IP address of the DUT as the Virtual IP address** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 interface with primary and secondary IP address <br/>2. Configure  VRRP  group on the DUT1 <br/>3.  Assign the DUT1 secondary IP address of an interface as the Virtual IP address <br/>4. Enable vrrp debug <br/>5. Capture the Advertisement packet on Spirent and verify the source IP address <br/>6. Though the Virtual IP address is assigned as secondary IP address, but still the source IP address of the Master should be the primary IP address  <br/>7. Execute clear VRRP statistics and verify vrrpv3 counters are cleared <br/>8. Delete the secondary IP address assigned on DUT1 interface <br/>9. After deleting,  DUT1 should remain in Master state  <br/>10. Check vrrp packet and verify source IP address <br/>11. Now delete the primary Ip address on DUT1 interface, verify DUT1 stops participating in the vrrp session |

### 3.2.41	IPv6: Validate whether the DUT allows configuring same Virtual IP address for different VRRP group

| **Test ID**    | **FtOpSoRoVrrpv3Fn043**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To check whether the DUT allows configuring same Virtual IP address for different VRRP group** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP group on the DUT1 <br/>2. Assign Virtual IP address as 3001::10 <br/>3. Enable vrrp debug  <br/>4. Configure another VRRP group on DUT1 and assign same IP address 3001::10 as Virtual IP address <br/>5. The VRRP node should not allow configuring the same virtual IP address on different VRRP group <br/>6. User should get an error/ info message while assigning the same virtual IP address to different vrrp group |

### 3.2.42	IPv6: To verify the behavior of the virtual routers when the Virtual IP address owner keeps going down while the traffic is continuously on

| **Test ID**    | **FtOpSoRoVrrpv3Fn044**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify the behavior of the virtual routers when the Virtual IP address owner keeps going down while the traffic is continuously on** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure VRRP on the DUT1 and DUT2 with same VRRP group <br/>2.  Assign the DUT1 interface address as the Virtual IP Address for both DUT <br/>3. Once the VRRP session is enabled with DUT1 has the master and DUT2  has the Backup state<br/>4. The IP packets transmitted from Host A should be forwarded by DUT1 to Host B  <br/>5. Ping continuously from Host A and Debug the packets on both interfaces <br/>6. Make sure there is only one ARP response  <br/>7. Now shutdown the master and observe the path the packet takes <br/>8. Bring back the Master after say 5 seconds and observe the packet path  <br/>9. When the master is shutdown the IP packets transmitted from Host A to Host B should be forwarded by DUT2 <br/>10. once the Master comes back the DUT1 should start forwarding the packets <br/>11. now again shutdown the Master, Bring back the master again, like this  keep on shutting down and bring back the Master for every 5 seconds |

### 3.2.43	IPv6: To verify VRRP protocol on Ve , port-channel & physical interfaces

| **Test ID**    | **FtOpSoRoVrrpv3Fn045**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify VRRP protocol on Ve & physical interfaces**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 and DUT2  with a VRRP group say 100 <br/>2. Assign the DUT1 interface to vlan 100, create a Ve 100 interface assign the IP address say 2001::10/24 and the same address as the virtual IP address so that DUT1 will become Master and DUT2 becomes backup for VRRP 100 <br/>3. Configure the DUT1 and DUT2 with another VRRP group say 200. <br/>4. Assign the DUT2 interface address as the virtual IP address for the VRRP group 200 as a result DUT2  will become Master and DUT1 becomes backup for VRRP 200. <br/>5. Enable the VRRP session  <br/>6. Send continuous packets from both Host A and Host B to Host C. <br/>7. Debug the packets and check the path it takes to reach Host C.  <br/>8. Shutdown the DUT1 interfaces (port assigned to ve) and verifies that DUT2 becomes the master for both the VRRP group’s 100 & 200. <br/>9. Bring back the DUT1 interface and verify that DUT1 regains its mastership and DUT2 continues to be master for VRRP group 200  <br/>10. Similarly, do shut/no shut on Ve interface and verify the same. <br/>11. During these process, let the continuous traffic be running and verify that master should transmit the packets to the host <br/>12. Repeat the above steps from 5 – 10 by enabling tracking on the physical interface assigned to ve interface |

### 3.2.44	IPv6: To verify track port with different weight while flapping the track port

| **Test ID**    | **FtOpSoRoVrrpv3Fn046**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify track port with different weight while flapping the track port** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure few VRRPv3 session on both DUT1 & DUT2 in such a way thatDUT1 is master for few sessions whereas DUT2 is master for rest of the sessions<br/>2. Track the uplink ports which includes a Port-channel, physical & Ve interface <br />3. Verify the priority are shown as configured since all the track ports<br/>are up <br />4. Start the traffic and make sure Master is forwarding the traffic for all<br/>the sessions <br />5. Start shutting down the track port one by one in such a way that the priority is reduced below backup and back up takes over <br />6. Verify the new master is forwarding the traffic and the priority on the back up is reduced to the weight on the track interface<br />7. Bring back the track interface up one by one and verify the backup becomes master<br />8. Flap the track interface multiple times and verify the priority is reverted back as expected |

### 3.2.45	IPv6: To verify track port with higher weight and invalid interface

| **Test ID**    | **FtOpSoRoVrrpv3Fn047**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify track port with higher weights and invalid interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure few VRRP session on both DUT1 & DUT2 in such a way that DUT1 is master for few sessions whereas DUT2 is master for rest<br />2. Track the uplink ports which includes a Port-channel, physical & Vlan interface which are not present in the system<br />3. Verify the priority is reduced by the weight since the ports are considered down due to non-existent<br />4. Bring the ports online by assigning the ports part of port-channel and vlan interface  and verify the current priority is same as configured priority since the ports are up<br />5. Start the traffic and make sure Master is forwarding the traffic for all the sessions<br />6. Try configuring the weight greater than configured priority, the cli should throw an error since bring the port down will set the priority to negative<br />7. Track port greater than 8 numbers and verify suitable errors are shown<br />8. Track both port which is part of port-channel and port-channel it self, removing the port from port-channel will bring the port-channel down but the physical port will be up<br />9. Make sure only the weight w.r.t port-channel is reduced<br />10. Similarly try the same experiment on the interface part of vlan interface too |

### 3.2.46	IPv6: To verify the docker restart ,reboot,config reload and warm boot case

| **Test ID**    | **FtOpSoRoVrrpv3Fn048**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To verify the Docker restart, reboot, config reload and warm boot ** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 and DUT2  with a VRRP group say 100<br />2. Assign the DUT1 interface address as the virtual IP address for the VRRP group 100 as a result DUT1 will become Master and DUT2 becomes backup for VRRP 100<br />3. Configure the Default route for the Host A has the DUT1 interface<br />4. Configure the DUT1 and DUT2 with another VRRP group say 200<br />5. Assign the DUT2 interface address as the virtual IP address for the VRRP group 200 as a result DUT2 will become Master and DUT1 becomes backup for VRRP 200<br />6. Configure the Default route for the Host B has the DUT2 interface<br />7. Send continuous packets from both Host A and Host B<br />8. Start triggers like warm boot, reload , config reload and Docker restart <br />9. Validate the VRRP functionality after each trigger<br />10. Verify Traffic end to end after each trigger |

### 3.2.47	IPv6: Verify VRRP state with IPv6 link-local/global address configured as virtual IP address

| **Test ID**    | **FtOpSoRoVrrpv3Fn049**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify VRRP behavior with IPv6 link local/global address configured as Virtual IP address of VR router** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 and DUT2 interfaces with both ipv4 and ipv6 address<br />2. Configure IPv6 link local/global address of an interface as virtual IP address of VR router on both DUT1 and DUT2  nodes<br />3. Verify VRRP sessions are coming up or not<br />4. Verify V-MAC on both the nodes<br />5. Verify traffic end to end <br />6. reload the device with the config, once the device is up again validate step3,4 and 5 |

### 3.2.48	IPv6: Triggers - After shut/no shut on the backup router, virtual mac should be installed in mac table

| **Test ID**    | **FtOpSoRoVrrpv3Fn050**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Triggers - After shut/no shut on the backup router, virtual mac should be installed in mac table** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 and DUT2 interfaces with both ipv4 and ipv6 address<br /> 2.Configure VRRP group on the DUT1 and DUT2 interfaces having dual stack<br /> 3.Verify MAC table on DUT2, vMAC should be installed in MAC table <br /> 4.Shut down the VRRP interface on DUT2, verify MAC again, this time vMAC should be removed from the MAC table <br /> 5.No shut the VRRP interface on DUT2, Verify MAC table , the vMAC again installed in the MAC table |

### 3.2.49	VRRP - Verify VRRP over IP unnumbered interface

| **Test ID**    | **FtOpSoRoVrrpv3Fn051**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify VRRP version2 and version3 over an IP unnumbered interface** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure DUT1 and DUT2 with VRRP group over IP unnumbered interface<br />2. Once the vrrp sessions comes up verify show vrrp and validate all the parameters<br />3. Validate traffic end-end <br />4. Shutdown the vrrp interface on DUT1 and make sure DUT2 becomes the Master <br />5. Bring back the vrrp interface on DUT1, DUT1 should advertise and declare as master |

## 3.3 Negative

### 3.3.1	Verify VRRP behavior by having different advertisement interval

| **Test ID**    | **FtOpSoRoVrrpv3Fn052**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify VRRP behavior by configuring different advertisement interval ** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Config 2 VRRP sessions say 100 and 200 on both the devices<br />2. Config Adv interval as 100 millisec in DUT1 and 10 sec in DUT2 for vrrp sessions 100 <br />3. Both DUT1 and DUT2 claims it self as master for VRRP session 100<br />4. But there is no disruption for VRRP 200 <br />5. Verify show VRRP for both the sessions<br />6. Revert back Advt interval to default value for VRRP 100<br />7. Now verify the node with higher priority will be Master and forwards the traffic |

## 3.4 Reboot
### 3.4.1    To verify VRRP function after doing reboot trigger

| **Test ID**    | **FtOpSoRoVrrpv3Fn053**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **Verify VRRP function after reboot trigger**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 and DUT2  with a VRRP group say 100<br />2. Assign the DUT1 interface address as the virtual IP address for the VRRP group 100 as a result DUT1 will become Master and DUT2 becomes backup for VRRP 100<br />3. Configure the Default route for the Host A has the DUT1 interface<br />4. Configure the DUT1 and DUT2 with another VRRP group say 200<br />5. Assign the DUT2 interface address as the virtual IP address for the VRRP group 200 as a result DUT2 will become Master and DUT1 becomes backup for VRRP 200<br />6. Configure the Default route for the Host B has the DUT2 interface<br />7. Send continuous packets from both Host A and Host B<br />8. Reboot DUT1 node and once the node comes up <br />9. Validate the VRRP functionality <br />10. Verify Traffic end to end after each trigger |

## 3.5 Scale test
### 3.5.1    To configure and verify the scale numbers are working fine max number of  vrrp instance per interface

| **Test ID**    | **FtOpSoRoVrrpv3Fn054**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To configure and verify the scale numbers**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 with a VRRP group say VRRP 1, assign the virtual IP address and enable the VRRP session<br />2. Verify the VRRP session by using the command Show VRRP<br />3. Now Configure the DUT1 with a VRRP group say VRRP 2, assign different virtual IP address which is not same as the one assigned to VRRP 1 and enable the VRRP session<br />4. Verify the VRRP session by using the command Show VRRP<br />5. Similarly continue to configure the DUT1 with VRRP group 3 to 128. Assign different virtual IP address for the entire VRRP group<br />6. Verify that all the VRRP session should be enabled and shown after executing the command Show VRRP and also DUT1 should be master<br />7. Start sending traffic for all the virtual IP address, and verify that only DUT1 should forward the packet for all the 128 sessions and DUT2 being back should drop the packets<br />8. Save the configuration to start up Config<br />9. Reboot the DUT<br />10. Copy the start up Config to Running Config<br />11. Verify that all the VRRP session are retained and shown after executing the command Show VRRP<br />12.ENable MAx number of VRRP instances per interface -16<br />13. Configure max number of VIP's per VRRP instance -4<br />14. Configure max number of tracked interfaces to per VRRP instance-8 |

### 3.5.2    To configure and verify the scale numbers are working fine max number of  vrrp enabled interfaces

| **Test ID**    | **FtOpSoRoVrrpv3Fn055**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To configure and verify the scale numbers**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure 128 ve interfaces on both DUT1 and DUT2 <br />2. Enable  vrrp instance on all the ve interfaces <br />3. Validate the max number of vrrp enabled interfaces by show vrrp <br />4. Verify traffic end to end<br /> |
### 3.5.3    To configure and verify the scale numbers are working fine max number of  VIP's per VRRP interfaces and max number of tracked interfaces

| **Test ID**    | **FtOpSoRoVrrpv3Fn056**                                      |
| -------------- | ------------------------------------------------------------ |
| **Test Name**  | **To configure and verify the scale numbers**                |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1. Configure the DUT1 with a VRRP group say VRRP 1, assign the virtual IP address and enable the VRRP session <br />2. Verify the VRRP session by using the command Show VRRP<br />3. Configure max number of VIP's per VRRP interface - which is 4 <br  />4. Configure max number of interfaces to be tracked - which id 8 per VRRP instance <br />5. Verify max tracked interfaces by show vrrp <br />6. Validate the traffic end to end |

## 3.6 Management

#### 3.6.1 SNMP
#### 3.6.1 gNMI
#### 3.6.1 REST API

# 4 Reference Links

CLI -Guide

<https://docs.google.com/document/d/1K1__1MKDNVlYPjiq1jSdeptJ5Rmy6F2vrl-YQ_JqdxQ/edit?ts=5d4b25a7>

