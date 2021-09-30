#  SQA Test Plan
# Master Key Encryption
#  SONiC 4.0 Project and Cyrus Release
# Table of Contents
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 9/25/2021 | Chandra Sekhar Reddy | Initial version |

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
| AES     | Advanced encryption standard  |
| PBKDF2     | Password-Based Key Derivation Function 2  |
| UMF     | Unified Management framework  |
| IPC     | Inter process communication.  |
| RPC     | Remote procedure call  |
| Encryption key| Derived key used for encryption/decryption services  |
| Master key     | Plaintext key provided by user.  |
| Default key     | Key derived from unique system MAC  |
| LDAP     | Lightweight Directory Access Protocol  |
| TACACS+     | Terminal Access Controller Access Control System       |
| RADIUS     | Remote Authentication Dial-In User Service |
| PAM    | Pluggable Authentication Modules |


# Feature Overview
Broadcom SONiC today encrypts protocol passwords via hardcoded encryption keys in source code. Protocols are free to choose the encryption key used and this presents a security vulnerability as the code is open source and anybody can get access to the keys used to encrypt protocol passwords. In addition, SONiC does not implement an infra/framework that centralizes the encryption/decryption services and hence protocols end up using different keys for encrypting their passwords..

Master key based encryption feature allows users to configure a custom encryption key that is used to encrypt protocol passwords on the system. This feature introduces an infrastructure/framework that centralizes the encryption/decryption services for consumers.
###  Requirements
#####  Functional Requirements
- Master key based encryption feature replaces hardcoded key based encryption mechanism to encrypt protocol passwords in SONiC.
- User should be able to configure the master key on the system via UI.
- User must not be able to retrieve the master key via any UI.
- User configured master key must not be used to encrypt protocol passwords directly. The recommendation is to run it through a key derivation function.
- Encrypted protocol passwords must be decryptable only using the same configured master key.
- The master key must persist across reboots.
- A default key must be used as the encryption key in the absence of a user configured master key.
- Default key must provide same level of security as a user configured master key.
- The default key must be unique per switch.
- On an update of the user configured master key, all the previously encrypted passwords must be re-encrypted with the new master key.
- User must be able to recover the system in case she/he forgets the configured master key. The root level user must be able to reset the configured master key and fall back to default master key.

#####  Configuration and Management Requirements
- Master key encryption feature is configurable via UMF only (unified management framework).
- KLISH CLI, REST and gNMI support for the configuration only.
- Config migration from/to an older release must be supported.
- Configuration portability - Uploading/sourcing configuration from a box and applying it to another box is supported only if the same master key is configured on both the source and destination switches.
# Design Overview

The master key encryption infrastructure will provide centralized encryption/decryption services on SONiC. The infrastructure is available on the host and applications/protocols avail services without having to know the encryption key. The master key is managed by the infra and access to it is restricted, hence providing a secure mechanism for crypto operations on SONiC.

The applications will communicate with the master key encryption infra over D-BUS. Note that the master key encryption infra is available to applications/protocols that are configured via UMF only.



# 1 Test Focus Areas
## 1.1 Functional Testing 
  - Generation of Master key with default key of system MAC

  - Generation of Master key with with user configured key

  - Encryption and decryption of all protocol password with Master Key

  - Re Encryption and decryption of all protocol password with new configured Master key

## 1.2 Reboot
  -	Master key will persist during the warm Boot
  -	Master key will persist during the Cold Boot
  -	Master key will persist during the Fast  Boot
## 1.3 Scale Testing 
  -	Applicable for all relevant scale testcases of  protocols like OSPF,BGP,RADIUS,TACACS+,LDAP etc.


# 2 Topologies
## 2.1 Topology 1

![LDAP Topology](LDAP_topology.png)


# 3 Test  Case and Objectives

## 3.1 Functional
### 3.1.1 Verify that system is able to derive a unique master key in the absence of user configured key
| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that system is able to derive a unique master key in the absence of user configured key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Verify that system uses system MAC as master key incase user not configured any key<br/>2)  Please check the master key in system files with root user<br/> |

