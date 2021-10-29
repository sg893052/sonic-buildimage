# Feature Name

Master key based encryption.
# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev  |    Date    |     Author      | Change Description |
| :--: | :--------: | :-------------: | ------------------ |
| 0.1  | 08/09/2021 | Shirisha Dasari | Initial version    |

# About this Manual
This document provides general information about the Master key based encryption feature implementation in SONiC.
# Scope
This document describes the high level design of Master key based encryption feature. 

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**       | **Meaning**                                          |
| -------------- | ---------------------------------------------------- |
| AES            | Advanced encryption standard.                        |
| PBKDF2         | Password-Based Key Derivation Function 2.            |
| UMF            | Unified Management framework.                        |
| IPC            | Inter process communication.                         |
| RPC            | Remote procedure call.                               |
| Encryption key | Derived key used for encryption/decryption services. |
| Master key     | Plaintext key provided by user.                      |
| MKI            | Master key infrastructure                            |
| Default key    | Key derived from unique system MAC.                  |

# 1 Feature Overview

Broadcom SONiC today encrypts protocol passwords via hardcoded encryption keys in source code. Protocols are free to choose the encryption key used and this presents a security vulnerability as the code is open source and anybody can get access to the keys used to encrypt protocol passwords. In addition, SONiC does not implement an infra/framework that centralizes the encryption/decryption services and hence protocols end up using different keys for encrypting their passwords.

Master key based encryption feature allows users to configure a custom encryption key that is used to encrypt protocol passwords on the system. This  feature introduces an infrastructure/framework that centralizes the encryption/decryption services for consumers. 


## 1.1 Requirements

### 1.1.1 Functional Requirements

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

### 1.1.2 Configuration and Management Requirements
- Master key encryption feature is configurable via UMF only (unified management framework).
- KLISH CLI, REST and gNMI support for the configuration only.
- Config migration from/to an older release must be supported.
- Configuration portability - Uploading/sourcing configuration from a box and applying it to another box is supported only if the same master key is configured on both the source and destination switches.  

### 1.1.3 Scalability Requirements
- Mass deployment scenarios with respect to master key configuration must be supported.  

### 1.1.4 Warm Boot Requirements

- Master key encryption infra must work across a warm-reboot.

## 1.2 Design Overview

The master key encryption infrastructure will provide centralized encryption/decryption services on SONiC. The infrastructure is available on the host and applications/protocols avail services without having to know the encryption key. The master key is managed by the infra and access to it is restricted, hence providing a secure mechanism for crypto operations on SONiC.

The applications will communicate with the master key encryption infra over D-BUS. Note that the master key encryption infra is available to applications/protocols that are configured via UMF only.   

### 1.2.1 Basic Approach
### 1.2.2 Container

Master key based encryption functionality is available on the SONiC host. Applications running in the dockers are consumers of this feature functionality. No new container is added for this feature functionality.

### 1.2.3 SAI Overview

No SAI changes are required for this feature.

# 2 Functionality
## 2.1 Target Deployment Use Cases

Hardcoded encryption keys in source code are a security vulnerability. Once the key is possessed by an unauthorized user, all passwords encrypted by the key are vulnerable. The master key encryption feature alleviates this problem by providing users a mechanism to choose a preferred master key that is used to derive the final encryption key for encryption/decryption. 

Encryption/decryption services are centralized and available to applications via a defined interface.

## 2.2 Functional Description



![Design](Design.jpg)

The master key encryption framework running on the host provides an interface to applications wanting to avail AES based (as of today) encryption/decryption services via D-BUS. D-BUS is an IPC mechanism in Linux which enables host-container and container-host communication seamlessly. The D-BUS model requires the master key encryption framework to act as a server while the applications/containers become the clients availing the server's services.

The master key encryption framework on SONiC comprises of multiple components that are detailed below. 

### 2.2.1 D-BUS communication interface

