#  SQA Test Plan
# RPVST+
#  SONiC 3.0 Project and Buzznik Release
[TOC]
# Test Plan Revision History
# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
| QA | Anil Kolkaleti |
| QA | Giri Babu Sajja |
| Dev | Danial Varagunasingh |
| Dev | Phanindra T.V |
| Dev | Gunavardhani Bandaru |
| Dev | Ravi Vemuri |

# List of Approvers



| Function |         Name         | Approved on |
| :------: | :------------------: | ----------- |
|   Dev    | Danial Varagunasingh |             |
|    QA    |   Giri Babu Sajja    |             |

# Definition/Abbreviation
| **Term** | **Meaning**                     |
| -------- | ------------------------------- |
| RPVST | Rapid Per Vlan Spanning Tree |
| CIST     | Common and internal spanning tree |
| CST      | Common spanning tree       |
| IST      | Internal spanning tree       |
| MSTI     | Multiple spanning tree instance |
| MSTP     | Multiple spanning tree protocol |
| PVST     | Per VLAN spanning tree protocol |
| REST     | Representational state transfer |
| STP      | Spanning tree protocol          |

# Feature Overview
RPVST+ is IEEE 802.1w (RSTP) standard implemented per VLAN. A single instance of RSTP runs on each configured VLAN.

RPVST provides rapid traffic re-convergence per vlan for point-to-point links in less than 500 milliseconds, following the failure of a bridge or bridge port. This re-convergence occurs more rapidly than the re-convergence provided by the IEEE 802.1D STP. Convergence in RSTP bridges is based on explicit handshakes between Designated ports and their connected Root ports and does not rely on any timers.

- Support BPDU guard functionality
- Support Root guard functionality
- Bridge-id should include the VLAN id of the RPVST instance along with bridge MAC address to derive a unique value for each instance.
- Port channel path cost will be same as the member port cost, it will not be cumulative of member ports cost
- DA MAC in case of RPVST+ should be Cisco Shared Spanning Tree Protocol Address 01:00:0C:CC:CC:CD
- Support protocol operation on static breakout ports
- Support protocol operation on physical and Port-channel interfaces



# Test Approach

######  What will be part of module config?

1. Configuring of port channels between all the duts.
2. Configuring of vlans(equal to no of duts in topology) on all the duts.
3.  Configuring of RPVST on all the duts and ports in the topology.
4.  Configuring of bridge priorities in such a way that 1 dut is root in 1 vlan instance.
5. Wait for stp convergence.
6. Check for dut and port stp states and return once the convergence is completed.

###### What tests will be covered / clubbed together?

1. Cli show and config apis will be covered as part of other tests.
2. Shut and no shut of port channel interface and physical interfaces will be covered in a single test.
3. MLAG partner failover and not failover convergence tests will be covered in single test.
4. All STP timers related tests will be covered in single test.

###### What 3rd party tools to be tested for?

​	None

###### In which area’s common warm-boot, cold boot , fast-boot file, corresponding test of the given feature gets covered?

​	RPVST doesn't support warmreboot. Cold boot and fast boot will be covered as part of existing common 	files "test_switching_fast_reboot.py, test_switching_save_reboot.py.

###### List of tests which can not be automated and why?

None.

###### What is the plan for covering secondary UI (Ex:- gNMI / KLISH / REST) and how much?

​	Separate tests are planned for each UI.

	1. Validate RPVST related REST commands.
 	2. Validate RPVST is working fine with gNMI interface.
 	3. Validate RPVST is working fine with KLISH CLI interface.

# 1 Test Focus Areas

## 1.1 CLI and management
	- All CLI commands
	- RPVST related REST commands.
	- RPVST on gNMI interface
	- RPVST debug commands



## 1.2 Functional Testing
  -	RPVST global configuration
  -	RPVST interface configuration
  -	BPDU guard
  -	Edge port
  - Root Guard
  -	BPDU filtering on RPVST  
  -	Config save and reload

## 1.3 Functional Interaction

  -	Interaction with MLAG
  -	Interaction on Port Channel

## 1.4 Reboot/Reload test cases

  -	Cold Reboot
  -	Config Reload

## 1.5 Interop

  -	None

## 1.6 Serviceability

  -	RPVST syslog messages
  -	RPVST statistics

## 1.7 Management

  -	debug commands
  -	gNMI interface
  -	REST interface
  -	KLISH CLI

## 1.8 Stress,Scale,Negative and Performance

  -	RPVST with max vlan instances
  -	Stress test with shut/no shut
  -	multiple times enable/disable RPVST

## 

## 2 Topologies


## 2.1 Topology 1
#### ![RPVST](rpvst.png)

# 3 Test  Case and Objectives
## 3.1 CLI and management



#### 3.1.1 Verify all RPVST config and show commands

| **Test ID** | **ft_rpvst_cli** |
|--------|:----------------|
| **Test Name** | **Verify all RPVST config and show commands** |
| **Test Setup** | **Topology1** |
| **Type** | **CLI** |
| **Steps** | **Steps :<br/><br/>  1. Bring up the SONIC switch with default configuration.<br/>2. Verify that all the commands(Config and show) listed in below document are working fine:<br/>http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/9629/23/L2/STP/SONiC+RPVST+Plus+FS.md<br/>3. For a config command verify :<br/>   min and max supported values are configured fine.<br/>  show commands of the corresponding configuration command reflects the configured values.** |
## 

## 3.2 Functionality

#### 3.2.1 Verify that DUT is working properly on spanning tree RPVST mode.



| **Test ID**    | **ft_rpvst_mode**                                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that DUT is working properly on spanning tree RPVST mode.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create VLAN 10,20,30 and 40. Participate all the ports in these vlans, Enable spanning tree on all the DUTs for all the VLANs.<br/>3. Set spanning tree mode as RPVST globally and configure priorities such that each DUT become root bridge for one vlan.<br/>4. Check the spanning tree port roles and states.<br/>5. configure maximum cost per vlan on the cross links connecting the DUT1-DUT3 and DUT2-DUT4.<br/>6. Send the bidirectional learned vlan tagged traffic between TG1-TG3. <br/>7. reset the cost to default on the cross links and send the learned traffic from TG1-TG3.<br/>8. Repeat all the above steps with random VLANs(other than 10,20,30,40 which are configured previously) configured on DUT.<br/>9. Disable spanning tree on DUT1.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that VLANs are created and spanning tree is enabled.<br/>3. Verify that spanning tree is set as RPVST and each DUT become root bridge for one vlan as per the configuration.<br/>4. Verify that port roles and states are correct for all the DUTs.<br/>5. Verify that the configuration is successful.<br/>6. Verify that traffic is forwarded without any loops in the path TG1-DUT1-DUT4-DUT3-TG3 or TG1-DUT1-DUT2-DUT3-TG3 and traffic should not get forwarded to other TG ports.<br/>7. Verify that the traffic converges on path TG1-DUT1-DUT3-TG3.<br/>8. Verify that the behavior should be same as step2-7.<br/>9. Spanning tree converges properly, and BPDU handling is proper on this device** |

#### 3.2.2 Verify that end to end learnt traffic is forwarded fine for a given vlan instance



| **Test ID**    | **ft_rpvst_learnt_traffic**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that end to end learnt traffic is forwarded fine for a given vlan instance** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure<br />1) Create multiple vlans.<br/>2)  Configure bridge priority of 0 on one switch per vlan<br/>3) Check convergence of each vlan<br/>4) Send learnt traffic between any 2 DUTs for a vlan.<br/><br/>Expected result:<br/>1) Verify vlan config is successful.<br/>2) Verify corresponding vlan configured with bridge priority 0 should become root switch for that vlan.<br/>3) Per every vlan instance, <br/>  a) There is only one root switch<br/> b) switch with highest mac address will become blocked switch for that vlan instance.<br/>c) Blocked switch should have one forwarding port and other ports should be blocking.<br/>4) Verify that traffic is forwarded only between those 2 pair of DUTs and TG ports in the traffic vlan.** |

#### 3.2.3 Verify that RPVSTP BPDU has the correct fields.



| **Test ID**    | **ft_rpvst_bpdu**                                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that RPVSTP BPDU has the correct fields.**          |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>      1. Boot the DUT with default configuration.<br/>      2. Check the default spanning tree mode on DUT.<br/>      3. Set spanning tree mode as RPVST.<br/>      4. Configure VLAN 1,99,999 with ports connected to TG in tagged mode for all the configured VLANs and Capture the BPDUs on IXIA and verify<br/>         a) the type and version on BPDU.<br/>       b) mac address and VLAN 1 on BPDU.<br/>     c) SSTP BPDU for bridge id field.<br/><br/>    <br/>Expected Behavior:<br/>     1. Verify that the DUT  is up with default configuration.<br/>     2. Verify that spanning tree is enabled in default mode.<br/>     3. Verify that spanning tree mode is set as RPVST.<br/>     4. Verify that BPDU is showing<br/>        a)  type and version as 2 both on IEEE BPDU and SSTP BPDU.<br/>       b) IEEE BPDU with destination mac address "0180.c200.0000, RPVST BPDU for VLAN 1 with TLV value and mac address as "0100.0ccc.cccd" which is untagged.<br/>      c) SSTP BPDU having mac address as "0100.0ccc.cccd" will have Root Identifier values with 32867(32768+99) and bridge MAC address.<br/>  Verify that other SSTP BPDU having mac address as "0100.0ccc.cccd" will have Root Identifier values with 37767(32768+999) and bridge MAC address.<br/>  SSTP BPDU having mac address as "0100.0ccc.cccd" with TLV value updated includes VLAN information and the BPDU is a tagged packet<br/> sys-id-ext will have the vlan instance id.** |

#### 3.2.4 Verify that the port enabled for BPDU filtering will drop any BPDUs it receives.



| **Test ID**    | **ft_rpvst_bpdu_filter**                                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the port enabled for BPDU filtering will drop any BPDUs it receives.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure: <br/><br/>1.  Start transmitting unresolved L2 traffic to port 0/1 of S1.<br/>2.  Configure the DUT port 0/1 for BPDU filtering. Also enable spanning tree on S2 and configure its bridge priority lesser than that of S1.<br/>3.  Disable port 0/1 of DUT for BPDU filtering.<br/>4.  Enable BPDU filtering on port 0/1 of DUT.<br/><br/>Expected Result: <br/><br/>1.  Verify that traffic is switched to both DUT and S2..<br/>2.  The switch S2 starts transmitting BPDUs and since it has a lesser priority it is eligible to become the root. DUT port 0/1 remains in Forwarding state and drops all the BPDUs on ingress and continues to transmit BPDUs with S1 information.<br/>    In 802.1d mode, as the root bridge ignores inferior BPDUs, S2 continues to act as root bridge for itself and S1 is root ridge for S1 and DUT. Traffic flows to both S1 DUT and S2 from S1 after this. <br/>    Verify the the S1 to DUT interconnected-link port states and port roles are not affected due to this BPDU-filter behavior. <br/>3.  The switch S2 becomes the root bridge and port 0/1 of DUT has port role as Root. The L2 traffic is continued to be received on port to both DUT and S2 from S1.<br/>4.  The switch S1 is restored as the root bridge. Verify same result as in 2 above.** |

