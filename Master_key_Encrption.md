#  SQA Test Plan
# Master Key Encryption
#  SONiC 4.0 Project and Cyrus Release
# Table of Contents
[TOC]
# Test Plan Revision History
| Rev | Date | Author | Change Description |
|:---:|:-----------:|:------------------:|-----------------------------|
| 0.1 | 9/25/2021 | Chandra Sekhar Reddy | Initial version |
| 0.2 | 10/12/2021 | Chandra Sekhar Reddy | updated the review comments |

# List of Reviewers
|  Function | Name |
|:---:|:-----------:|
| 1 |  Shirisha Dasari |

# List of Approvers
|  Function | Name | Date Approved|
|:---:|:-----------:|:------------------:|
| 1 | Sachin Suman  |  |

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
  - Fallback to default key from a user configured key and update of an existing master key to be covered as well.
  - All protocols need to be verified here i.e protocol functionality needs to be verified. The protocol must work seamlessly while using the default master key and also when a user provided master key is updated.

## 1.2 Reboot
  -	Master key will persist during the warm Boot
  -	Master key will persist during the Cold Boot
  -	Master key will persist during the Fast  Boot
## 1.3 Scale Testing 
  -	Applicable for all relevant scale testcases of  protocols like OSPF,BGP,RADIUS,TACACS+,LDAP etc.


# 2 Topologies
## 2.1 Topology 1

![LDAP Topology](Master_key_encryption_topo.png)


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
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2)  Please check the master key should not be displayed via show running config<br/> |

### 3.1.6 Verify that system is able to encrypt the protocol passwords with user configured Master Key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that system is able to encrypt the protocol passwords with user configured Master Key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/> |

### 3.1.7 Verify that the system re-encrypts all existing protocol passwords with the new master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the system re-encrypts all existing protocol passwords with the new master key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Configure new master key<br/>5) Verify from show running config that password is re-encrypted with different encrypted string compared to previous one<br/> |

### 3.1.8 Verify that the user is prompted to provide the correct old passphrase while trying to update the new master key passphrase

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the user is prompted to provide the correct old passphrase while trying to update the new master key passphrase** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key with passpharse<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Configure new master key and prompt for passphrase<br/>5) verify that master key will not configured with wrong passphrase<br/>6) verify that master key will be configured with Correct passphrase<br/>7) Verify from show running config that password is re-encrypted<br/> |

### 3.1.9 Verify that the user is not allowed to retry a master key update in the case of a failure of authentication w.r.t older master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the user is not allowed to retry a master key update in the case of a failure of authentication w.r.t older master key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key <br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Configure new master key <br/>5) verify that master key will not configured with wrong old key<br/>6) verify that master key will not be configured with Correct old key before 1 Sec retry<br/>7) verify that master key will  be configured with Correct old key After 1 Sec retry<br/>7) Verify from show running config that password is re-encrypted and check that different from previous one<br/> |

### 3.1.10 Verify that the deletion of the master key results in the system falling back to the default key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the deletion of the master key results in the system falling back to the default key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key <br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Delete the master key <br/>5)  Verify that system fallback to default system key<br/>6)  Verify that syslog generated that fallback to default system key<br/> |

### 3.1.11 Verify that the configuration from one switch can be migrated to another provided that the master keys are the same

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the configuration from one switch can be migrated to another provided that the master keys are the same** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user Default key <br/>2) Configure any protocol password ( ospf) between two switches and verify that ospf neighbors are up <br/>3) Verify from show running config that password is encrypted<br/>4) Copy the config_db.json file from switch 1 and load the same file again<br/>5)  Verify that the config loaded properly<br/>6)  Verify the same with user configured key and check ospf should be intact<br/> |

### 3.1.12 Verify that the configuration from one switch cannot be migrated to another provided that the master keys are Different

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the configuration from one switch cannot be migrated to another provided that the master keys are Different** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default key <br/>2) Configure any protocol password ( ospf) between two switches and verify that ospf neighbors are up <br/>3) Verify from show running config that password is encrypted<br/>4) Copy the config_db.json file from switch 1 and try to change the master key<br/>5)  Try to load the same config on switch 1<br/>6)  Verify that the ospf not comes up as mater key is diff and protocl password encryption/decryption to diff value <br/> |

### 3.1.13 Verify that ospf protocol functionality  with the Default Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ospf protocol functionality  with the Default Configured Master key** |
| **Test Setup** | **Topology as per the ospf functional suite**                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure ospf as per the ospf functional suite  <br/>3) Verify from show running config that password is encrypted<br/>4) Verify ospf neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/> |

### 3.1.14 Verify that ospf protocol functionality  with the user Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ospf protocol functionality  with the user Configured Master key** |
| **Test Setup** | **Topology as per the ospf functional suite**                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure ospf as per the ospf functional suite  <br/>3) Verify from show running config that password is encrypted<br/>4) Verify ospf neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/>7) Configured the new key and verify that passwords are re-encrypted<br/>8) Verify ospf neighbors are up<br/>9) Verify that route count as expected<br/>10) Verify that traffic as expected<br/> |