D-BUS is a Linux IPC and RPC mechanism that provides secure intra system communication over Unix domain sockets. It is a message bus system, a simple way for applications to talk to one another. 

SONiC already supports a message bus (**"/org/SONiC/HostService"**) to facilitate communication between application dockers and **sonic-host-services** service on the host. **sonic-host-services** supports a set of host modules and actions that applications can request to execute on the host. Applications send requests and receive results/outputs of the requested actions over the D-BUS. The **mgmt-framework** docker uses the D-BUS functionality today to avail services related to image management, tech support generation etc. 

A new host module is added to **sonic-host-services** for key management. This new host module will implement the handlers to encrypt plaintext, decrypt ciphertext and update master key on the system(Section 2.2.5).  The application dockers can then avail these services over D-BUS once they initialize and setup the communication over D-BUS. To make this process easy and transparent, interface libraries (to be integrated into the docker) will be provided in multiple programming languages (Python - libkeymgrapi-py, C/C++ - libkeymgrapi) for the clients. A GO based interface already exists in the **mgmt-framework** docker.



```
C/C++ API interface :

typedef struct {

   int rc;

  char *body_p;

} HostResult_t; 

 \* Connects to the "/org/SONiC/HostService/" dbus object and returns result

 \* as sent by sonic-host-services through dbus.

 \* @return HostResult_t

 \*   @input

 \*   endpoint_p : Name of specific host module.

 \*   action_p  : Specific action of the host module

 \*   data_p   : data to act on

HostResult_t HostQuery(char *endpoint_p, char *action_p, char *data_p);


Example API invocation to encrypt password "plaintext":

HostResult_t *result_p = HostQuery("keyhandler.key_ctl", "pwEncrypt", "plaintext");
```



The C/C++ library can be easily linked by applications using the following snippet of code:

```
rules/xxx.mk

   $(xxx)_DEPENDS += $(LIBKEYMGRAPI_DEV)

   $(xxx)_RDEPENDS += $(LIBKEYMGRAPI)

 src/sonic-xxx/Makefile.am

   xxx_LDADD += -llibkeymgrapi
```

### 2.2.2 Key derivation function

The CLI command to configure a master key on SONiC will allow the user to choose a plaintext key. The plaintext hence configured is neither used for encryption nor stored on the system as is. The actual encryption key used for encryption services is derived from the user configured plaintext key using a key derivation function to keep things secure.   

PBKDF2 is a key derivation function that is recommended by NIST and has FIPS 140-2 validated implementations like HMAC. The master key encryption infra uses PBKDF2 with a strong iteration count to derive the final encryption key from the user configured key. The derived key is saved and used for encryption and decryption services on the system. 

### 2.2.3 Master key management

The encryption key is derived from a user configured master key(via the UMF interface, other UI like CLICK are not supported) and is used for crypto operations(Refer sec 2.2.2). The encryption key is stored in a file on the on the flash and is only root-readable and writable. The key file always stores the derived encryption key, the user provided master key is not stored anywhere on the system. 

The encryption key file is managed(read and written to) by the master key infra only. Individual applications do not have access to the encryption key directly and can only avail encryption/decryption services from the infra. The key file contains the following info:

1) Derived or default encryption key.

In the absence of a user configured master key, the infra uses a per device unique key and derives an encryption key from it as described in Section 2.2.2. The default key is also managed solely by the master key infrastructure, as is moving to a non-default key and falling back to a default key  when the user resets the key to the factory default (default).

An update of the encryption key is to be expected when the following events occur:

- User configures the master key first-time. Key is updated from default to user-configured.

- User updates the master key. Validation of the originally configured master key on the system is mandatory to update it. Hence, the user is required to provide the original master key for validation. The original key provided by the user is run through PBKDF2 to derive the final encryption key. This encryption key is validated against the stored encryption key. Post validation, the system proceeds to accept the new master key and update the encryption key derived from the new key.