#### 3.2.5 Verify that a port (also configured to be an edge port) and enabled for BPDU Filtering will be disabled for BPDU Filtering when acquired by the LAG.



| **Test ID**    | **ft_rpvst_bpdu_filter_enable**                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that a port (also configured to be an edge port) and enabled for BPDU Filtering will be disabled for BPDU Filtering when acquired by the LAG.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure: <br/><br/>1.  Start transmitting L2 unresolved traffic on Ixia port to 0/1 of S1.<br/>2.  Configure 0/1 of DUT as edge port and enable DUT globally for BPDU Filtering. Also enable spanning tree on S2 and configure its bridge priority lesser than that of S1.<br/>3.  Add 0/1 on DUT and S2 to LAG. <br/>4.  Remove 0/1 from LAG on DUT.<br/><br/>Expected Result: <br/><br/>1.  Verify that the L2 traffic is sent to both DUT and S2<br/>2.  Verify that port 0/1 of DUT drops all BPDUs received. Verify that the interface counters for BPDUs received is not incremented. Also verify that the L2 traffic is forwarded out to both DUT to S2. (As S2 in 802.1d mode may ignore the inferior BPDUs received on FWD/DESG port and continues o be in the same state)<br/>3.  0/1 of DUT becomes LAG member and hence BPDU filtering is not effective. The switch S2 becomes the root bridge and LAG1 of DUT goes FWD/Root. Traffic is forwarded to DUT and S2.<br/>4.  The switch S1 is restored as the root bridge and 0/1 of DUT drops all received BPDUs as BPDU filtering now takes effect. Verify same result as in 2 above.** |

#### 3.2.6 BPDU Filtering behavior based on global, Interface and Edge port configuartion.



| **Test ID**    | **ft_rpvst_bpdu_filter_behavior_based_on_global_interface_edge_port_config** |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **BPDU Filtering behavior based on global, Interface and Edge port configuartion.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1.    Bring up the setup with one back to back link to partner device as shown in setup. 2.    Verify the BPDU filtering status as per the below matrix whish has 5 combinations. </br>Port configuration - Global configuration - Edge port - Filtering state<br/>Default - Enable - Enable - Enable<br/>Default - Enable - Disable - Disable<br/>Default - Disable - Not applicable - Disable<br/>Disable - Not applicable - Not applicable - Disable<br/>Enable - Not applicable - Not applicable - Enable<br/><br/>Expected Results:<br/>1.    Verify that boxes are up and DUT is root Bridge. .<br/>2.    Verify that BDPU filtering(BPDUs being dropped/forwarded) state is enabled/disbaled as per the matrix combination.<br/>** |

#### 3.2.7 Verify that the LAG port when enabled for BPDU Filtering will drop all the received BPDUs.



| **Test ID**    | **ft_rpvst_bpdu_filter_lag_port**                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the LAG port when enabled for BPDU Filtering will drop all the received BPDUs.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure: <br/><br/>1.  Start transmitting L2 unresolved traffic on Ixia port to 0/1 of S1.<br/>2.  Configure LAG1 of DUT as edge port and enable DUT globally for BPDU Filtering. Also enable spanning tree on S2 and configure its bridge priority lesser than that of S1.<br/>3.  Disable DUT for BPDU Filtering on LAG1 of DUT.<br/>4.  Enable BPDU Filtering on LAG1 of DUT.<br/><br/>Expected Result: <br/><br/>1.  Verify that the L2 traffic is sent to both DUT and S2<br/>2.  The LAG1 of DUT drops all BPDUs received. Verify that the interface counters for BPDUs received is not incremented. <br/>   Also verify that the L2 traffic is forwarded to DUT an S2. <br/>    ( In case of 802.1d, S2 may ignore inferior BPDU from DUT and its LAG1 will continue to be in forwarding sate.)<br/>3.  The switch S2 becomes the root bridge and LAG1 of DUT goes FWD/Root. Traffic is now forwarded to DUT and S2.<br/>4.  The switch S1 is restored as the root bridge and LAG1 of DUT drops all received BPDUs. Verify same result as in 2 above.** |

#### 3.2.8 Verify that BPDU filter is working fine when DUT is working on spanning tree  RPVST mode.



| **Test ID**    | **ft_rpvst_bpdu_filter_rpvst_mode**                          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BPDU filter is working fine when DUT is working on spanning tree  RPVST mode.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUT with default configuration.<br/>2. Create VLAN 10 on DUT and partner.<br/>3. Enable spanning tree on DUT and PD.<br/>4. Set spanning tree mode as RPVRST globally for VLAN 10 and on interfaces.<br/>5. Make DUT as root bridge.<br/>6. Now enable BPDU filter globally and make the partner connected port as edgeport.<br/>7. Now change the priority on partner device.<br/>8. Now disable the BPDU filter.<br/><br/>Expected Behavior:<br/>1. DUT and Partner is up with default configuration.<br/>2. VLAN is created on both the switches.<br/>3. Verify that spanning tree is enabled.<br/>4. Verify that spanning tree is set as RPVST.<br/>5. Verify that DUT is root bridge.<br/>6. Verify that BPDU filter is enabled.<br/>7. Verify that priority is changed on partner and partner starts sending the BPDU which os dropped on DUT.<br/>8. Verify that BPDU filter is disabled on DUT and DUT accepts BPDUs from partner. Partner becomes root bridge.** |

#### 3.2.9 Verify BPDU guard functionality when RPVST is configured.



| **Test ID**    | **ft_rpvst_bpduguard**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify BPDU guard functionality when RPVST is configured.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUT with default configuration.<br/>2. Enable spanning tree on DUT and PD.<br/>3. Set spanning tree mode as RPVST globally for native VLAN and on interfaces.<br/>4. Make partner as root bridge.<br/>5. Now enable BPDU guard on the interface on DUT.<br/>6. Now check the port status on DUT.<br/>7. Now disable BPDU guard and enable BPDU guard with D-Disable option.<br/><br/>Expected Behavior:<br/>1. DUT and Partner is up with default configuration.<br/>2. Verify that spanning tree is enabled.<br/>3. Verify that spanning tree is set as RPVST.<br/>4. Verify that partner is root bridge.<br/>5. Verify that BPDU guard is enabled.<br/>6. Verify that a syslog message is observed on DUT indicating a BPDU is received on BPDU interface.<br/>7. Verify that on receiving BPDU on DUT, port is moved to D-Disable state and also check for syslog message when the D-Disable condition is triggered.** |

#### 3.2.10 Verify that RPVST config is applied fine when configured from config_db.json file



| **Test ID**    | **ft_rpvst_config_db**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that RPVST config is applied fine when configured from config_db.json file** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>   Learnt Unicast traffic:<br/>   Instance1 for vlan10: DUT2<->DUT3 <br/>   Instance2 for vlan20: DUT3<->DUT1 <br/>   Instance3 for vlan30: DUT2<->DUT1 <br/>6. Save config.<br/>7. Now clear the config.<br/>8. Apply config from config_db.json file.<br/>9. Check RPVST convergence.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>    Instance1 for vlan10: traffic should go through DUT2->DUT1->DUT3<br/>   Instance2 for vlan20: traffic should go through DUT3->DUT2->DUT1<br/>   Instance3 for vlan30: traffic should go through DUT2->DUT3->DUT1<br/>6. Verify that config is saved to config_db.json file.<br/>7. Verify that config is cleared.<br/>8. Verify that config is applied successfully.<br/>9. Verify that RPVST is converged fine for all the instances and traffic is forwarded fine.** |

#### 3.2.11 Verify that with default configuration, bridge with lower bridge-id is the ROOT Bridge  with RPVST configured.



| **Test ID**    | **ft_rpvst_default_convergence**                             |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that with default configuration, bridge with lower bridge-id is the ROOT Bridge  with RPVST configured.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Check RPVST convergence.<br/><br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>4. Verify that DUT with lowest mac address will become Root Bridge for all the VLANs.** |

#### 3.2.12 Verify that Broadcast/Multicast/Unicast traffic is forwarded fine with RPVST configured to have multiple vlan instances.



| **Test ID**    | **ft_rpvst_diff_traffic_type**                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that Broadcast/Multicast/Unicast traffic is forwarded fine with RPVST configured to have multiple vlan instances.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>   MAC of DUT1<DUT2<DUT3<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send BC/MC/UC traffic for all the vlan instances.<br/>   VLAN10: BC traffic from DUT1<br/>           Learnt UC bi-directional from DUT2 to DUT3<br/>   VLAN20: MC traffic from DUT2<br/>           Learnt UC bi-directional from DUT3 to DUT1<br/>   VLAN30: Learnt UC bi-directional from DUT2 to DUT1<br/>           Unknown UC traffic from DUT2 to DUT1<br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>    Instance1 for vlan10: traffic should go through DUT2->DUT1->DUT3<br/>   Instance2 for vlan20: traffic should go through DUT3->DUT2->DUT1<br/>   Instance3 for vlan30: traffic should go through DUT2->DUT3->DUT1<br/>   Verify that BC,MC,unknown unicast traffic is flooded and learnt UC is forwarded only to the destined ports.** |

#### 3.2.13 Verify Edge port functionality when RPVST is configured.



| **Test ID**    | **ft_rpvst_edgeport**                                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify Edge port functionality when RPVST is configured.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure: <br/>1.  Configure the port connected to TG as edge port.<br/>2.  Do shut and no shut of the configured edge port.<br/>Expected result:<br/>1  Verify that there is no learning or forwarding delay when the port is enabled as edge port and moves to forwarding state immediately. Check for syslog for port moving from blocking to forwarding state.<br/>2	Check TCN is not triggered by shut and no shut of edge port.** |

#### 3.2.14 Verify that the mac address are not learnt when the port is in discarding state when RPVST is configured.



| **Test ID**    | **ft_rpvst_fdb_blockingport**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the mac address are not learnt when the port is in Blocking state when RPVST is configured.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Steps :<br/>1) Bring the DUT with latest build.<br/>2) Enable RPVST on both switches.<br/>3) Create VLAN 10 on both switches,make the ports as trunk and enble RPVST for this VLAN.<br/>4) Make DUT1 as root switch.<br/>5) Send incremental Uni-directional UC traffic from TG1 to TG2.<br/>6) Check FDB entries.<br/>7) Disable and enable RPVST<br/><br/>Results:<br/>1) DUT should be UP with latest build.<br/>2) Verify that RPVST config is successful.<br/>3) Verify that VLAN is configured and port config is successful.<br/>4) Verify that DUT1 becomes root switch and one of the port on DUT2 will be Blocking.<br/>5) Verify that traffic sent from TG1 is received on TG2 on active link.<br/>6) Verify that mac entries are learnt on Forwarding port on DUT2.<br/>7) Verify that RPVST is configured fine and mac entries are learnt on Forwarding port only. There should not be any mac entries on Blocking port of DUT2.** |

#### 3.2.15 Verify that with RPVST configured fdb flush happens when interface cost is changed.



