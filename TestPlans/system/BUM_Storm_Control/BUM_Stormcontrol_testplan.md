#  SQA Test Plan
#  SONiC BUM Storm Control
#  SONiC 3.0 Project and Buzznik Release
[TOC]
# Test Plan Revision History
| Rev  |    Date    |        Author        |      Change Description      |
| :--: | :--------: | :------------------: | :--------------------------: |
| 0.1  | 10/10/2019 | Praveen Kumar Kota   |      Initial version         |

# List of Reviewers
| Function |         Name         |
| :------: | :------------------: |
|   Dev    |     Ramakant J       |
|   Dev    |     Mohanarajan S    |
|   QA     | Anil Kumar Kolkaleti |
|   QA     |   Giri Babu Sajja    |
|   Auto-infra     |   Kalyan     |

# List of Approvers
| Function  |           Name         | Date Approved |
| :-------: | :--------------------: | :-----------: |
|   Dev     |     Ramakant J         |               |
|   Dev     |     Mohanarajan S      |               |
|   QA      |  Anil Kumar Kolkaleti  |               |
|   QA      |  Giri Babu Sajja       |               |

# Definition/Abbreviation
| **Term** | **Meaning**                                           |
| -------- | ----------------------------------------------------- |
| BUM      | Broadcast Unknown-unicast and unknown-Multicast       |


# Feature Overview
A traffic storm occurs when packets flood the LAN, creating excessive traffic and degrading network performance. The type of traffic can be Broadcast, Unknown-unicast or unknown-Multicast (BUM). The storm-control feature allows the user to limit the amount of BUM traffic admitted to the system. This can be achieved by configuring the type of storm (Broadcast or Unknown-unicast or unknown-Multicast) and the corresponding bits per second (bps) parameter on a given physical interface. Traffic that exceeds the configured rate will be dropped. Unknown-multicast traffic consists of all multicast traffic which donot match any of the statically configured or dynamically learned multicast groups.

# Test Approach

a. As part of the module config creation of a VLAN and adding members to VLAN. Configuring all types of storm-control to the interfaces. Will add the common TG configuration also to the module which helps in reducing the test case run time.

b. Below tests will be covered as part of the test plan 

1. Need to check the traffic is getting rate-limited as per bps value configured in each storm-control type for various packet sizes(64,128,256,1480).
2. Need to check the other traffic stream is unaffected due to storm-control config on the interface.
3. Need to check the only particular portchannel member is getting rate-limited as per storm-control config.
4. Need to check the BUM storm-control config on interface participating in max VLAN where traffic is rate-limiting irrespective of VLAN.
5. Need to check the functionality of the BUM storm-control config on save and reload, reboot and warm reboot scenarios.
6. Need to check bps value configured on one interface is independent of other interface.
7. Configuring same storm control config on same interface throws an error message.
8.Configuration errors out when applied on port-channel or VLAN.
9.Configuring on interface without providing bps value and check that configuration is  not accepted.
10.Unconfiguring on an interface by providing bps as input and  verify that  additional / invalid parameter error is displayed.

c. No third party tools are required for this feature

d. All tests can be automatable as per the existing test plan. 

e. REST and gNMI are Not Applicable.

# 1 Test Focus Areas
## 1.1 Functional Testing 
  - All CLI commands.
  - BUM traffic gets rate-limited as per BUM storm-control policer parameters
  - Other traffic streams are unaffected with storm-control config on interfaces
  - Add, Delete and add BUM RL again on same interface.
  - Traffic is rate-limited as per the updated value of bps


## 1.2 Negative Testing 
  - storm-control configuration errors out when applied on port-channel or VLAN interfaces
  - BUM storm-control on interface without providing bps value
  - Unconfigure BUM storm-control on interface by providing bps  
  - Same configuration is repeated on the interface

## 1.3 Scale and Performance Testing
  - BUM storm-control with max VLAN and incremental kbps values on physical interfaces on the system.
  
 
## 1.4  Reboot/Reload/Upgrade Testing
   - Warm Reboot
   - Cold Reboot


# 2 Topologies
## 2.1 Topology
![BUM](BUM_Topology.PNG "Figure: BUM_Topology ")


# 3 Test  Case and Objectives
## 3.1 CLI and Management 

### 3.1.1 Verify broadcast storm-control,unknown-unicast and unknown-multicast storm-control config and un-config on interface and all show commands

| **Test ID**    | **ft_stormcontrol_cli**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify broadcast storm-control,unknown-unicast and unknown-multicast storm-control config and un-config on interface and all show commands.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **CLI**                                                      |
| **Steps**      | **Verify broadcast storm-control,unknown-unicast and unknown-multicast storm-control config and un-config on interface and all show commands** |