- Reset of the user key to factory default and hence a fallback to the default key.

Any operation related to the master key (config, update) is relayed to the infra by the UMF via D-BUS (section 2.2.1). 

### 2.2.4 Default key

In the absence of a configured master key, the system uses a per system unique default key. This default key is used until the user configures a master key  via UI.

The default key is as secure as a user configured master key as the default key is also derived in a process similar to the user configured one(Section 2.2.2). In addition, since the default key is unique per system, it truly offers a secure mechanism for encrypting protocol passwords. The default encryption  key is derived from the system MAC address on an image install and populated into the key file to make it persistent in the absence of a user configured master key. The default key is used by the system until the user configures a master key. Once user configures the key, the system updates the key file to the encryption key derived from user provided key post re-encrypting any protocol passwords already configured on the system. 

If the user decides to reset the key to factory default, the system falls back to the default key and follows the same re-encryption process as described earlier. The key file is updated accordingly.

### 2.2.5 APIs supported

The master key infrastructure supports key management and encryption/decryption APIs over D-BUS. The key management APIs will be restricted to UMF only (i.e they can be invoked by UMF only) while encryption/decryption services are open to all clients.

##### 2.2.5.1 **startUpdateTx**

The configuration of a new master key involves multiple steps at UMF and MKI. UMF uses this API to indicate the start of a master key update transaction to MKI (new master key config/update of an existing master key config/fall back to default master key).  The API expects UMF to provide the old key(configured by the user) and the new key in the case of an update.   

The old key(if it exists) is validated prior to deriving the new encryption key.   The master key infra follows the below mentioned flow:

a) Master key infra derives the encryption key from old master key via PBKDF2 and compares it with the stored encryption key. If they do not match, a failure is returned to the caller. In case old master key validation fails, UMF additionally throttles the user to slow down brute-force attacks.

b) If keys match, the operation is permitted and the infra moves on-to derive the new encryption key.

c) At this step, the old master key is replaced with the new derived encryption key in the key file. The old key is saved as well until the master key update transaction is completed. The update is marked as in-progress and success returned to the caller.

**d)** **All encryption/decryption requests received henceforth are rejected until the master key update transaction is marked as completed.**

##### 2.2.5.2 **commitUpdateTx**

The commitUpdateTx API is used to complete the master key update transaction. Once the transaction is indicated as completed by UMF, MKI updates the context, discards the old key and finalizes the new master encryption key. All encryption/decryption requests are henceforth accepted and serviced via the new master encryption key.

##### 2.2.5.3 **revertUpdateTx**

The revertUpdateTx D-BUS API is used by UMF to indicate to the MKI to abort the master key update transaction. This is typically used if the password re-encryption of protocol passwords failed and the master key update operation must be rolled back.

MKI reverts to the old encryption key and discards the new encryption key and returns success to the caller.

#####  2.2.5.4 **pwEncrypt**

**pwEncrypt** is used to encrypt a plaintext using AES 128 CBC. The API uses the encryption key (saved in key file) to encrypt the incoming plaintext data and return the encrypted string.

Note that this API will not validate the plaintext semantics. It is up to the application to validate the semantics of the data being encrypted. The primary user of this API would be UMF (to encrypt protocol passwords configured by user). 

#####  2.2.5.5 **pwDecrypt**

**pwDecrypt** is used to decrypt ciphertext and is primarily used by the protocol dockers. 

#####  2.2.5.6 **pwReencrypt**

**pwReencrypt** is used to decrypt ciphertext using the old master key and re-encrypt it using the newly configured master key. UMF uses this D-BUS API during the master key update operation.

### 2.2.6 Password re-encryption - Registration framework