| **Test ID**    | **ft_rpvst_int_cost_fdb_flush**                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that with RPVST configured fdb flush happens when interface cost is changed.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>   Learnt Unicast traffic:<br/>   Instance1 for vlan10: DUT2<->DUT3 <br/>   Instance2 for vlan20: DUT3<->DUT1 <br/>   Instance3 for vlan30: DUT2<->DUT1 <br/>6. Stop traffic and check mac entries for all instances.<br/>7. Change cost at interface level to a lower level on blocking port.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>    Instance1 for vlan10: traffic should go through DUT2->DUT1->DUT3<br/>   Instance2 for vlan20: traffic should go through DUT3->DUT2->DUT1<br/>   Instance3 for vlan30: traffic should go through DUT2->DUT3->DUT1<br/>6. Verify that mac entries are learnt fine for all the instances.<br/>7. Verify that blocking port moves to forwarding state and mac entries are flushed.** |

#### 3.2.16 Verify RPVST  behavior after disabling/enabling spanning tree on a port.



| **Test ID**    | **ft_rpvst_interface_disable_enable**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify RPVST  behavior after disabling/enabling spanning tree on a port.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>   MAC of DUT1<DUT2<DUT3<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>   Learnt Unicast traffic:<br/>   Instance1 for vlan10: DUT2<->DUT3 <br/>   Instance2 for vlan20: DUT3<->DUT1 <br/>   Instance3 for vlan30: DUT2<->DUT1 <br/>6. Disable spanning tree on port of DUT3 towards DUT1.<br/>7. Enable spanning tree on port of DUT3 towards DUT1.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>    Instance1 for vlan10: traffic should go through DUT2->DUT1->DUT3<br/>   Instance2 for vlan20: traffic should go through DUT3->DUT2->DUT1<br/>   Instance3 for vlan30: traffic should go through DUT2->DUT3->DUT1<br/>6. Verify that the port status is Disabled in all the instances.<br/>7. Verify that spanning tree converges fine and the port states are correct in all the instances.** |

#### 3.2.17 Verify the RPVST converges fine on adding and deleting additional interface to topology. 



| **Test ID**    | **ft_rpvst_link_add_del**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST converges fine on adding and deleting additional interface to topology.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>   MAC of DUT1<DUT2<DUT3<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>   Learnt Unicast traffic:<br/>   Instance1 for vlan10: DUT2<->DUT3 <br/>   Instance2 for vlan20: DUT3<->DUT1 <br/>   Instance3 for vlan30: DUT2<->DUT1 <br/>   <br/>6. Add new cable between DUT1 and DUT3. Add such that the port used on DUT1 has lower ifindex.<br/>   Make the ports as trunk.<br/>7. Remove the cable added at step6.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>    Instance1 for vlan10: traffic should go through DUT2->DUT1->DUT3<br/>   Instance2 for vlan20: traffic should go through DUT3->DUT2->DUT1<br/>   Instance3 for vlan30: traffic should go through DUT2->DUT3->DUT1<br/>   <br/>6. Verify that the topology converges fine for all instances with the new ports added.<br/>   VLAN20 instance traffic will not be disrupted and other instances traffic will be disrupted and continue on new link added. <br/>7. Verify that the topology converges fine for all the instances. It should be same as before the cable pull event.<br/>   Verify that traffic is forwarded as per the current topology for all the instances.** |

#### 3.2.18 Verify that multiple vlan instances are configured and all instances are converged fine. 



| **Test ID**    | **ft_rpvst_multiple_instances_convergence**                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that multiple vlan instances are configured and all instances are converged fine.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1) Create multiple vlans.<br/>2)  Configure bridge priority of 0 on one switch per vlan<br/>3) Check convergence of each vlan<br/><br/>Expected result:<br/>1) Verify vlan config is successful.<br/>2) Verify corresponding vlan configured with bridge priority 0 should become root switch for that vlan.<br/>3) Per every vlan instance, <br/>  a) There is only one root switch<br/> b) switch with highest mac address will become blocked switch for that vlan instance.<br/>c) Blocked switch should have one forwarding port and other ports should be blocking.** |

#### 3.2.19 Verify correct switch is elected as root in corresponding vlan as per configured bridge priority.



| **Test ID**    | **ft_rpvst_nondefault_bridge_priority**                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify correct switch is elected as root in corresponding vlan as per configured bridge priority.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1) Create multiple vlans.<br/>2)  Configure bridge priority of 0 on one switch per vlan<br/><br/>Expected result:<br/>1) Verify vlan config is successful.<br/>2) Verify corresponding vlan configured with bridge priority 0 should become root switch for that vlan.** |

#### 3.2.20 Verify the RPVST convergences fine when one of the interface is shut/no shut.



| **Test ID**    | **ft_rpvst_port_shut_noshut**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST convergences fine when one of the interface is shut/no shut.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>   MAC of DUT1<DUT2<DUT3<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. shutdown port on DUT3 towards DUT1.<br/>6. No shut the port on DUT3 towards DUT1.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that next eligible port will become root port and will move to forwarding state.<br/>6. Verify that the previously shut port will become root port again.** |

#### 3.2.21 Verify that with RPVST configured on port-channel interfaces STP BPDUs are sent or received fine.



| **Test ID**    | **ft_rpvst_portchannel_bpdu**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that with RPVST configured on port-channel interfaces STP BPDUs are sent or received fine.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUT with default configuration.<br/>2. Enable spanning tree on DUT and PD.<br/>3. Set spanning tree mode as RPVST globally for native VLAN and VLAN 10 and on interfaces.<br/>4. Make partner as root bridge.<br/>5. Configure 2 ports to be part of a port-channel.<br/>6. Check STP BPDUs.<br/><br/>Expected Behavior:<br/>1. DUT and Partner is up with default configuration.<br/>2. Verify that spanning tree is enabled.<br/>3. Verify that spanning tree is set as RPVST.<br/>4. Verify that config is successful.<br/>5. Verify that port-channel config is successful.<br/>6. Verify that STP BPDUs are are sent and forwarded fine.** |

#### 3.2.22 Verify RPVST is converging fine when topology changes- due to minimum link configured and lag members are removed.



| **Test ID**    | **ft_rpvst_portchannel_minlink**                             |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify RPVST is converging fine when topology changes- due to minimum link configured and lag members are removed.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Have 2 back to back links between each DUT pair and configure these ports part of port-channel.<br/>   Configure port-channel minimum links as 2.<br/>3. Create vlans 10,20,30 and make all port-channel ports participate in all vlans.<br/>4. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>5. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>6. Check the port-channel interface cost.<br/>7. Send traffic for all the vlan instances.<br/>8. On port-channel ports on link between DUT1 and DUT3, remove one of the port from port-channel.<br/>9. Check RPVST convergence.<br/>10. Add the ports back to port-channel on DUT1 and DUT3.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that port-channel config is successful and is UP on all DUTs.<br/>   Verify that port-channel minimum links config is successful.<br/>3. Verify vlan creation is successful and port-channel ports are participated in all vlans.<br/>4. Verify that spanning tree mode is set as RPVST.<br/>5. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>6. Verify that port-channel interfaces cost is correctly displayed.<br/>7. Verify that traffic is forwarded fine as per the instance topology.<br/>8. Verify that port-channel on DUT1 and DUT3 has only one link now and the port-channel will be down as minimum links configured is 2.<br/>9. Verify that Instance2(vlan20) traffic is not disrupted.<br/>   Verify that RPVST converges fine for VLAN10 and VLAN30 and traffic forwarded as per the current topology.<br/>10. Verify that port-channel between DUT1 and DUT3 is UP.<br/>    Verify that RPVST is converged fine for all instances.** |

#### 3.2.23 Verify that a Change in the port state of one RPVST instance should not affect the other RPVST instances.



| **Test ID**    | **ft_rpvst_portcost_in_single_instance**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that a Change in the port state of one RPVST instance should not affect the other RPVST instances.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Have 2 back to back links between each DUT pair and configure these ports part of port-channel.<br/>   Configure port-channel minimum links as 2.<br/>3. Create vlans 10,20,30 and make all port-channel ports participate in all vlans.<br/>4. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>5. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>6. Check the port-channel interface cost.<br/>7. Send traffic for all the vlan instances.<br/>8. On port-channel ports on link between DUT1 and DUT3, remove one of the port from port-channel.<br/>9. Check RPVST convergence.<br/>10. Add the ports back to port-channel on DUT1 and DUT3.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that port-channel config is successful and is UP on all DUTs.<br/>   Verify that port-channel minimum links config is successful.<br/>3. Verify vlan creation is successful and port-channel ports are participated in all vlans.<br/>4. Verify that spanning tree mode is set as RPVST.<br/>5. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>6. Verify that port-channel interfaces cost is correctly displayed.<br/>7. Verify that traffic is forwarded fine as per the instance topology.<br/>8. Verify that port-channel on DUT1 and DUT3 has only one link now and the port-channel will be down as minimum links configured is 2.<br/>9. Verify that Instance2(vlan20) traffic is not disrupted.<br/>   Verify that RPVST converges fine for VLAN10 and VLAN30 and traffic forwarded as per the current topology.<br/>10. Verify that port-channel between DUT1 and DUT3 is UP.<br/>    Verify that RPVST is converged fine for all instances.** |

#### 3.2.24 Verify the Edge port feature in RPVST mode 



| **Test ID**    | **ft_rpvst_portfast**                                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the port fast feature in RPVST mode**               |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Steps :<br/><br/>1) Bring the DUT with latest build.<br/>2) Enabled RPVST and Port fast on the TG connected interface.<br/>3) Shut/no shut the ixia connected interface.<br/><br/>Results:<br/>1) DUT should be UP with latest build.<br/>2) RPVST should be enabled successfully.<br/>3) DUT should not trigger any TCN changes.<br/>   Verify that the port will move to Forwarding state immediately without moving to Listening and Learning states.** |

#### 3.2.25 Verify that interface with lower interface index will be in forwarding state and port having higher interface index will be in Blocking state and root port is selected based on port priority of the designated bridge. 



| **Test ID**    | **ft_rpvst_portpriority**                                    |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that interface with lower interface index will be in forwarding state and port having higher interface index will be in Blocking state and root port is selected based on port priority of the designated bridge** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Now create VLAN 10. Make the ports 1 and 2 as trunk ports.(ex: port1 on DUT1 connected to port1 on DUT1 and port2 on DUT1 connected to port2 of DUT2)<br/>3. Set spanning tree mode as RPVST.<br/>4. Configure DUT1 as Root Bridge for VLAN 1, 10.<br/>5. Check the port roles and status.<br/> 6. If port1 on DUT2 is forwarding after step5, Change the port priority for vlan 10 on port2 of Root switch DUT1 to a lower value.<br/><br/><br/>Expected Behavior:<br/>1. Verify that the DUTs are up with default configuration.<br/>2. Verify that VLANs are created successfully.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that DUT1 is elected as Root Bridge for VLAN 1, 10.<br/>5. On DUT2 which is non root bridge, verify that port which is receiving BPDU with lowest interface ID from DUT1 will be in forwarding state and other port will be in Blocking state.<br/> 6. Verify that now port2 on DUT2 will be in forwarding state as this port is receiving a better BPDU with less port priority.** |

#### 3.2.26 Verify RPVST convergence over untagged ports. 



| **Test ID**    | **ft_rpvst_ports_untagged**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify RPVST convergence over untagged ports**             |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans. Configure ports as untagged ports in one of the vlan<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans as untagged in one of the vlan.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.** |

#### 3.2.27 Verify the port root guard feature in RPVST mode 



