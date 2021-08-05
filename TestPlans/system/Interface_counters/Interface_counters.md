SQA Test Plan

###### #SONiC_Interface_counters_Verify

#SONiC 3.4.0 Release

## Test Plan Revision History

| Rev  | Date       | Author      | Change Description |
| ---- | ---------- | ----------- | ------------------ |
| 1    | 07/13/2020 | Pavan Kumar | Initial Version    |
|      |            |             |                    |
|      |            |             |                    |
|      |            |             |                    |
|      |            |             |                    |

## List of Reviewers

| Function |      Name       |
| :------: | :-------------: |
|   Dev    |     Roopesh     |
|    QA    | Chandra Bhushan |
|    QA    |    Chandra V    |

## List of Approvers

| Function |      Name       | Date Approved |
| :------: | :-------------: | :-----------: |
|   Dev    |     Roopesh     |               |
|    QA    | Chandra Bhushan |               |
|    QA    |    Chandra V    |               |

# Definition/Abbreviation

| **Term** | **Meaning**            |
| -------- | ---------------------- |
| CLI      | Command Line Interface |
| DUT      | Device under test      |
|          |                        |
|          |                        |

## Introduction

### Objective 

The main objective of this document is to cover the test cases that will be executed to verify the interface counters fields.  Topologies and test cases  for testing the feature will be discussed as part of this document.

### Scope

Only covers interface counters fields.

## Feature Overview

The "Show interface counters" command gives all the detailed information about the interface packet counters. 

Eg: Unicast, Broadcast, Multicast, Total Packets, Errored, Dropped etc.

## 1 Test Focus Areas

### 1.1 Functional Requirements

System Readiness:

- Bring up the topology mentioned below.
- Send various traffic streams like unicast, multicast, broadcast etc.
- Verify whether the interface counters details were getting updated as expected.
- Also compare the details across all the counters clis 

## 2 Topologies

D1T1:2,D1D2:4,D2T2:2

Int_counters_topo

## 3 Test Case and objectives

### **3.1 Functional**

### 3.1.1 Verify base line check for the interface counters.

| **Test ID**    | **Interface_counters_Fun001**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_Int_counters_base**                                 |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure and start the traffic stream (Unicast,Multicast, Broadcast, Errored) <br/>3) Check show interface counters output <br/>3) Check show interface <interface_name> output <br/>3) Check show interface counters <interface_name> output <br/> |

### 3.1.2 Verify that unicast traffic is sent successfully across the dut.

| **Test ID**    | **Interface_counters_Fun002**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_unicast**                              |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure and start the unicast traffic stream <br/>3) Verify the "unicast" field in both input and output data of the show interface cli<br/>4) Verify the "unicast packets received" and "unicast packets transmitted fields in the show interface counters cli<br/>5) Verify the "rx_ok" and "tx_ok" field in the show interface counters  cli<br/>6)Verify if all the respective fields is updated with the same values<br/> |

### 3.1.3 Verify that multicast traffic is sent successfully across the dut.

| **Test ID**    | **Interface_counters_Fun003**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_multicast**                            |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure and start the multicast traffic stream <br/>3) Verify the "multicast" field in both input and output data of the the show interface cli<br/>4) Verify the "multicast packets received"  and "Multicast Packets Transmitted" field in the show interface counters cli<br/>5) Verify the " rx_ok"  and "tx_ok" field in the show interface counters  cli<br/>6)Verify if all the respective is updated with the same values<br/> |

### 3.1.4 Verify that broadcast traffic is sent successfully across the dut.

| **Test ID**    | **Interface_counters_Fun004**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_broadcast**                            |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure and start the broadcast traffic stream <br/>3) Verify the "broadcast" field in both input and output data of the show interface cli<br/>4) Verify the "broadcast packets received" and "Broadcast Packets Transmitted" field in the show interface counters cli<br/>5) Verify the "rx_ok" and "tx_ok" field in the show interface counters  cli<br/>6)Verify if all the respective fields is updated with the same values<br/> |

### 3.1.5 Verify that errored traffic get captured across the dut.