The UMF framework today accepts user configured protocol  passwords from CLI, encrypts them and saves them into CONFIG_DB. The protocol dockers decrypt the ciphertext from CONFIG_DB and use it as appropriate. On an update of the master key, these protocol passwords saved in CONFIG_DB must be decrypted using the old encryption key and re-encrypted using the new encryption key. This re-encryption of protocol passwords is implemented in the UMF with the aid of a registration mechanism. 

Individual protocol components are required to register with the MKI infra in UMF to get their keys re-encrypted automatically on a master key update. The registration happens within mgmt-framework. Individual component xfmr functions can call the registration API in the init() routine. It takes the following parameters:

a) Table in **CONFIG_DB** which stores the protocol passwords.
b) Field of table in **CONFIG_DB** to access configured protocol passwords.

The API to be called to register with the infra is:

**func MkiRegister(keyTbl string, keyField string) error **

**keyTbl** contains the table whose keys are used to access the protocol password in CONFIG_DB. **keyField** is the field to be read within the table per key in CONFIG_DB to access the protocol password.

For example, to register RADIUS tables with MKI, the following code could be used:

```
func init() {
        var err error
        // Register RADIUS tables with MKI for
        // key re-encryption on master key update.
        tblName1 := "RADIUS"
        fieldName := "passphrase"
        err = MkiRegister(tblName1, fieldName)
        if err != nil {
                log.Infof("Failed to register table RADIUS")
        }

        tblName2 := "RADIUS_SERVER"
        err = MkiRegister(tblName2, fieldName)

        if err != nil {
                log.Infof("Failed to register RADIUS_SERVER")
        }
}
```

**func MkiUnregister(keyTbl string) error**  can be used to un-register with the MKI.

A master key update consists of 2 steps:

- Accept new key from user once the old key is authorized and derive the new encryption key.
- Re-encrypt registered passwords with the new key and write them back into respective tables in CONFIG_DB. 
  - This will trigger a change notification to the respective protocol code that listens to these CONFIG_DB tables. The handlers can process the notification as required. For example, protocol code could decide to determine if the change is master key update related(notification provided by infra) or password change and choose the behavior. This however would need additional handling in the protocol code. Please do note that an update of a master key is a major event that is expected to happen sparingly. Hence, the benefits of any such additional handling must be weighed against complexity. 

Note that any decrypt/encrypt operations requested for while the master key is being updated, will not be serviced and failure returned with an appropriate error. The components must handle this failure gracefully by er-trying the operation.

### 2.2.7 Config Migration

#### 2.2.7.1 Copy paste of protocol password configuration

When working with the default key, a switch's complete configuration or protocol password configuration is not portable across devices as the key used to encrypt the protocol passwords is unique per switch. Attempting to copy configuration from one device to another can lead to un-expected behavior and must be strictly avoided. In some cases, the protocol password may be decoded to a garbage passphrase leading to a silent protocol error that is difficult to detect and debug. To be able to copy paste protocol passwords configuration from one device to another, the same master key must be configured on both the systems.  

If user copied configuration from one switch to another inadvertently and sees protocols not working as expected, the master key on both the switches could be different. In this case, it is advised to clear the configuration, configure the same master key on both the devices and attempt re-configuration on the target device again.  

To be able to copy configuration securely and successfully across switches in the network, the administrator must configure the same master key on all the switches to which configuration must be migrated/copied. 

#### 2.2.7.2 Config Migration with upgrade/downgrade

Config migration will be supported with upgrade and downgrade scenarios.

### 2.2.8 Component interactions



![Sample config flow](PasswordConfigFlow.jpg)



The diagram above depicts a sample flow of control when a protocol password is configured. The component interactions with master key infra are outlined below:

**1)** **Each component must register with the master key infra in mgmt-framework (Section 2.2.6)**.

**2) Component transformer must encrypt incoming passphrase via D-BUS encrypt API (Section 2.2.5.2)**.

**3) Protocol daemons decrypt the encrypted passwords via a D-BUS API (Section 2.2.5.3).**