| **Test ID**    | **ft_rpvst_rootguard**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the port root guard feature in RPVST mode**         |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUT with default configuration.<br/>2. Enable spanning tree on DUT and PD.<br/>3. Create VLAN 10 on DUT and PD, make participation of interface for VLAN 10.<br/>4. Set spanning tree mode as RPVST globally for VLAN 10 and on interfaces.<br/>5. Make partner as root bridge.<br/>6. Now enable root guard on interface of DUT.<br/>7. Now check the port status on DUT.<br/>8. Remove root guard on DUT interface<br/><br/>Expected Behavior:<br/>1. DUT and Partner is up with default configuration.<br/>2. Verify that spanning tree is enabled.<br/>3. Verify that VLAN is created.<br/>4. Verify that spanning tree is set as RPVST.<br/>5. Verify that partner is root bridge.<br/>6. Verify that root guard is enabled.<br/>7. Verify that DUT interface connected to partner goes to Blocking/inconsistent state as soon as it receives superior BPDU from partner.<br/>8. Verify that RPVST convergence is fine.** |

#### 3.2.28 Verify that another switch is elected as the root in the event of root switch failure by disabling RPVST globally or disabling at vlan level on the ROOT Bridge. 



| **Test ID**    | **ft_rpvst_rootswitch_disable_rpvst**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that another switch is elected as the root in the event of root switch failure by disabling RPVST globally or disabling at vlan level on the ROOT Bridge** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20  and make all ports participate in vlan 10,20in all devices.<br/>3. Set spanning tree mode as RPVST globally and in vlan  configured.<br/>4. Configure DUT1 as root bridge  by changing the lowest priority for VLAN 1 and VLAN 10 and DUT 3 as root bridge in VLAN 20. Configure such that DUT2 will be the next eligible root switch for VLAN1,10 and VLAN20 in case of root swich failure.<br/>5.Now try  the event of root switch failure by disabling RPVST globally on  the root switch  DUT1.<br/>6. Enable RPVST globally on DUT1.<br/>7.Now try the event of root switch failure by disabling RPVST on VLAN 10 on the root switch i,e DUT1.<br/> 8. Enable RPVST on VLAN10 and VLAN1 on DUT1.<br/> 9. Now try the event of root switch failure by disabling RPVST on VLAN 20 i,e DUT3.<br/> 10. Enable RPVST on VLAN 20 on DUT3.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in vlan.<br/>3. Verify that spanning tree mode is set as PVST.<br/>4. Verify that switch become the root switch.<br/>5. Verify that DUT2 becomes root switch for instance1 and 10 when DUT1 is  not sending PVST BPDU's.<br/>6. Verify that DUT1 will become the root bridge for instances 1 and 10.<br/> 7. Verify that DUT2 becomes root switch for instance1 and 10 when DUT1 is not sending PVST BPDU's in VLAN 10 and 1.<br/> 8. Verify that PVST convergence fine and DUT1 becomes root for VLAN 10.<br/> 9. Verify that DUT2 becomes root switch for instance20 when DUT3 is not sending PVST BPDU's in VLAN 20<br/> 10. Verify that PVST convergence fine and DUT3 becomes root for VLAN 20.** |

#### 3.2.29 Verify that another switch is elected as the root in the event of root switch failure by removing all the ports in VLAN instance in RPVST topology.



| **Test ID**    | **ft_rpvst_rootswitch_noswitchport**                         |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that another switch is elected as the root in the event of root switch failure by removing all the ports in VLAN instance in RPVST topology.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20  and make all ports participate in vlan 10,20in all devices.<br/>3. Set spanning tree mode as RPVST globally and in vlan  configured.<br/>4. Configure DUT1 as root bridge  by changing the lowest priority for VLAN 1 and VLAN 10 and DUT 3 as root bridge in VLAN 20. Configure such that DUT2 will be the next eligible root switch for VLAN1,10 and VLAN20 in case of root switch failure.<br/>5.Now try  the event of root switch failure by removing all ports (no switchport) on VLAN 10 on  the root switch  i,e DUT1.<br/>6. Participate ports  on VLAN10 and VLAN1 on DUT1.(switch port mode trunk)<br/>7. Now try the event of root switch failure by removing all ports on  RPVST on  VLAN 20 (no switchport)i,e DUT3.<br/>8. Participate ports in  RPVST on VLAN 20 on DUT3. (switchport mode trunk)<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in vlan.<br/>3. Verify that spanning tree mode is set as PVST.<br/>4. Verify that switch become the root switch.<br/>5. Verify that DUT2 becomes root switch for instance1 and 10 when DUT1 is  not sending PVST BPDU's in VLAN 10 and 1.<br/>6. Verify that PVST convergence fine and DUT1 becomes root for VLAN 10 and VLAN 1.<br/>7. Verify that DUT2 becomes root switch for instance20 when DUT3 is  not sending PVST BPDU's in VLAN 20<br/>8. Verify that PVST convergence fine and DUT3 becomes root for VLAN 20.** |

#### 3.2.30 Verify in a looped scenario  switch with highest mac address has only one forwarding port and others are blocking.



| **Test ID**    | **ft_rpvst_blocked_switch**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify in a looped scenario  switch with highest mac address has only one forwarding port and others are blocking** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1) Create multiple vlans.<br/>2)  Configure bridge priority of 0 on one switch per vlan<br/>3) Check for blocked switch per instance<br/><br/>Expected result:<br/>1) Verify vlan config is successful.<br/>2) Verify corresponding vlan configured with bridge priority 0 should become root switch for that vlan.<br/>3) Per every vlan instance, switch having highest mac address will become blocked switch. Should have one forwarding ports and other ports are blocking.** |

#### 

#### 3.2.31 Verify that another switch is elected as the root in the event of root switch failure by shutting down the port in RPVST topology.



| **Test ID**    | **ft_rpvst_rootswitch_shut**                                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that another switch is elected as the root in the event of root switch failure by shutting down the port in RPVST topology.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10, and make all ports participate in vlan 10 in all devices.<br/>3. Set spanning tree mode as RPVST globally and in vlan  configured.<br/>4. Configure DUT1 as root bridge  by changing the lowest priority and DUT2 has next eligible priority for becoming root switch.<br/>5. shut down  all the links connected on DUT1 <br/>6. No shut down all the links connected on DUT1. <br/><br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in vlan.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switch become the root switch<br/>5. Verify that DUT1  is isolated from topology and is shown as Root .Also DUT2 will become root switch as it has better priority than DUT3<br/>6. verify that  DUT1 becomes root switch and DUT2 , DUT3 becomes  non root switches.** |

#### 3.2.32 Verify that with RPVST configured fdb flush happens in the instance for which there is a topology change and other instances fdb is not flushed.



| **Test ID**    | **ft_rpvst_single_instance_fdb_flush**                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that with RPVST configured fdb flush happens in the instance for which there is a topology change and other instances fdb is not flushed.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>   Learnt Unicast traffic:<br/>   Instance1 for vlan10: DUT2<->DUT3 <br/>   Instance2 for vlan20: DUT3<->DUT1 <br/>   Instance3 for vlan30: DUT2<->DUT1 <br/>6. Stop traffic and check mac entries for all instances.<br/>7. For vlan instance 10, on DUT3 change port cost of Root port to a higher value.<br/>8. Start traffic for all instances.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>    Instance1 for vlan10: traffic should go through DUT2->DUT1->DUT3<br/>   Instance2 for vlan20: traffic should go through DUT3->DUT2->DUT1<br/>   Instance3 for vlan30: traffic should go through DUT2->DUT3->DUT1<br/>6. Verify that mac entries are learnt fine for all the instances.<br/>7. Verify that port state of discarding port for instance1 on DUT3 is changed to forwarding and traffic for this instance should take the new path.<br/>   Verify that there are no state/role changes for other instances.<br/>   Verify that mac entries of vlan10 instance are flushed due to topology change and mac entries of other instances should not be flushed.<br/>8. Verify that traffic for instance10 should take the new path now and mac should be learnt fine.<br/>   Verify that traffic for other instances is forwarded fine.** |

#### 3.2.33 Verify RPVST timers are working fine.



| **Test ID**    | **ft_rpvst_timers**                                          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify RPVST timers are working fine.**                    |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Configure different non-default forward delay values in each root switches.<br/>6. Change the port cost value in each RPVST vlan instance and check forward delay convergence time in each instance.<br/>7. Configure non-default hello interval for vlan 10 on DUT1 which is root switch for vlan10.<br/>8. Check the Hello BPDUs exchange.<br/>9. Configure non-default Max age timer for vlan 10 on DUT1 which is root switch for vlan10.<br/> 10. Check for convergence for instance vlan20.<br/> 11. Check for convergence for instance vlan10<br/><br/><br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>5. Verify that configuration is done successfully.<br/>    i)for instance vlan10, all DUTs will honor fwd delay time configured on DUT1<br/>    ii)for instance vlan20, all DUTs will honor fwd delay time configured on DUT2<br/>    iii)for instance vlan20, all DUTs will honor fwd delay time configured on DUT3<br/>6. Verify that forward delay timer is working properly in each instance i.e port is moving to forwarding state as per the fwd delay configured on that vlan instance.<br/>7. Verify that configuration is done successfully.<br/>  i)for instance vlan10, all DUTs will honor Hello time configured on DUT1<br/>  ii)for instance vlan20,30 all DUTs will have default hello time.<br/>  <br/> 8. Verify that Hello time for instance vlan10 is as per the configured value and for other vlan instances hello time is default.<br/>9. Verify that configuration is done successfully.<br/>  i)for instance vlan10, all DUTs will honor Max age time configured on DUT1<br/>  ii)for instance vlan20,30 all DUTs will have default Max age time.<br/>  <br/> 10. Verify that vlan 20 convergence time will be ~(Max age default timer + 2*fwd delay timer)<br/> 11. Verify that vlan 10 convergence time will be ~(Configured Max age timer + 2*fwd delay timer)** |

#### 3.2.34 Verify that end to end unlearnt traffic is forwarded fine for a given vlan instance



| **Test ID**    | **ft_rpvst_unlearnt_unicast_traffic**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that end to end unlearnt traffic is forwarded fine for a given vlan instance** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1) Create multiple vlans.<br/>2)  Configure bridge priority of 0 on one switch per vlan<br/>3) Check convergence of each vlan<br/>4) Send unlearnt learnt traffic for a specific vlan.<br/><br/>Expected result:<br/>1) Verify vlan config is successful.<br/>2) Verify corresponding vlan configured with bridge priority 0 should become root switch for that vlan.<br/>3) Per every vlan instance, <br/>  a) There is only one root switch<br/> b) switch with highest mac address will become blocked switch for that vlan instance.<br/>c) Blocked switch should have one forwarding port and other ports should be blocking.<br/>4) Verify that traffic is forwarded to all TG ports part of the traffic vlan.** |

#### 3.2.35 Verify RPVST  behavior after adding/removing  few more VLANs.