### 3.1.2 Verify that the bps value configured is independent per interface. 

| **Test ID**    | **ft_stormcontrol_bps_intf_indp**                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the bps value configured is independent per interface.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **CLI**                                                      |
| **Steps**      | **Procedure:<br/>1)Bring the DUT default config.<br/>2)Configure BUM storm-control on an interface <br/>3) Configure same bps value on other interface and check the bps value configured is independent per interface.<br/>Expected Result:<br/>1) Verify that device comes with default config.<br/>2) verify that configuration is successful.<br/>3) Verify that configuration is applied and verify that the bps value configured is independent per interface.** |



### 3.1.3  Verify when user configures BUM storm-control on interface where the same type is already configured, verify that if bps value is different, the new bps value is updated to the CONFIG_DB. 

| **Test ID**    | **ft_stormcontrol_bps_overwrite_new_bps_value**              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify when user configures BUM storm-control on interface where the same type is already configured, verify that if bps value is different, the new bps value is updated to the CONFIG_DB.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **CLI**                                                      |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure any storm-control action with bps value.<br/>3) Again configure same storm-control action with different bps value.<br/>Expected Result:<br/>1) Verify the device Up with default config.<br/>2) verify that configuration is successful.<br/>3) Verify that configuration is applied successfully and new value is updated in CONFIG_DB.** |



## 3.2 Functional


### 3.2.1 Verify that  all three BUM storm-control on interface and verify that the rate-limiting of traffic is as per the  respective policer parameters. 
| **Test ID**    | **ft_stormcontrol_BUM_traffic_policer_params**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that  all three BUM storm-control on interface and verify that the rate-limiting of traffic is as per the  respective policer parameters.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure all there BUM storm-control on an interface with rate level.<br/>3)Send traffic from ixia with broadcast stream config.<br/>4)Send traffic from ixia with unknown-multicast stream config.<br/>5)Send traffic from ixia with unknown-unicast stream config.<br/>Expected results:<br/>1)Verify that device up with default config.<br/>2)Verify that BUM storm-control configuration is applied on an interface.<br/>3)Verify that  broadcast traffic gets rate-limited as per broadcast storm-control policer parameters.<br/>4)Verify that  unknown-Multicast traffic gets rate-limited as per unknown-multicast storm-control policer parameters.<br/>5)Verify that  unknown-unicast traffic gets rate-limited as per unknown-unicast storm-control policer parameters.**  |



### 3.2.2  Verify that the BUM storm-control configurations are correctly re-applied after cold reboot. .

| **Test ID**    | **ft_stormcontrol_cold_reboot**                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the BUM storm-control configurations are correctly re-applied after cold reboot.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure BUM storm-control configurations on all interfaces.<br/>3)Try to perform cold reboot.<br/>Expected results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that BUM storm-control config is applied on all interfaces.<br/>3)Verify that cold reboot operation is performed and configuration is retained after reboot.<br/>4) Verify that the BUM storm-control configurations are correctly re-applied after cold reboot.** |


### 3.2.3  Verify that all the three storm-control types can be configured and rate limiting is applied independently on the three types of traffic received at the interface.  

| **Test ID**    | **ft_stormcontrol_config_all_same_interface**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that all the three storm-control types can be configured and rate limiting is applied independently on the three types of traffic received at the interface.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure BUM storm-control configurations all types on all interfaces.<br/>3)Send traffic from ixia to the interface rates as below,equal,high rate levels.<br/>4)Perform above steps on remaining BUM storm-control types.<br/>Expected results:<br/>1)Verify that device up with default config.<br/>2)Verify that BUM storm-control configuration is applied <br/>3)Verify that traffic is forwarded in below,equal rate and high rate it gets dropped <br/>4)Verify that all the three storm-control types can be configured and rate limiting is applied independently on the three types of traffic received at the interface.**  |


### 3.2.4 Verify that after unconfiguring BUM storm-control configuration on interface, BUM storm traffic rate is no  more rate limited. 

| **Test ID**    | **ft_stormcontrol_config_clear_noaffect_traffic**            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that after unconfiguring BUM storm-control configuration on interface, BUM storm traffic rate is no  more rate limited.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure any storm-control type on an interface.<br/>3)Send traffic from ixia beyond the rate level.<br/>4)Now try to unconfigure storm-control on interface.<br/>Expected results:<br/>1)Verify that device is up with default config.<br/>2)Verify that any storm-control configuration is applied.<br/>3)Verify that traffic gets forwarded up to rate level beyond level gets dropped.<br/>4) Verify that after unconfiguring BUM storm-control configuration on interface, BUM storm traffic rate is no  more rate limited.** |