**4) Config migration for protocol password.** Earlier release/build may have protocol password in plaintext or encrypted form (hardcode encryption key). The protocol password will hence have to be migrated appropriately on an upgrade/downgrade case.

## 2.3 Design choice

An alternative design explored for the communication between master key infra and clients was a simple library API based interface that would need access to the key file from within the dockers. This was a concern since access to the key file is no longer centralized and secure since it is easily readable via the docker. In addition, the idea was to introduce an infra that could simply replace the key file with a TPM in future which would be easy with the D-BUS approach.

From early profiling data, the time required to encrypt data from a docker by invoking a D-BUS method is about 4-8ms while the same is about 3-8ms when accessing the key file in the docker directly. Given that the performance of both these options is similar coupled with the fact that the D-BUS approach fulfills most of the requirements, it was chosen as the communication interface. 

## 2.4 Limitations

- The derived encryption key is saved in a file on the flash. Though the file is protected from non-root users, the file can still be read by root users. The idea behind the file approach is to have an infra in place that can possibly be migrated to use a TPM based design sometime down-the-line.

- Most of the protocol passwords are ultimately saved in respective config files in plaintext. With protocol code being open-source, the passwords are expected to be saved in plaintext. However, the config files are only root accessible.

- The master key infra uses D-BUS as an interface rather than a plain API based interface. A round trip over D-BUS (client invoking a D-BUS method and receiving a response) is technically in the order of a few milliseconds. This is typically higher compared to encrypting/decrypting via inline functions in source as is the case today. D-BUS allows us to centralize the service and make sure access to the key is restricted compared to an API interface. Additionally, the encryption/decryption and master key update operations are not expected to be frequent operations.

  

## 3.1 Overview

## 3.2 DB Changes
### 3.2.1 CONFIG DB

### MASTER_KEY_TABLE

```
;Stores information about master key configuration.
key                    = MASTER_KEY|key                         ; Fixed key "key"
                                                                ; 
configured             = "yes"                                  ; Boolean indicating if master key is                                                                                           ; configured or not.
```

### 3.2.2 APP DB

No changes.

### 3.2.3 STATE DB

No changes.

### 3.2.4 ASIC DB

No changes.

### 3.2.5 COUNTER DB

No changes.

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent

No changes proposed.

### 3.3.2 Other Process 
## 3.4 SyncD

No changes proposed.

## 3.5 SAI

No changes proposed.

## 3.6 User Interface
### 3.6.1 Data Models

TBD

### 3.6.2 CLI
#### 3.6.2.1 Configuration Commands

The configuration commands use the KLISH framework and require KLISH CLI to be in configuration mode (configure terminal);.

##### 3.6.2.1.1 key config-key password-encrypt 

This config command configures the master key to be used for encrypting protocol passwords on the system. 

```
sonic# key config-key password-encrypt 
New key: <enter key>
Confirm key: <enter key>
sonic#
```

The master key can be updated using the same command.

```
sonic# key config-key password-encrypt 
Old key: <enter key>
New key: <enter key>
Confirm key: <enter key>
sonic#
```

The "no"version of the command can be used to reset the master key to factory default (and hence move to the device specific default key).

```
sonic# no key config-key password-encrypt 
WARNING:System will default to device specific master key.  [confirm Y/N]: Y
sonic#
```

Note that this action will re-encrypt the protocol passwords with the default key (device specific key).

#### 3.6.2.2 Show Commands

##### 3.6.2.1.1 show config-key password-encrypt

This command indicates if a master key has been configured.

```
sonic#show config-key password-encrypt
Master key configured: Yes
sonic#
```

#### 3.6.2.3 Debug Commands