| **Test ID**    | **ft_rpvst_vlan_add_delete**                                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify RPVST  behavior after adding/removing  few more VLANs** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>6. Add VLANs 40,50,60 and enable PVST<br/>7. Now send traffic for all instances.<br/>8. Remove VLANs 40,50,60.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>6. Verify that RPVST is enabled for these instances and converges fine for all the VLANs in all DUTs.<br/>   Verify that DUT having the lowest MAC will become Root bridge for VLANs 40,50 and 60.<br/>   and traffic of previously configured instances is not disrupted when these new vlans are added.<br/>7.Verify that traffic is forwarded fine for all the VLANs.<br/>8. Verify that PVST instance for VLANs 40,50,60 is removed.<br/>   Traffic for instances VLAN 10,20 and 30 is not disrupted.** |

#### 3.2.36 Verify that shut and no shut of a discarding port continues to keep discarding port in discarding state.



| **Test ID**    | **ft_rpvst_blockedport_shut_noshut**                         |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that shut and no shut of a blocked port continues to keep blocked port in blocked state** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1) Create multiple vlans.<br/>2)  Configure bridge priority of 0 on one switch per vlan<br/>3) Check for blocked switch per instance<br/>4) Send learnt unicast traffic and shut and no shut of blocking port<br/>5) Verify blocked port state again<br/><br/>Expected result:<br/>1) Verify vlan config is successful.<br/>2) Verify corresponding vlan configured with bridge priority 0 should become root switch for that vlan.<br/>3) Per every vlan instance, switch having highest mac address will become blocked switch. Should have one forwarding ports and other ports are blocking.<br/>4) Verify that traffic is not impacted.<br/>5) Verify blocked port is moved to blocked state again after shut and no shut** |

#### 3.2.37 Verify that shut of blocked port does not have any impact on the traffic.



| **Test ID**    | **ft_rpvst_blockedport_shut_traffic**                        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that shut of blocked port does not have any impact on the traffic** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1) Create multiple vlans.<br/>2)  Configure bridge priority of 0 on one switch per vlan<br/>3) Check for blocked switch per instance<br/>4) Send learnt unicast traffic and shut and no shut of blocking port<br/><br/>Expected result:<br/>1) Verify vlan config is successful.<br/>2) Verify corresponding vlan configured with bridge priority 0 should become root switch for that vlan.<br/>3) Per every vlan instance, switch having highest mac address will become blocked switch. Should have one forwarding ports and other ports are blocking.<br/>4) Verify that traffic is not impacted.** |

## 

## 3.3 Functional_interaction

#### 3.3.1 Verify the RPVST  convergence in MLAG topology with different root bridges for different   RPVST instances.

### 

| **Test ID**    | **ft_rpvst_interop_mlag_diff_rootbridge_diffinstances**      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST  convergence in MLAG topology with different root bridges for different   RPVST instances.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Pre-configuration on DUTs P, S:<br/>===============================<br/>Below configuration has to be performed in both P, S DUTs:<br/>1.    Add a port P3 and S3 into port-channel 3/3.<br/>2.    Add P1 and S1  physical ports into port-channel 3/1.<br/>3.    Configure lag 3/1 as VPC1.<br/>4.    Add P2 and S2 physical ports into port-channel  3/2.<br/>5.    Configure lag 3/2 as VPC2.<br/>6.    Enable peer-link on lag interface 3/3.<br/>7.    Enable keep-alive on the peer-link 3/3.<br/>8.    Enable  RPVST on peer-link.<br/>9.    Configure all the ports P1 to P3 and S1 to S3 are members of two VLANs  X and Y.<br/>10.   Configure  RPVST on Vlan X and Y and also configure P as primary root Bridge and S as secondary root for VLAN X.<br/><br/>Pre-configuration on DUT A :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add A1 and A2 ports into port-channel 3/1.<br/>2.   Configure all the ports A1 to A3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan X and Y and also configure A as primary root Bridge for Vlan Y.<br/><br/>Pre-configuration on DUT B :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.    Add B1 and B2 ports into port-channel 3/2.<br/>2.   Configure all the ports B1 to B3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan X and Y.<br/><br/>Procedure:<br/>============<br/>1.    Enable Feature VPC on both P, S DUTs.<br/>2.    Enable  RPVST on all DUTs P, S,A and B.<br/>3.    Check that P is the root Bridge of the  RPVST instance of VLAN X.<br/>4.    Check that A is the root Bridge of the  RPVST instance of VLAN Y.<br/>5.    Check the  RPVST port roles and states on P, S, A and B.<br/>6.    Initiate bidirectional traffic on Ixia ports connected to the partner devices A and B for VLANs X and Y.<br/>7.    Repeat the steps 1 to 6, by configuring RPVST on all P, S,A and B.<br/><br/>EXPECTED BEHAVIOR:<br/>==================<br/>1. Verify that mlag is enabled in both the devices, device with lower mac-address elected as primary and other one as secondary.<br/>2. Verify that  RPVST is enabled in all the devices. <br/>3. Verify that P is primary of MLAG and Root bridge of  RPVST instance VLAN X.<br/>4. Verify that P is primary of MLAG and Root bridge of  RPVST instance VLAN  Y.<br/>5.a)Verify the below state roles of VLAN X<br/>    For VLAN X, lag1(P1,S1) and lag3 (P2,S2) on the primary MLAG device P are in designated/forwarding state;<br/>    lag1(A1,A2) on MLAG partner A is in root/forwarding state;<br/>    Lag2(B1,B2) on MLAG partner B is in root/forwarding state;<br/>    A3 is in designated/forwarding; B3 is in alternate/blocking state;<br/>    Peer-link on Primary MLAG device (P3) is in designated/forwarding state;<br/>    peer-link on Secondary MLAG device (S3) is in root/forwarding state.<br/>5.b)Verify the below state roles of VLAN Y.<br/>   For VLAN Y, lag1(A1,A2) and A3 on the MLAG partner A are in designated/forwarding state. <br/>    Lag1(P1,S1) on the primary MLAG device is in root/forwarding state. <br/>    Lag2(P2,S2) on the primary MLAG device is in designated/forwarding state. <br/>    Lag2(B1,B2) on MLAG partner B is in root/forwarding state; <br/>    B3 is alternate/discarding state. Peer-link on Primary MLAG device (P3) is in designated/forwarding state;<br/>    peer-link on Secondary MLAG device (S3) is in root/forwarding state.<br/>6. Verify that Traffic flow is fine on both directions for both VLANs.<br/>7. Verify the steps 1 to 6.** |

#### 3.3.2 Verify that in  RPVST topology traffic is being forwarded over peer-link to the end station, in case of all MLAG links failure scenario.

### 

| **Test ID**    | **ft_rpvst_interop_mlag_link_fail**                          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that in  RPVST topology traffic is being forwarded over peer-link to the end station, in case of all MLAG links failure scenario.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Pre-configuration on DUTs P, S:<br/>===============================<br/>Below configuration has to be performed in both P, S DUTs:<br/>1.    Add a port P3 and S3 into port-channel 3/3.<br/>2.    Add P1 and S1 physical ports into port-channel 3/1.<br/>3.    Configure lag 3/1 as VPC1.<br/>4.    Add P2 and S2 physical ports into port-channel 3/2.<br/>5.    Configure lag 3/2 as VPC2.<br/>6.    Enable peer-link on lag interface 3/3.<br/>7.    Enable keep-alive on the peer-link 3/3.<br/>8.    Enable  RPVST on peer-link.<br/>9.    Configure all the ports P1 to P3 and S1 to S3 as VLAN X.<br/>10.   Configure P as primary root Bridge and S as secondary root for the VLAN X.<br/><br/>Pre-configuration on DUT A :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add A1 and A2 ports into port-channel 3/1.<br/>2.   Configure all the ports A1 to A3 as member of VLAN X.<br/>3.   Set spanning tree mode as RPVST globally.<br/><br/>Pre-configuration on DUT B :<br/>========================<br/>DUT B need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add B1 and B2 ports into port-channel 3/2.<br/>2.   Configure all the ports B1 to B3 as member of VLAN.<br/>3.   Set spanning tree mode as  RPVST.<br/><br/><br/>Procedure:<br/>============<br/>1.    Enable Feature VPC on both P, S DUTs.<br/>2.    Enable PVSTP on all DUTs P, S,A and B.<br/>3.    Check that P is the root Bridge of the RPVST instance VLAN X.<br/>4.    Check the  RPVST port roles and states on P, S, A and B.<br/>5.    Initiate bidirectional traffic on Ixia ports connected to the partner devices A and B for VLANs X.<br/>6.    Shutdown the interfaces P2 on Primary MLAG device P.<br/>7.    Check that MLAG will program Peer-link to allow traffic over.<br/>8.    Now no shutdown the port which shutdown on step 6, check peer-link traffic.<br/>9.    Repeat the steps 1 to 8, by configuring RPVST on all P, S,A and B.<br/><br/>EXPECTED BEHAVIOR:<br/>==================<br/>1. Verify that mlag is enabled in both the devices, device with lower mac-address elected as primary and other one as secondary.<br/>2. Verify that PVSTP is enabled in all the devices. <br/>3. Verify that P is primary of MLAG and Root bridge of RPVST instance VLAN1.<br/>4. Verify the below state roles <br/>    For VLAN X, lag1(P1,S1) and lag3 (P2,S2) on the primary MLAG device P are in designated/forwarding state;<br/>    lag1(A1,A2) on MLAG partner A is in root/forwarding state;<br/>    Lag2(B1,B2) on MLAG partner B is in root/forwarding state;<br/>    A3 is in designated/forwarding; B3 is in alternate/blocking state;<br/>    Peer-link on Primary MLAG device (P3) is in designated/forwarding state;<br/>    peer-link on Secondary MLAG device (S3) is in root/forwarding state.<br/>5a.Verify that spanning tree topology is converged and traffic is flowing properly with no looping from A to B.<br/>5b.Verify that no traffic should forwarded over peer-link.<br/>6. Verify that port is shutdown.<br/>7. Verify that traffic is passing over peer-link (check by using the counters).<br/>8. Verify that port become UP, and now Peer-link should block the traffic over.<br/>9. Verify the steps 1 to 8.** |

#### 3.3.3 Verify that  RPVST convergence in the MLAG topology  when Partner is root bridge of the topology

### 