### 3.2.5 Verify that other traffic streams are unaffected by BUM storm-control configuration. 

| **Test ID**    | **ft_stormcontrol_config_unaffect_other_traffic**            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that other traffic streams are unaffected by BUM storm-control configuration.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT default config.<br/>2)Configure  BUM storm-control configuration on ixia connected interfaces.<br/>3)Send storm-control traffic.<br/>4)Send traffic from other ixia port with other traffic config not related to storm-control.<br/>Expected results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that storm-control configuration is applied.<br/>3)Verify that storm-control traffic stream configured and able to send traffic.<br/>4)Verify that traffic from other ixia ports are unaffected by BUM storm-control configuration.** |



### 3.2.6 Verify that traffic is rate-limited as per the updated value of bps when user updates existing  BUM storm-control configuration on interface. 
| **Test ID**    | **ft_stormcontrol_traffic_rate_limited_bpsvalue**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that traffic is rate-limited as per the updated value of bps when user updates existing  BUM storm-control configuration on interface.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure all there BUM storm-control on an interface with new bps value<br/>3)Send traffic from ixia with broadcast stream config.<br/>4)Send traffic from ixia with unknown-multicast stream config.<br/>5)Send traffic from ixia with unknown-unicast stream config.<br/>Expected results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that BUM storm-control  config is created and configured bps value is displayed correct value in show command.<br/>3)Verify that  broadcast traffic gets rate-limited as per bps value configured<br/>4)Verify that  unknown-multicast traffic gets rate-limited as per bps value configured<br/>5)Verify that  unknown-unicast traffic gets rate-limited as per bps value configured.** |


### 3.2.7  Verify BUM storm-control  physical interfaces participating as members of portchannel and traffic is  rate-limiting and only particualr member is getting rate-limited.

| **Test ID**    | **ft_stormcontrol_portchannel_intf**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BUM storm-control  physical interfaces participating as members of portchannel and traffic is  rate-limiting and only particular member is getting rate-limited.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functionality**
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Create one portchannel and particate few interfaces in to that portchannel.<br/>3)Configure any BUM storm-control on an interface which is a member in portchannel with an bps value.<br/>4)Send traffic from ixia any storm-control type stream config which is configured.<br/>Expected results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that portchannel creation is successful and portchannel is also up.<br/>3)Verify that storm-control config is successful on an interface partcipating in portchannel.<br/>4)Verify that traffic is getting rate-limited at only particular member.** | 



## 3.3 Negative


### 3.3.1  Verify that error is returned when the same configuration is repeated on the interface. 

| **Test ID**    | **ft_stormcontrol_neg_config_same_interface**                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that error is returned when the same configuration is repeated on the interface.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Negative**                                                 |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure BUM storm-control on an interface.<br/>3)Try to configure the same config on same interface.<br/>Expected Results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that config is applied and present in config_db.<br/>3)Verify that error is returned when the same configuration is repeated on the interface** |

### 3.3.2   Verify that storm-control configuration errors out when applied on port-channel or VLAN interfaces. 

| **Test ID**    | **ft_stormcontrol_neg_config_vlan_portchannel**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that storm-control configuration errors out when applied on port-channel or VLAN interfaces.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Negative**                                               |
| **Steps**      | **Procedure:<br/>1)Bring up the two DUTs with default configuration.<br/>2)Create portchannel on both device and add ports in them<br/>3)Create VLAN and add ports in VLAN in both devices.<br/>4)Configure the Broadcast storm control on portchannel or VLAN members.<br/>Expected Results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that Portchannel coming up successfully.<br/>3)Verify VLAN creation is successful.<br/>4)Verify that storm-control configuration errors out when applied on port-channel or VLAN interfaces.** |


### 3.3.3  Verify that BUM storm-control on interface without providing bps value and check that configuration is  not accepted. 

| **Test ID**    | **ft_stormcontrol_neg_config_without_bpsvalue**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BUM storm-control on interface without providing bps value and check that configuration is  not accepted .** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Negative**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure BUM storm-control on interface without providing bps value.<br/>Expected Results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that BUM storm-control on interface without providing bps value and verify  that configuration is  not accepted.** |


### 3.3.4  Verify that unconfigure of  BUM storm-control on interface by providing bps as input and verify that  additional / invalid parameter error is displayed. 