| **Test ID**    | **Interface_counters_Fun005**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_errored**                              |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure and start the errored traffic stream <br/>3) Verify the "error" field in both input and output data of the show interface cli<br/>4) Verify the "rx_err" and "tx_err" field in the show interface counters  cli<br/>5)Verify if all the respective fields is updated with the same values<br/> |

### 3.1.6 Verify that dummy traffic is sent successfully and dropped across the dut.

| **Test ID**    | **Interface_counters_Fun006**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_dropped**                              |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure and start the dummy traffic stream <br/>3) Verify the "dropped" field in both the input and output data of the show interface cli<br/>4) Verify the "rx_drp" field in the show interface counters  cli<br/>5)Verify if all the respective fields is updated with the same values<br/> |

### 3.1.7 Verify that dummy traffic is sent successfully and get captured across the dut.

| **Test ID**    | **Interface_counters_Fun007**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_total_packets**                        |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure the random traffic stream <br/>3) Verify the "packets" field in the show interface cli<br/>4) Verify the "rx_ok" field in the show interface counters  cli<br/>5) Verify the "Total Packets Received Without Errors" packets received field in the show interface counters cli<br/>6)Verify if all the respective fields is updated with the same values<br/> |

### 3.1.8 Verify that dummy traffic is sent successfully and dropped across the dut.

| **Test ID**    | **Interface_counters_Fun008**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_total_packets**                        |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure the random traffic stream <br/>3) Verify the "packets" field in the show interface cli<br/>4) Verify the "rx_err" field in the show interface counters  cli<br/>5)Verify if all the respective fields is updated with the same values<br/>6) Verify the "Total Packets Received Without Errors" packets received field in the show interface counters cli<br/> |

### 3.1.9 Verify that over sized traffic is sent successfully and get captured across the dut.

| **Test ID**    | **Interface_counters_Fun009**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_oversized_packets**                    |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure the random over sized packet traffic stream <br/>3) Verify the "over size" field in the show interface cli<br/>4) Verify the "rx_oversize" field in the show interface counters  cli<br/>5)Verify if all the respective fields is updated with the same values |

### 3.1.10 Verify that under sized traffic is sent successfully and get captured across the dut.

| **Test ID**    | **Interface_counters_Fun010**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_undersized_packets**                   |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure the random over sized packet traffic stream <br/>3) Verify the "Undersize Received" field in the show interface counters cli<br/> |

### 3.1.11 Verify that traffic is sent successfully and all packets get captured across the dut.

| **Test ID**    | **Interface_counters_Fun011**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_total_packets_size**                   |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure the random traffic stream <br/>3) Verify the "octets" field in the show interface cli<br/>4) Verify the "Packets Received 64 Octets" field in the show interface counters  cli<br/>5)Verify the "Total Packets Received Without Errors" packets received field in the show interface counters cli<br/>6) Verify if all the respective fields is updated with the same values<br/> |

### 3.1.12 Verify that traffic is sent successfully and all packets get captured across the dut.

| **Test ID**    | **Interface_counters_Fun012**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_total_packets_size_128_255**           |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure the random traffic stream <br/>3) Verify the "(128 to 255 Octects)" field in the show interface cli<br/>4) Verify the "Packets Received 64 Octets" field in the show interface counters  cli<br/>5) Verify the "Packets Transmitted 128-255 Octets" packets received field in the show interface counters cli<br/>6)Verify if all the respective fields is updated with the same values<br/> |

### 3.1.13 Verify that clear counters is updated across all the counter clis of the dut.

| **Test ID**    | **Interface_counters_Fun013**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_clear_all**                            |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel) <br/>2) Clear counters using "Clear counters interface "interface name" <br/>3) Verify the clearing of counters date and time interval is same across all the counter clis <br/> |



### **3.2 Negative Test Cases**

### 3.3.1 Verify any unicast traffic is sent successfully and apart from unicast rest of the filed counters remain same.

| **Test ID**    | **Interface_counters_Fun_014**                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify_int_counters_negative**                             |
| **Test Setup** | **Int_counters_topo**                                        |
| **Type**       | **Negative**                                                 |
| **Steps**      | 1)Configure interface (Physical,vlan,portchannel,sub interface) <br/>2) Configure the unicast traffic stream 
3) Verify only  "unicast" field in the show interface cli is incremented.<br/> |