| **Test ID**    | **ft_rpvst_interop_mlag_partner_rootbridge**                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that  RPVST convergence in the MLAG topology  when Partner is root bridge of the topology** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Pre-configuration on DUTs M1(P), M2(S):<br/>===============================<br/>Below configuration has to be performed in both M1, M2 DUTs:<br/>1.    Add a port P3 and S3 into port-channel 3/3.<br/>2.    Add P1 and S1  physical ports into port-channel 3/1.<br/>3.    Configure lag 3/1 as VPC1.<br/>4.    Add P2 and S2 physical ports into port-channel  3/2.<br/>5.    Configure lag 3/2 as VPC2.<br/>6.    Enable peer-link on lag interface 3/3.<br/>7.    Enable keep-alive on the peer-link 3/3.<br/>8.    Enable  RPVST on peer-link.<br/>9.    Configure all the ports P1 to P3 and S1 to S3 are members of Vlan1.<br/>10.   Configure  RPVST on Vlan1 .<br/><br/>Pre-configuration on DUT A :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add A1 and A2 ports into port-channel 3/1.<br/>2.   Configure all the ports A1 to A3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan1 and Configure A as primary root Bridge for PVST VLAN1.<br/><br/>Pre-configuration on DUT B :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add B1 and B2 ports into port-channel 3/2.<br/>2.   Configure all the ports B1 to B3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan1.<br/><br/>Procedure:<br/>============<br/>1.    Enable Feature VPC on both M1, M2 DUTs.<br/>2.    Enable  RPVST on all DUTs M1, M2,A and B.<br/>3.    Check that A is the root Bridge of the  RPVST instance VLAN1.<br/>4.    Check the  RPVST port roles and states on M1, M2, A and B.<br/><br/>EXPECTED BEHAVIOR:<br/>==================<br/>1. Verify that mlag is enabled in both the devices, device with lower mac-address elected as primary and other one as secondary.<br/>2. Verify that  RPVST is enabled in all the devices. <br/>3. Verify that M1 is primary of MLAG and Root bridge of  RPVST instance VLAN1.<br/>4.  Verify the below state roles <br/>    For VLAN1, lag1(A1,A2) and A3 on the MLAG partner A are in designated/forwarding state. <br/>    Lag1(P1,S1) on the primary MLAG device is in root/forwarding state. <br/>    Lag2(P2,S2) on the primary MLAG device is in designated/forwarding state. <br/>    Lag2(B1,B2) on MLAG partner B is in root/forwarding state; <br/>    B3 is alternate/discarding state. Peer-link on Primary MLAG device (P3) is in designated/forwarding state;<br/>    peer-link on Secondary MLAG device (S3) is in root/forwarding state.** |

#### 3.3.4 Verify that  RPVST convergence in the MLAG topology  when Partner is root bridge of the topology and failover happens in the Partner device.

### 

| **Test ID**    | **ft_rpvst_interop_mlag_partner_rootbridge_failover**        |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that  RPVST convergence in the MLAG topology  when Partner is root bridge of the topology and failover happens in the Partner device.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Pre-configuration on DUTs P, S:<br/>===============================<br/>Below configuration has to be performed in both P, S DUTs:<br/>1.    Add a port P3 and S3 into port-channel 3/3.<br/>2.    Add P1 and S1  physical ports into port-channel 3/1.<br/>3.    Configure lag 3/1 as VPC1.<br/>4.    Add P2 and S2 physical ports into port-channel  3/2.<br/>5.    Configure lag 3/2 as VPC2.<br/>6.    Enable peer-link on lag interface 3/3.<br/>7.    Enable keep-alive on the peer-link 3/3.<br/>8.    Enable  RPVST on peer-link.<br/>9.    Configure all the ports P1 to P3 and S1 to S3 are members of Vlan1.<br/>10.   Configure  RPVST on Vlan1 .<br/><br/>Pre-configuration on DUT A :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add A1 and A2 ports into port-channel 3/1.<br/>2.   Configure all the ports A1 to A3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan1 and Configure A as primary root Bridge for PVST VLAN1.<br/><br/>Pre-configuration on DUT B :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add B1 and B2 ports into port-channel 3/2.<br/>2.   Configure all the ports B1 to B3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan1.<br/><br/>Procedure:<br/>============<br/>1.    Enable Feature VPC on both P, S DUTs.<br/>2.    Enable  RPVST on all DUTs P, S,A and B.<br/>3.    Check that A is the root Bridge of the PVSTP instance VLAN1.<br/>4.    Check the  RPVST port roles and states on P, S, A and B.<br/>5.    Initiate bidirectional traffic on Ixia ports connected to the partner devices A and B.<br/>6.    Power cycle the Partner in the topology to observed Primary fail-over behavior is as mentioned in the verification step.<br/>7.    Wait till the Power cycled device P comes up, check the behavior is as mentioned in the verification step.<br/>8.    Repeat the steps 1 to 7, by configuring RPVST on all P, S,A and B.<br/><br/><br/>EXPECTED BEHAVIOR:<br/>==================<br/>1. Verify that mlag is enabled in both the devices, device with lower mac-address elected as primary and other one as secondary.<br/>2. Verify that PVSTP is enabled in all the devices. <br/>3. Verify that P is primary of MLAG and Root bridge of PVST instance VLAN1.<br/>4.  Verify the below state roles <br/>    For VLAN1, lag1(A1,A2) and A3 on the MLAG partner A are in designated/forwarding state. <br/>    Lag1(P1,S1) on the primary MLAG device is in root/forwarding state. <br/>    Lag2(P2,S2) on the primary MLAG device is in designated/forwarding state. <br/>    Lag2(B1,B2) on MLAG partner B is in root/forwarding state; <br/>    B3 is alternate/discarding state. Peer-link on Primary MLAG device (P3) is in designated/forwarding state;<br/>    Peer-link on Secondary MLAG device (S3) is in root/forwarding state.<br/>5. Verify that Traffic flow is fine on both directions.<br/>6.  a) Verify that for VLAN1, election happens and a new root is elected. The spanning tree roles/states are changed.<br/>    b) Loss observed for the bi directional traffic between A and B.<br/>7. Verify that once A comes UP For VLAN1, the MLAG partner device A will become root once it is UP; its interfaces lag1(A1,A2) and A3 move to designated/discarding state.<br/>8. Verify the steps 1 to 7.** |

#### 3.3.5 Verify that  RPVST convergence in the MLAG topology  when Primary is root bridge of the topology.

### 

| **Test ID**    | **ft_rpvst_interop_mlag_primary_rootbridge**                 |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that  RPVST convergence in the MLAG topology  when Primary is root bridge of the topology.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Pre-configuration on DUTs M1(P), M2(S):<br/>===============================<br/>Below configuration has to be performed in both M1, M2 DUTs:<br/>1.    Add a port P3 and S3 into port-channel 3/3.<br/>2.    Add P1 and S1  physical ports into port-channel 3/1.<br/>3.    Configure lag 3/1 as VPC1.<br/>4.    Add P2 and S2 physical ports into port-channel  3/2.<br/>5.    Configure lag 3/2 as VPC2.<br/>6.    Enable peer-link on lag interface 3/3.<br/>7.    Enable keep-alive on the peer-link 3/3.<br/>8.    Enable  RPVST on peer-link.<br/>9.    Configure all the ports P1 to P3 and S1 to S3 are members of Vlan1.<br/>10.   Configure  RPVST on Vlan1 and also configure M1 as primary root Bridge and M2 as secondary root.<br/><br/>Pre-configuration on DUT A :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.   Add A1 and A2 ports into port-channel 3/1.<br/>2.   Configure all the ports A1 to A3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan1.<br/><br/>Pre-configuration on DUT B :<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.    Add B1 and B2 ports into port-channel 3/2.<br/>2.   Configure all the ports B1 to B3 to the members of Vlan1.<br/>3.   Configure  RPVST on Vlan1.<br/><br/>Procedure:<br/>============<br/>1.    Enable Feature VPC on both M1, M2 DUTs.<br/>2.    Enable  RPVST on all DUTs M1, M2,A and B.<br/>3.    Check that M1 is the root Bridge of the  RPVST instance VLAN1.<br/>4.    Check the  RPVST port roles and states on M1, M2, A and B.<br/>5.    Repeat the steps 1 to 4 , by configuring RPVST on all M1, M2,A and B.<br/><br/>EXPECTED BEHAVIOR:<br/>==================<br/>1. Verify that mlag is enabled in both the devices, device with lower mac-address elected as primary and other one as secondary.<br/>2. Verify that  RPVST is enabled in all the devices. <br/>3. Verify that M1 is primary of MLAG and Root bridge of PVST instance VLAN1.<br/>4.  Verify the below state roles <br/>    For VLAN1, lag1(P1,S1) and lag3 (P2,S2) on the primary MLAG device M1 are in designated/forwarding state;<br/>    lag1(A1,A2) on MLAG partner A is in root/forwarding state;<br/>    Lag2(B1,B2) on MLAG partner B is in root/forwarding state;<br/>    A3 is in designated/forwarding; B3 is in alternate/blocking state;<br/>    Peer-link on Primary MLAG device (P3) is in designated/forwarding state;<br/>    peer-link on Secondary MLAG device (S3) is in root/forwarding state.<br/>5. Verify the steps 1 to 4.** |

#### 3.3.6 Verify the traffic in East to west scenario with a link (non peer-link) between primary and secondary in  RPVST topology. 

### 

| **Test ID**    | **ft_rpvst_interop_mlag_primary_secondary_topology**         |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the traffic in East to west scenario with a link (non peer-link) between primary and secondary in  RPVST topology.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Pre-configuration on DUTs P, S:<br/>===============================<br/>Below configuration has to be performed in both P,S DUTs:<br/>1.    Add a port 0/12 into port-channel 3/1.<br/>2.    Add 4 physical ports into port-channel 3/2.<br/>3.    Configure lag 3/2 as mlag.<br/>4.    Enable peer-link on lag interface 3/1.<br/>5.    Enable keep-alive on the peer-link 3/1.<br/>6.    Enable spanning-tree on peer-link.<br/>7.    Configure STP such that P becomes Root bridge.<br/>8. A link between primary and secondary with stp enabled.<br/><br/>Pre-configuration on DUT A:<br/>========================<br/>DUT A need not be a MLAG aware DUT, It could be any switch.<br/>1.    Add 8 ports into port-channel 3/1.<br/><br/>Procedure:<br/>============<br/>1. Enable mlag on both P, S DUTs.<br/>2. Enable STP on all DUTs P,S,B,D,E,F,H,C and A. <br/>3. Check the STP port states on S and P.<br/>4. Capture STP BPDUs on A and D on ports connected to both Primary and secondary.<br/>5. Capture the STP BPDUs on devices C,E and B<br/>6. Check traffic destined form H to F and H to C.<br/>7. Disable STP on non peer link between P and S and include it in required vlans.<br/>8. Repeat the test with  RPVSTP.<br/><br/>EXPECTED BEHAVIOR:<br/>==================<br/>1. Verify that mlag is enabled in both the devices, device with lower mac-address elected as primary and other one as secondary.<br/>2. Verify that STP is enabled in all the devices. <br/>3.  The peer-link on S which connects to P is root port and peer-link on P is the designated port. Since peer-link is always kept forwarding state and spanning tree is enabled on the peer-link, the orphan link between P and S goes into discarding, even though the same is configured with the lower cost.<br/>4. Verify that devices A and D receive STP BPDUs on VPC interfaces with Root identifier and Designated Bridge identifier as VMAC.<br/>5. Device  C and E should receive STP BPDUS with Root Identifier : VMAC and designated bridge Identifier as switch MAC of secondary i.e.mac address of device S. <br/>Device  B should receive STP BPDUS with Root Identifier : VMAC and designated bridge Identifier as switch MAC of device A.<br/>6. Traffic destined from H to F and H to C will not pass through since the peer-link does not allow data traffic on it and the other link that connects P and S is in discarding state<br/>7. Verify that traffic which was not flowing earlier starts flowing fine.<br/>8. Same results as with STP.<br/><br/>Note:<br/>Administrator should take care  that there are no loops with this connection."** |

#### 3.3.7 Verify the RPVST is converging fine and LLDP info is shown correctly when LLDP is enabled. 

### 