### 3.1.2  Verify that the default master key is in-accessible to the user via UI

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the default master key is in-accessible to the user via UI** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Verify that system uses system MAC as master key incase user not configured any key<br/>2)  Please check the master key in system files with non root user not able to access<br/> |

### 3.1.3 Verify that system is able to encrypt the protocol passwords with default Master Key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that system is able to encrypt the protocol passwords with Master Key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1)Verify that system uses system MAC as master key incase user not configured any key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/> |

### 3.1.4 Verify that system is able to derive a unique master key with the user configured key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that system is able to derive a unique master key with the user configured key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2)  Please check the master key in system files with root user<br/> |

### 3.1.5  Verify that the  master key is in-accessible to the user via UI

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the master key is in-accessible to the user via UI** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2)  Please check the master key in system files with non root user not able to access<br/> |

### 3.1.6 Verify that system is able to encrypt the protocol passwords with user configured Master Key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that system is able to encrypt the protocol passwords with user configured Master Key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/> |

## 3.2 Config Reload/Cold Reboot Test Cases

#### 3.2.1 Config Reload

### 3.2.1.1 Verify the IPv4 SSH login from client after config reload with LDAP User with Authentication TACACS+, Name Service LDAP, sudo Authorization LDAP

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the IPv4 SSH login from client after config reload with LDAP User with Authentication TACACS+, Name Service LDAP, sudo Authorization LDAP** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**             | 1) After bringing the testbed as per the test case 3.1.17 <br/>2)  Save the config and reload<br/>3) Try to connect via ssh to device and provide the credentials whatever configured in LDAP server under sudoer group<br/>4) verify that user login and home directory created and check if any syslog messages for the login success
5) verify that user can go to root by sudo -i and check gid,uid and groups should be root |



#### 3.2.2 Cold Reboot

### 3.2.2.1 Verify the IPv4 SSH login from client after Cold Reboot with LDAP User with Authentication TACACS+, Name Service LDAP, sudo Authorization LDAP

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the IPv4 SSH login from client after Cold Reboot with LDAP User with Authentication TACACS+, Name Service LDAP, sudo Authorization LDAP** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) After bringing the testbed as per the test case 3.1.17 <br/>2) Save the config and cold reboot<br/>3) Try to connect via ssh to device and provide the credentials whatever configured in LDAP server under sudoer group<br/>4) verify that user login and home directory created and check if any syslog messages for the login success
5) verify that user can go to root by sudo -i and check gid,uid and groups should be root |





## 3.3 Scale Testing
### 3.3.1 Verify the LDAP Scale testing with 8 IPv4 servers configured in the Device with SSH login from client with LDAP User with Authentication TACACS+, Authorization LDAP, Name Service LDAP
| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify the LDAP Scale testing with 8 IPv4 servers configured in the Device with SSH login from client with LDAP User with Authentication TACACS+, Authorization LDAP, Name Service LDAP** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Configure the IPv4 address between SSH client and DUT<br/>2) Configure the Base DN in the device<br/>3) Configure the 8 LDAP servers with priority 1 to 8 in the device make sure 8 one is reachable <br/>4) Create LDAP user in the LDAP server with which SSH client will login<br/>5) Configure aaa nss passwd ldap.<br/>6) Configure aaa nss shadow ldap.<br/>7) Configure aaa nss group ldap.<br/>8) Configure tacacs+ server and passkey.<br/>9) Configure aaa authentication ldap with failthrough disabled.<br/>10) Configure ldap pam group dn and pam memeber attribute as memberUID.<br/>11) Configure aaa authorization login as ldap.<br/>12) Try to connect via ssh to device and provide the credentials whatever configured in LDAP server<br/>13) verify that user login after geeting the details from 8th server and home directory created and check if any syslog messages for the login success<br/>14) Try to connect user which is not configured in LDAP server, it will throw the proper syslog message and will not connect.<br/> |
### 
# 4 Reference Links

https://github.com/BRCM-SONIC/sonic_doc_private/blob/20384e58e8c528f72379818affe783a6c4d65afc/base/master_key_infra/Master%20key%20encryption%20feature%20HLD.md