#### 3.6.2.4 IS-CLI Compliance
|CLI Command|Compliance|IS-CLI Command (if applicable)| Link to the web site identifying the IS-CLI command (if applicable)|
|:---:|:-----------:|:------------------:|-----------------------------------|
| **key config-key password-encrypt ** | Compliant | **key config-key password-encryption \*[master key]\*** | https://www.cisco.com/c/en/us/support/docs/security-vpn/ipsec-negotiation-ike-protocols/46420-pre-sh-keys-ios-rtr-cfg.html https://www.juniper.net/documentation/us/en/software/junos/user-access/topics/topic-map/master-password-configuration-encryption.html|

### 3.6.3 REST API Support

TBD

### 3.6.4 Service and Docker Management

NA

# 4 Flow Diagrams

4.1.1 Master key update (involves re-encryption of passwords)



![Flow diagram](Flow_diagram.jpg)


# 5 Error Handling

Any errors encountered during the execution of any D-BUS APIs will be logged appropriately to syslog. In addition,  log messages are logged for the following events:

1) New master key configured by user.

"A new master key has been configured on the system."

2) Master key deleted by the user.

"System using unique system default master key."

3) Incorrect old key provided by the user.

"Master key update failed, incorrect old key provided."

4) Encryption/decryption operation failed.

"Encryption operation failed."

5)  In the absence of a configured master key, a message is logged to indicate to the user that the system is working with the default master key. 

"System using a unique default master key. Please configure a master key using "key config-key password-encrypt" command."

# 6 Serviceability and Debug

NA

# 7 Warm Boot Support

The master key infra works seamlessly post a warm-reboot. 

# 8 Scalability

Customer networks may require multiple/all switches in the network to be configured with the same master key to support config portability. In this case, ZTP can be used to configure the same master key passphrase on all the switches in the network. Attention is however required to the fact that ZTP data today is plaintext and this needs to be enhanced to make sure that the master key configuration is protected.  

# 9 Unit Test

1) Verify that system is able to derive a unique master key in the absence of user configured key.

2) Verify that the system is able to encrypt/decrypt successfully with the default key in the absence of user configured key.

3) Verify that the default master key is in-accessible to the user via UI.

4) Verify that the a warning message is logged to syslog regularly indicating to the user that the system is working with a default key.

5) Verify that the default key is unique per box and remains persistent across reboots.

6) Verify that user is able to configure a master key successfully from UI.

7) Verify that the system re-encrypts all existing protocol passwords with the new master key (decrypt with default key and encrypt with new key) and writes them back to CONFIG_DB.

8) Verify that protocol functions appropriately even after a master key update.

9) Verify that the user is prompted to provide the correct old passphrase while trying to update the master key passphrase.

10) Verify that the user is not allowed to retry a master key update in the case of a failure of authentication w.r.t older master key. The system must not allow the user to retry at least for 1 second.

11) Verify that the user is able to update the master key successfully.

12) Verify that the master key infra successfully updates all passwords already configured on the system with new key.

13) Verify that the master key file is not accessible to non-root users.

14) Verify that the new master key is persistent across reboots.

15) Verify that the deletion of the master key results in the system falling back to the default key. The system must log a message indicating that the system is working with a default key.

16) Verify that the configuration from 1 switch can be migrated to another provided the master keys are the same.

17) Verify that the system warns the user of a possible failure on config apply if the user has not made sure that the same master key is configured on both the switches.

18) Verify that the running config displays all the protocol passwords in encrypted format.

19) Verify that all protocol dockers are able to successfully decode the encrypted password using the infra.

20) Verify that the master key registration infra works appropriately i.e registration/de-registration with infra.

21) Verify that the system logs appropriate error messages on encryption/decryption failure.

22) Verify that the encryption/decryption services work seamlessly post a warm-reboot.

23) Verify that the key management services are available right post the system bootup.

24) Verify that the master key infra is able to successfully handle encryption/decryption of multiple passwords.

25) Verify that the master key infra does not save the user provided passphrase as it is and derives a key from the same for the master key.



# 10 Internal Design Information

The master key is saved on the flash at **/etc/masterkey/masterk**.