| **Test ID**    | **ft_rpvst_lldp**                                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST is converging fine and LLDP info is shown correctly when LLDP is enabled.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create VLAN 10, 20, 30.<br/>3. Set spanning tree mode as RPVRST globally for created VLANs and on interfaces.<br/>4. Configure DUT1 as root bridge for instance VLAN 10, DUT2 for VLAN 20, DUT3 for VLAN 30.<br/>5. Check RPVST convergence.<br/>6. Enable LLDP on all DUTs globally and also on the interfaces.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that VLAN is created.<br/>3. Verify that spanning tree is set as RPVST.<br/>4. Verify that DUT1 is elected as root bridge for VLAN 10, DUT2 for VLAN 20, DUT3 for VLAN 30.<br/>5. Verify that RPVST converged fine in all the instances.<br/>6. Verify that LLDP information is shown correctly in all the DUTs.<br/>   RPVST blocking ports should also show the lldp neighbor ship info.** |

#### 3.3.8 Verify that RPVST convergence is fine with portchannel interfaces in the topology. 

| **Test ID**    | **ft_rpvst_portchannel_convergence**                         |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that RPVST convergence is fine with portchannel interfaces in the topology** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUT with default configuration.<br/>2. Enable spanning tree on DUT and PD.<br/>3. Set spanning tree mode as RPVST globally for native VLAN and VLAN 10 and on interfaces.<br/>4. Make partner as root bridge.<br/>5. Configure 2 ports to be part of a port-channel.<br/>6. Check STP BPDUs.<br/>7. Check convergence over portchannel interface.<br/><br/>Expected Behavior:<br/>1. DUT and Partner is up with default configuration.<br/>2. Verify that spanning tree is enabled.<br/>3. Verify that spanning tree is set as RPVST.<br/>4. Verify that config is successful.<br/>5. Verify that port-channel config is successful.<br/>6. Verify that STP BPDUs are are sent and forwarded fine.<br/>7. Verify that RPVST has converged fine in all the instances and portchannel interfaces show correct port states.** |

#### 3.3.9 Verify there  is no change in portchannel cost when member is deleted and added. 

| **Test ID**    | **ft_rpvst_portchannel_cost_member_remove_add**              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify there  is no change in portchannel cost when member is deleted and added** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Have 3 back to back links between each DUT pair and configure these ports part of lag.<br/>3. Create vlans 10,20,30 and make all port-channel ports participate in all vlans.<br/>4. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>5. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>6. Check the port-channel interface cost.<br/>7. Send traffic for all the vlan instances.<br/>8. On port-channel ports on link between DUT1 and DUT3, remove two member ports from port-channel.<br/>9. Check RPVST convergence.<br/>10. Add the ports back to port-channel on DUT1 and DUT3.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that port-channel config is successful and is UP on all DUTs.<br/>3. Verify vlan creation is successful and lag ports are participated in all vlans.<br/>4. Verify that spanning tree mode is set as RPVST.<br/>5. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>6. Verify that port-channel interfaces cost is correctly displayed.<br/>7. Verify that traffic is forwarded fine as per the instance topology.<br/>8. Verify that port-channel on DUT1 and DUT3 has only one link now.<br/>   Verify that the cost is correctly displayed.<br/>9. Verify there is no topology change for any instance and cost of the port-channel between DUT1 and DUT3 remains same even after removing members.<br/> 10. Verify there is no topology change for any instance and cost of the port-channel between DUT1 and DUT3 remains same even after adding members** |

#### 3.3.10 Verify that while addition/removal of member ports to a lag there are no topology changes when RPVST is configured.

| **Test ID**    | **ft_rpvst_portchannel_no_tc_member_del_add**                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that while addition/removal of member ports to a lag there are no topology changes when RPVST is configured.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Have 2 back to back links between each DUT pair and configure these ports part of port-channel.<br/>   Configure port-channel min links as 1.<br/>3. Create vlans 10,20,30 and make all port-channel ports participate in all vlans.<br/>4. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>5. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>6. Check the port-channel interface cost.<br/>7. Send traffic for all the vlan instances.<br/>8. On the port-channel between DUT1 and DUT3 remove one port-channel member.<br/>8. Now add back the member port on port-channel between DUT1 and DUT3<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that lag config is successful and is UP on all DUTs.<br/>   Verify that lag min links config is successful.<br/>3. Verify vlan creation is successful and lag ports are participated in all vlans.<br/>4. Verify that spanning tree mode is set as RPVST.<br/>5. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>6. Verify that lag interfaces cost is correctly displayed.<br/>7. Verify that traffic is forwarded fine as per the instance topology.<br/>8. Verify that lag between DUT1 and DUT3 has only one member.<br/>   Verify that there is no change the topology for all the instances.<br/>9. Verify that lag between DUT1 and DUT3 has two members.<br/>   Verify that there is no change the topology for all the instances.** |

#### 3.3.11 Verify the RPVST over port-channel is converging fine when vlans are added or removed.

| **Test ID**    | **ft_rpvst_portchannel_vlan_add_delete**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST over port-channel is converging fine when vlans are added or removed.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional_interaction**                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Have 2 back to back links between each DUT pair and configure these ports part of port-channel.<br/>3. Create vlans 10,20,30 and make all port-channel ports participate in all vlans.<br/>4. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>5. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>6. Check the port-channel interface cost.<br/>7. Send traffic for all the vlan instances.<br/>8. Add VLANs 40,50,60 globally, participate port-channel in these VLANs on all DUTs and enable RPVST<br/>9. Now send traffic for all instances.<br/>10. Remove VLANs 40,50,60 from VLAN config.<br/>11. on DUT1 remove VLAN10 on all port-channel interfaces.<br/>12. On DUT1 add VLAN10 on port-channel interfaces.<br/><br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that port-channel config is successful and is UP on all DUTs.<br/>3. Verify vlan creation is successful and port-channel ports are participated in all vlans.<br/>4. Verify that spanning tree mode is set as RPVST.<br/>5. Verify that switches become root bridges in respective vlans i.e. DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>6. Verify that port-channel interfaces cost is correctly displayed.<br/>7. Verify that traffic is forwarded fine as per the instance topology.<br/>8. Verify that PVST is enabled for these instances and converges fine for all the VLANs in all DUTs.<br/>   Verify that DUT having the lowest MAC will become Root bridge for VLANs 40,50 and 60.<br/>   and traffic of previously configured instances is not disrupted when these new vlans are added.<br/>9.Verify that traffic is forwarded fine for all the VLANs.<br/>10. Verify that RPVST instance for VLANs 40,50,60 is removed.<br/>   Traffic for instances VLAN 10,20 and 30 is not disrupted.<br/>11. Verify that port-channel interfaces are removed from VLAN10 on DUT1.<br/>  Verify that DUT1 does not show any interfaces in instance for VLAN10.<br/>  Verify that convergence in other instances 20,30 should not be disrupted.<br/>12. Verify that port-channel interfaces are added VLAN10 on DUT1.<br/>  Verify that DUT1 shows the correct states in instance for VLAN10.<br/>  Verify that all DUTs converged fine in instance for VLAN10.** |

#### 



## 3.4 Reboot/Reload/Upgrade Test Cases
#### 3.4.1  Cold reboot

#### Verify that switch is elected back as the root in the event of root switch failure by rebooting the root switch  in RPVST topology.



| **Test ID**    | **ft_rpvst_rootswitch_reboot**                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that switch is elected back as the root in the event of root switch failure by rebooting the root switch  in RPVST topology.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>6. Save and reload one of the DUT (ROOT).<br/>7. Send traffic for all the vlan instances.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>6. Verify that rebooted DUT (ROOT) is booted up fine with the previously saved config<br/>7. Verify that traffic is forwarded fine as per the instance** |

#### 

#### 3.4.2 Config reload

#### Verify the RPVST convergence by reloading the DUT and checking for convergence.



| **Test ID**    | **ft_rpvst_config_reload**                                   |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST convergence by reloading the DUT and checking for convergence.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functionality**                                            |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>6. Save and reload one of the DUT.<br/>7. Send traffic for all the vlan instances.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>6. Verify that rebooted DUT is booted up fine with the previously saved config<br/>7. Verify that traffic is forwarded fine as per the instance topology.** |

## 3.5 Interop

## 3.6 Serviceability	

#### 3.6.1 Verify that root guard and bpdu guard related syslog messages are correctly displayed.



| **Test ID**    | **ft_rpvst_log_events**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that root guard and bpdu guard related syslog messages are correctly displayed.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Serviceability**                                           |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure root guard on DUT3 port towards DUT1<br/>5. disable spanning tree on vlan 10 of DUT1<br/>6. disable root guard and enable RPVST on vlan 10.<br/>7. configure BPDU guard with shutdown on DUT3 port towards DUT1.<br/>8. disable bpdu guard and noshut BPDU-DIS pot<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. verify that port moves to inconsistent state as it receives superior BPSU and proper syslog should be shown.<br/>5. verify port moves to consistent state after root guard timeout.<br/>6. verify config is successful.<br/>7. Verify port moves to BPDU-DIS on receiving BPDU and proper syslog is shown.<br/>8. BPDU-DIS port is now moved to forwarding state.** |

#### 3.6.2 Verify all RPVST statistics are correctly displayed.



| **Test ID**    | **ft_rpvst_statistics**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify all RPVST statistics are correctly displayed.**     |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Serviceability**                                           |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Check RPVST convergence.<br/>5. Check Statistics.<br/>6. Clear statistics.<br/><br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>Verify that DUT with lowest mac address will become Root Bridge for all the VLANs.<br/>5. verify that statistics are incremented and correctly displayed.<br/>6. Verify that statistics are cleared.** |

#### 

## 3.7 Management

#### 3.7.1 Verify all RPVST debug commands are working fine.



| **Test ID**    | **ft_rpvst_debug_commands**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify all RPVST debug commands are working fine.**        |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Management**                                               |
| **Steps**      | **Steps :<br/><br/>  1. Bring up the SONIC switch with default configuration.<br/>2. Verify that all the debug commands listed in below document are working fine:<br/>http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/9629/9/L2/STP/SONiC+RPVST+Plus+FS.md<br/><br/>Following debug commands will be supported for enabling additional logging which can be viewed in /var/log/stplog<br/><br/>debug spanning-tree event<br/>debug spanning-tree interface <ifname><br/>debug spanning-tree verbose<br/>debug spanning-tree vlan <id><br/>debug spanning-tree reset<br/>debug spanning-tree show<br/><br/>Following debug commands will be supported for displaying internal data structures<br/>debug spanning-tree dump global<br/>debug spanning-tree dump vlan <id><br/>debug spanning-tree dump vlan interface <id> <ifname><br/><br/>3. Verify that all the debug messages are logged to  /var/log/stplog<br/>4. Verify that debug commands output is captured in tech support output.** |

#### 3.7.2 Validate RPVST is working fine with gNMI interface.