### 3.1.15 Verify that ospf protocol functionality after system boot with the User Configured Master Key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ospf protocol functionality after system boot with the User Configured Master Key** |
| **Test Setup** | **Topology as per the ospf functional suite**                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure ospf as per the ospf functional suite  <br/>3) Verify from show running config that password is encrypted<br/>4) Verify ospf neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/>7) Configured the new key and verify that passwords are re-encrypted<br/>8) Verify ospf neighbors are up<br/>9) Verify that route count as expected<br/>10) Verify that traffic as expected<br/>11) Reboot the system and verify that master key intact<br/>12) Verify ospf neighbors are up<br/>13) Verify that route count as expected<br/>14) Verify that traffic as expected<br/>15) Verify that there should not be any encryption/decryption errors in syslog<br/> |

### 3.1.16 Verify that BGP protocol functionality  with the Default Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BGP protocol functionality  with the Default Configured Master key** |
| **Test Setup** | **Topology as per the BGP functional suite**                 |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure BGP as per the BGP functional suite  <br/>3) Verify from show running config that password is encrypted<br/>4) Verify BGP neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/> |

### 3.1.17 Verify that BGP  protocol functionality  with the user Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BGP protocol functionality  with the user Configured Master key** |
| **Test Setup** | **Topology as per the BGP functional suite**                 |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure BGP as per the BGP functional suite  <br/>3) Verify from show running config that password is encrypted<br/>4) Verify BGP neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/>7) Configured the new key and verify that passwords are re-encrypted<br/>8) Verify BGP neighbors are up<br/>9) Verify that route count as expected<br/>10) Verify that traffic as expected<br/> |

### 3.1.18 Verify that BGP  protocol functionality after system boot with the User Configured Master Key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BGP protocol functionality after system boot with the User Configured Master Key** |
| **Test Setup** | **Topology as per the BGP functional suite**                 |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure BGP as per the ospf functional suite  <br/>3) Verify from show running config that password is encrypted<br/>4) Verify BGP neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/>7) Configured the new key and verify that passwords are re-encrypted<br/>8) Verify BGP neighbors are up<br/>9) Verify that route count as expected<br/>10) Verify that traffic as expected<br/>11) Reboot the system and verify that master key intact<br/>12) Verify BGP neighbors are up<br/>13) Verify that route count as expected<br/>14) Verify that traffic as expected<br/> |

### 3.1.19 Verify that RADIUS protocol functionality  with the Default Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that RADIUS protocol functionality  with the Default Configured Master key** |
| **Test Setup** | **Topology as per the RADIUS functional suite**              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure the RADIUS server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the RADIUS as authentication<br/>5) Try to ssh to device with user which is configured in RADIUS<br/>6) Verify user is authenticated and allowed to login into device<br/> |

### 3.1.20 Verify that RADIUS protocol functionality  with the User Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that RADIUS protocol functionality  with the User Configured Master key** |
| **Test Setup** | **Topology as per the RADIUS functional suite**              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the User Configured key <br/>2) Configure the RADIUS server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the RADIUS as authentication<br/>5) Try to ssh to device with user which is configured in RADIUS<br/>6) Verify user is authenticated and allowed to login into device<br/> |

### 3.1.21 Verify that RADIUS protocol functionality  after system boot with the User Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that RADIUS protocol functionality  after system boot with the User Configured Master key** |
| **Test Setup** | **Topology as per the RADIUS functional suite**              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the User Configured key <br/>2) Configure the RADIUS server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the RADIUS as authentication<br/>5) Try to ssh to device with user which is configured in RADIUS<br/>6) Verify user is authenticated and allowed to login into device<br/>7) Reboot the device and verify that master key is intact<br/>8) Verify that user allowed to re-login via ssh<br/> |

### 3.1.22 Verify that TACACS+ protocol functionality  with the Default Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that TACACS+ protocol functionality  with the Default Configured Master key** |
| **Test Setup** | **Topology as per the TACACS+ functional suite**             |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure the TACACS+ server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the TACACS+ as authentication<br/>5) Try to ssh to device with user which is configured in TACACS+ <br/>6) Verify user is authenticated and allowed to login into device<br/> |

### 3.1.23 Verify that TACACS+  protocol functionality  with the User Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that TACACS+ protocol functionality  with the User Configured Master key** |
| **Test Setup** | **Topology as per the TACACS+ functional suite**             |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the User Configured key <br/>2) Configure the TACACS+ server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the TACACS+ as authentication<br/>5) Try to ssh to device with user which is configured in TACACS+ <br/>6) Verify user is authenticated and allowed to login into device<br/> |