| **Test ID**    | **ft_stormcontrol_neg_unconfig_with_bpsvalue**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that unconfigure of  BUM storm-control on interface by providing bps as input and verify that  additional / invalid parameter error is displayed.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Negative**                                               |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure BUM storm-control on an interface.<br/>3)Now Try to unconfigure storm-control of any type by providing bps value.<br/>Expected Results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that config is applied.<br/>3)Verify that unconfigure of  BUM storm-control on interface by providing bps as input and verify that  additional / invalid parameter error is displayed.** |



## 3.4 Reboot/Reload/Upgrade Test Cases

### 3.4.1  Verify that BUM storm-control is only active on interfaces that previously had storm-control configurations  before warm boot. 

| **Test ID**    | **ft_stormcontrol_config_active_intf_warmboot**              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BUM storm-control is only active on interfaces that previously had storm-control configurations  before warm boot.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Reboot**                                                   |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure BUM storm-control on few interfaces.<br/>3)Perform Warm Boot operation on the device. <br/>Expected Results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that config is applied only on few interfaces.<br/>3)Verify that BUM storm-control is only active on interfaces that previously had storm-control configurations  before warm boot.** |


### 3.4.2  Verify that BUM storm-control configurations are restored after warm boot. 

| **Test ID**    | **ft_stormcontrol_config_restore_warmboot**                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BUM storm-control configurations are restored after warm boot.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Reboot**                                                   |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure BUM storm-control configurations.<br/>3)Perform Warm Boot operation on the device. <br/>Expected Results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that config is applied only on few interfaces.<br/>3)Verify that BUM storm-control configurations are restored after warm boot.** |


### 3.4.3 Verify that BUM storm rate-limiting continues to work at the specific rate across warm boot. 

| **Test ID**    | **ft_stormcontrol_rate_limit_across_warmboot**               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BUM storm rate-limiting continues to work at the specific rate across warm boot.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Reboot**                                                   |
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config..<br/>2)Configure BUM storm-control on an interface with  it's rate limits.<br/>3)Send continuous traffic from ixia.<br/>4)Perform Warm Boot operation.<br/>Expected Results:<br/>1)Verify that device is Up with default config.<br/>2)verify that configuration is successful.<br/>3)Verify that traffic gets forwarded.<br/>4)Verify that BUM storm rate-limiting continues to work at the specific rate across warm boot.** |


## 3.5 Stress,Scale and Performance


### 3.5.1  Verify that rate-limiting works fine on repeated configuration / unconfiguration and bps update.

| **Test ID**    | **ft_stormcontrol_incremental_bps_value**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that rate-limiting works fine on repeated configuration / unconfiguration and bps update.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Scalability**
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure all three BUM storm-control on an interface with an bps value.<br/>3)Configure new bps value for broadcast stormcontrol (with range of bps values(51200 to 102400 with increment of 5120))<br/>4)Send traffic from ixia with broadcast stream config.<br/>5)Again configure new bps value with range of bps values(51200 to 102400 with increment of 5120) and Send traffic from ixia with broadcast stream config.<br/>6)Repeat  3,4,5 for remaing unknown-uncast and unknown-multicast storm-control types.<br/>Expected results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that BUM storm-control  config is created and configured bps value is applied on all types.<br/>3)Verify that new bps value got updated for broadcast storm-control type.<br/>4)Verify that traffic is getting rate-limited as per bps value configured.<br/>5)Verify that configuring,unconfiguring of bps value is working fine and traffic is getting rate-limited as per bps value configured.<br/>6)Verify that traffic is getting rate limited as per bps value in each storm-control type.** |



### 3.5.2  Verify BUM storm-control config on interface participating in max VLAN where traffic is  rate-limiting irrespective of VLAN.

| **Test ID**    | **ft_stormcontrol_scaling_max_vlan_intf**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BUM storm-control on all the physical interfaces on the system and check that rate-limiting is  performed on all interfaces.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Scalability**
| **Steps**      | **Procedure:<br/>1)Bring the DUT with default config.<br/>2)Configure any BUM storm-control on an interface with an bps value.<br/>3)Create max VLAN and partcipate same interface in all VLANs.<br/>4)Send incremental VLAN traffic from ixia with any storm-control type stream config which is configured.<br/>Expected results:<br/>1)Verify that device is Up with default config.<br/>2)Verify that BUM storm-control  config is created and configured bps value.<br/>3)Verify max VLAN is created in device and interface is a member in all VLANs.<br/>4)Verify that traffic is getting rate-limited irrespective of VLAN tag.** |



## 4 Reference Links
<http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/12596/4/L2/bum_storm_control/sonic-bum-storm-control-hld.md>