| **Test ID**    | **ft_rpvst_gNMI_mode**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Validate RPVST is working fine with gNMI interface.**      |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Management**                                               |
| **Steps**      | **Procedure<br/>=========<br/>1) Go to telemetry docker by issuing below command "docker exec -it telemetry bash"<br/>2) Perform gNMI get operation for RPVST on <YANG module> using YANG objects.<br/>3) Perform gNMI set operation for RPVST on <YANG module> using YANG objects.<br/>4) Perform gNMI POLL operation for RPVST on <YANG module> using YANG objects.<br/>5) Perform gNMI Subscribe ONCE  operation for RPVST on <YANG module> using YANG objects.<br/>6) Perform gNMI Subscribe STREAM for RPVST operation on <YANG module> using YANG objects.<br/><br/><br/>Expected Result<br/>===============<br/>1) Verify that operation is successful and command gets executed successfully. <br/>2) Verify that operation is successful and displays correct output.<br/>3) Verify that operation is successful and displays correct output.<br/>4) Verify that operation is successful and displays correct output.<br/>5) Verify that operation is successful and displays correct output.<br/>6) Verify that operation is successful and displays correct output.** |

#### 3.7.3 Validate RPVST related REST commands.

| **Test ID**    | **ft_rpvst_REST_mode**                                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Validate RPVST related REST commands..**                   |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Management**                                               |
| **Steps**      | **Test Step Procedure<br/>===================<br/><br/>1.    Perform a GET operation on RPVST using REST<br/>2.    Modify the value of the RPVST parameter using a POST operation using REST followed by a GET operation<br/>3.    Perform UPDATE and DELETE operations followed by GET operation<br/><br/>Expected Results<br/>================<br/><br/>1.    Verify that GET operation is successful, shows HTTP response 200 and DUT returns correct values for the REST call.<br/>2.    Verify that POST operation is successful, show HTTP response 200 and DUT returns correct configured values for the REST call.<br/>3.    Verify that UPDATE, DELETE operations are successful, shows HTTP response 200 and update and delete functionality worked fine.** |

#### 3.7.4 Validate RPVST is working fine with KLISH CLI interface.

| **Test ID**    | **ft_rpvst_KLISH_CLI_mode**                                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Validate RPVST is working fine with KLISH CLI interface.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Management**                                               |
| **Steps**      | **Execution Steps<br/>===============<br/><br/>1. Execute the RPVST commands  in the appropriate mode prompt.<br/>2. Execute the corresponding RPVST show command.<br/>3. Execute the RPVST commands in the other available mode prompts.<br/>4. If the command supports numerical value assignment then execute the command checking the boundary values.<br/>5. If the command supports numerical value assignment then execute the command checking the boundary values + and - 1.<br/>6. Execute the no form of the command <no_command_name>.<br/>7. Type the first 2-3 letters of each of the words of the command <command_name> and then press the Tab button after each word.<br/>8. Type ? before typing in each of the words of the command <command_name>.<br/>9. If stacking is supported and the command is meant for interface-config mode, then attach a stack member to the DUT and execute the command <command_name> using the stack member (i.e non-manager ) unit / stack member’s interface.<br/>10. Configure any non-default value on the DUT through the command <command_name> and then verify in SNMP and Web.<br/>11. Now execute the command "clear config".<br/>12. Configure any non-default value on the DUT through the command <command_name> and then save and reload the DUT.<br/>13. Configure default vlaue on the DUT through the command <command_name> and check this configued value is not appearing in running-config.<br/><br/>Expected Result<br/>===============<br/><br/>1. The command should get executed successfully and the DUT should not throw any error.<br/>2. The command should get executed successfully and the output should list appropriate default value.<br/>3. The command should not get executed successfully and the DUT should throw an error. <br/>4. The command should get executed successfully and the DUT should not throw any error.<br/>5. The command should not get executed successfully and the DUT should throw an error. <br/>6. The command should get executed successfully and the DUT should not throw any error.<br/>7. The words of the command should get auto-filled.<br/>8. The available words of the command should get displayed accompanied by appropriate help message, and Verify that there are no grammatical and spelling mistakes in the help text.<br/>9. The command should get executed successfully and the DUT should not throw any error.<br/>10. Web and SNMP should display the same non-default value. <br/>11. The configured non-default value should get reset to default and should not get displayed in "show running-config" output.<br/>12. The configured non-default value should be displayed even after the DUT is rebooted.<br/>13. Verify that this configuration is not shown under "show running-config" as it is the default configuration.** |

## 

#### 

## 3.8 Stress,Scale,Negative and Performance
#### 3.8.1 Verify RPVST convergence with MAX instances.



| **Test ID**    | **ft_rpvst_max_vlan_instances**                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify RPVST convergence with MAX instances.**             |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Scalability**                                              |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Enable spanning tree on all the DUTs.<br/>3. Create VLANs 1 to 255 on all the DUTs.<br/>4. Set spanning tree mode as RPVST globally for all the created VLANs and on interfaces.<br/>5. Verify that spanning tree is converged properly by sending and verifying the traffic to all the vlans configured.<br/>6. Shutdown and no shutdown all the interafces on any randon dut in the topology and verify that spanning tree is converged properly by sending and verifying the traffic to all the vlans configured.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that spanning tree is enabled.<br/>3. Verify that VLANs are created.<br/>4. Verify that spanning tree is set as RPVST.<br/>5. Verify that traffic is forwarded without any loss for all the vlans.<br/>6. Verify that traffic is forwarded without any loss for all the vlans.** |

#### 3.8.2 Verify the RPVST convergence by changing the bridge priorities and check for stable state.

| **Test ID**    | **ft_rpvst_stress_bridge_priority**                          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST convergence by changing the bridge priorities and check for stable state.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Stress**                                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>6. Change the bridge priority on DUT1 so that it will be root bridge for all instances.<br/>7. Change the bridge priority on DUT2 so that it will be root bridge for all instances.<br/>8. Change the bridge priority on DUT3 so that it will be root bridge for all instances.<br/>9. Configure DUT1 to be root for vlan10, DUT2 as root for vlan20 and DUT3 as root for VLAN30.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>6. Verify that DUT1 will become root bridge for all instances.<br/>7. Verify that DUT2 will become root bridge for all instances.<br/>8. Verify that DUT3 will become root bridge for all instances.<br/>9. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   Verify that RPVST converges fine in all 3 instances.<br/>   Verify that traffic is forwarded fine for all 3 instances.** |

#### 3.8.3 Verify the RPVST convergence by shut/no shut of lag interfaces for multiple times and checking for convergence.

| **Test ID**    | **ft_rpvst_stress_lag_shut_noshut**                          |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST convergence by shut/no shut of lag interfaces for multiple times and checking for convergence.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Stress**                                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Have 2 back to baack links between each DUT pair and configure these ports part of lag.<br/>3. Create vlans 10,20,30 and make all lag ports participate in all vlans.<br/>4. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>5. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>6. Send traffic for all the vlan instances.<br/>7. Shut and noshut the lag interfaces on DUT1 for 5 iterations.<br/>8. Check RPVST convergence.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that lag config is successful and is UP on all DUTs.<br/>3. Verify vlan creation is successful and lag ports are participated in all vlans.<br/>4. Verify that spanning tree mode is set as RPVST.<br/>5. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>6. Verify that traffic is forwarded fine as per the instance topology.<br/>7. Verify that lag interfaces are up after all iterations.<br/>8. Verify that RPVST converges fine in all 3 instances.<br/>9. Verify that traffic is forwarded fine for all 3 instances.** |

#### 3.8.4 Verify the RPVST convergence by shut/no shut of physical interfaces for multiple times and checking for convergence.

| **Test ID**    | **ft_rpvst_stress_shut_noshut**                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST convergence by shut/no shut of physical interfaces for multiple times and checking for convergence.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Stress**                                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Have 2 back to baack links between each DUT pair and configure these ports part of lag.<br/>3. Create vlans 10,20,30 and make all lag ports participate in all vlans.<br/>4. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>5. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>6. Send traffic for all the vlan instances.<br/>7. Shut and noshut the physical interfaces on DUT1 for 5 iterations.<br/>8. Check RPVST convergence.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that lag config is successful and is UP on all DUTs.<br/>3. Verify vlan creation is successful and lag ports are participated in all vlans.<br/>4. Verify that spanning tree mode is set as RPVST.<br/>5. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>6. Verify that traffic is forwarded fine as per the instance topology.<br/>7. Verify that physical interfaces are up after all iterations.<br/>8. Verify that RPVST converges fine in all 3 instances.<br/>9. Verify that traffic is forwarded fine for all 3 instances.** |

#### 3.8.5 Verify the RPVST convergence by disabling and re-enabling spanning tree globally on the DUT for multiple times.

| **Test ID**    | **ft_rpvst_stress_stp_disable_enable**                       |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the RPVST convergence by disabling and re-enabling spanning tree globally on the DUT for multiple times.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Stress**                                                   |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Create vlans 10,20,30 and make all ports participate in all vlans.<br/>3. Set spanning tree mode as RPVST globally and also in all vlans configured.<br/>4. Configure DUT1 as root bridge in vlan 10 instance by changing the lowest priority and DUT2,DUT3 in vlan 20,30 instances respectively.<br/>5. Send traffic for all the vlan instances.<br/>6. Disable spanning tree globally on DUT1 then on DUT2 and on DUT3.<br/>7. Enable spanning tree globally on DUT1 then on DUT2 and on DUT3.<br/>8. repeat steps 6 and 7 for 5 iterations.<br/>9. Check RPVST convergence.<br/>10. Send traffic for all the vlan instances.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify vlan creation is successful and ports are participated in all vlans.<br/>3. Verify that spanning tree mode is set as RPVST.<br/>4. Verify that switches become root bridges in respective vlans i.e DUT1 for Vlan 10, DUT2 for Vlan20 and DUT3 for Vlan30.<br/>   All the links will be utilized for traffic forwarding.<br/>5. Verify that traffic is forwarded fine as per the instance topology.<br/>6. Verify that spanning tree is disabled on all 3 DUTs.<br/>7. Vreify that spanning tree is enabled on all 3 DUTs.<br/>8. Verify that spanning tree is disabled/enabled as per the config.<br/>9. Verify that RPVST converges fine in all 3 instances.<br/>10.Verify that traffic is forwarded fine for all 3 instances.** |

#### 3.8.6 Verify running max RPVST VLAN instances are not disrupted when max+1 vlan is created and ports are added to this vlan.

| **Test ID**    | **ft_rpvst_max_vlan_plusone**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify running max RPVST VLAN instances are not disrupted when max+1 vlan is created and ports are added to this vlan.** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Negative**                                                 |
| **Steps**      | **Procedure:<br/>1. Boot the DUTs with default configuration.<br/>2. Enable spanning tree on all the DUTs.<br/>3. Create max+1 VLANs on all the DUTs.<br/>4. Set spanning tree mode as RPVST globally for all the created VLANs and on interfaces.<br/>5. Verify that spanning tree is converged properly by sending and verifying the traffic to all the max vlans configured.<br/>6. Shutdown and no shutdown all the interfaces on any random DUT in the topology and verify that spanning tree is converged properly by sending and verifying the traffic to all the vlans configured.<br/><br/>Expected Behavior:<br/>1. DUTs are up with default configuration.<br/>2. Verify that spanning tree is enabled.<br/>3. Verify that VLANs are created.<br/>4. Verify that spanning tree is set as RPVST.<br/>5. Verify that traffic is forwarded without any loss for all the vlans.<br/>6. Verify that traffic is forwarded without any loss for all the vlans.** |

## 


# 4 Reference Links

 http://gerrit-lvn-07.lvn.broadcom.net:8083/c/sonic/documents/+/9629/9/L2/STP/SONiC+RPVST+Plus+FS.md#292