### 3.1.25 Verify that LDAP protocol functionality  with the Default Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that LDAP protocol functionality  with the Default Configured Master key** |
| **Test Setup** | **Topology as per the LDAP functional suite**                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure the LDAP server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the LDAP as authentication<br/>5) Try to ssh to device with user which is configured in LDAP <br/>6) Verify user is authenticated and allowed to login into device<br/> |

### 3.1.26 Verify that LDAP  protocol functionality  with the User Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that LDAP protocol functionality  with the User Configured Master key** |
| **Test Setup** | **Topology as per the LDAP functional suite**                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the User Configured key <br/>2) Configure the LDAP server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the LDAP as authentication<br/>5) Try to ssh to device with user which is configured in LDAP<br/>6) Verify user is authenticated and allowed to login into device<br/> |

### 3.1.27 Verify that LDAP protocol functionality  after system boot with the User Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that LDAP protocol functionality  after system boot with the User Configured Master key** |
| **Test Setup** | **Topology as per the LDAP functional suite**                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the User Configured key <br/>2) Configure the LDAP server credentials on device  <br/>3) Verify from show running config that password is encrypted<br/>4) Configure the LDAP as authentication<br/>5) Try to ssh to device with user which is configured in LDAP <br/>6) Verify user is authenticated and allowed to login into device<br/>7) Reboot the device and verify that master key is intact<br/>8) Verify that user allowed to re-login via ssh<br/> |

### 3.1.28 Verify that SNMPv3  protocol functionality  with the User Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that SNMPv3  protocol functionality  with the User Configured Master key** |
| **Test Setup** | **Topology as per the SNMPv3 functional suite**              |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the User Configured key <br/>2) Configure the SNMPv3 on the device <br/>3) Verify from show running config that password is encrypted<br/>4) Configure some of the object using CLI<br/>5) Try to GET the data using netsnmp tool<br/>6) Verify the data is properly fetched |

### 3.1.29 verify that system throws syslog errors when delete the key file

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify that system throws syslog errors when delete the key file** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key <br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Delete the key file at /etc/<>xxx.txt <br/>5)  Verify that system throws proper syslog message that kei file is not there to decrypt/encrypt passwords<br/> |

### 3.1.30 verify that system recovers, fallback to default key after deleting the configured master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **verify that system recovers, fallback to default key after deleting the configured master key** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key <br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Delete the key file at /etc/<>xxx.txt <br/>5)  Verify that system throws proper syslog message that key file is not there to decrypt/encrypt passwords<br/>6)  Try to delete the configured master key <br/>7)  Verify that syslog generated that fallback to default system key and all passwords re-encrypted with default key<br/> |

## 3.2 Config Reload/Cold boot/Warm boot/Fast boot tests

#### 3.2.1 Verify that the new master key is persistent across Config Reload

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the new master key is persistent across Config Reload** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Do Config reload and verify the master key intact<br/> |

#### 3.2.2 Verify that the new master key is persistent across Cold boot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the new master key is persistent across Cold boot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Do Cold boot and verify the master key intact<br/> |

#### 3.2.3 Verify that the new master key is persistent across Warm boot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the new master key is persistent across Warm boot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Do Warm boot and verify the master key intact<br/> |

#### 3.2.4 Verify that the new master key is persistent across Fast boot

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that the new master key is persistent across Fast boot** |
| **Test Setup** | **Topology1**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the user configured key<br/>2) Configure any protocol password ( ospf/BGP/LDAP etc) <br/>3) Verify from show running config that password is encrypted<br/>4) Do Fast boot and verify the master key intact<br/> |

## 3.3 Scale Testing

### 3.3.1 Verify that ospf Scale functionality  with the user Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that ospf Scale functionality  with the user Configured Master key** |
| **Test Setup** | **Topology as per the L3 Scale and performance suite**       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure ospf as per the L3 Scale and performance suite functional suite  <br/>3) Scale the ospf routes as per the platform<br/>4) Verify ospf neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/>7) Configured the new key and verify that passwords are re-encrypted<br/>8) Verify ospf neighbors are up<br/>9) Verify that route count as expected<br/>10) Verify that traffic as expected<br/> |

### 3.3.2 Verify that BGP Scale functionality  with the user Configured Master key

| **Test ID**    | **iTAS Test Case Manager ID**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that BGP Scale functionality  with the user Configured Master key** |
| **Test Setup** | **Topology as per the L3 Scale and performance suite**       |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Verify that system generates master key with the Default system MAC key <br/>2) Configure BGP as per the L3 Scale and performance suite functional suite  <br/>3) Scale the BGP routes as per the platform<br/>4) Verify BGP neighbors are up<br/>5) Verify that route count as expected<br/>6) Verify that traffic as expected<br/>7) Configured the new key and verify that passwords are re-encrypted<br/>8) Verify BGP neighbors are up<br/>9) Verify that route count as expected<br/>10) Verify that traffic as expected<br/> |

# 4 Reference Links

https://github.com/BRCM-SONIC/sonic_doc_private/pull/256

