# Broadcom Secure Boot

## High Level Design Document
**Rev 1.0**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Feature Overview](#feature-overview)
  * [Functional Requirements](#functional-requirements)
* [Design](#design)
  * [](#)
* [CLI](#cli)
* [Unit Test](#unit-test)
* [Internal Design Information](#internal-design)


# Revision

Rev   |   Date   |    Author   | Change Description
:---: | :-----:  | :---------: | :---------
1.0   | 06/29/21 | Fuzail Khan | Initial version


# About this Manual

This document describes the Secure Boot feature enabled in Broadcom SONiC Network Operating System (NOS). It deals with process of extending the chain of trust to
the Broadcom SONiC NOS image and how different components are combined to fetch a full scale secure boot solution.

# Scope

Secure boot is enabled at the very basic level of BIOS/UEFI. This component serves as the root of trust (RoT). The trust is extended the to various stages of booting.
This document covers the process of extending the chain of trust to Broadcom SONiC installer image. Here HW supplier and ONIE software suppoliers are assumed to be the same
and the platform comes with secure boot enabled ONIE already loaded on it.

# Definition/Abbreviation
There is a lot of security related terminologies and jargon. Below is a small list to help better understand the document.

### Table 1: Abbreviations

| **Term**     |  **Meaning**                                                    |
|:-------------|:----------------------------------------------------------------|
| NOS          | Network Operating System                                        |
| UEFI         | Unified Extensible Firmware Interface                           |
| BIOS         | Basic Input Output System                                       |
| TPM          | Trusted Platform Module                                         |
| PCR          | Platform Configuration Register, within TPM                     |
| MOK          | Machine Owner Key                                               |
| DER          | DER is the binary format for data structures described by ASN.1 |
| GUID         | Globally Unique Identifier                                      |
| IIB          | Image Information Block, part of ONIE installation image format |
| PEM          | A base64 ASCII representation of DER                            |

# Feature Overview 

In ONIE enabled computing environment, end user put their trust in 
 - Various HW components such as FPGA, CLPDs, Boot firmware etc.
 - Software e.g. ONIE, NOS installers and NOS
Root of trust (RoT) is generally is such a core component which can be trusted explicitly. Usually this RoT is UEFI or BIOS. Trust is then propogated throughout the components
of the boot process. Chain of trust is formed where one component of the boot process meausures, verfies and execute the next component. If verification fails, the boot
process is aborted.
ONIE provides support for secure boot by including various applications, keys and certificates management. To apply the secure boot concept to SONiC installer image, we must
provide the NOS image in way suitable for secure boot applications. The NOS image should be signed and Image Information Block (IIB) should be appended to it. Since the NOS image can be
loaded via sonic_installer command as well, an infra similar to ONIE nos intaller must be present to validate the new NOS image.

## Functional Requirements

Functional requirements includes

 - Only a trusted NOS image should be able to load on the machine from ONIE environment
 - Signing the Broadcom SONiC NOS executible image with a given key and create an image in 'Signed ONIE Installable Image Format'
 - Signing the Broadcom SONiC NOS grub and linux kernel with a given key
 - In case of failure in verification, load process shoudl be aborted and ONIE mode should be enabled
 - Only a trusted SONiC-NOS image should be loaded on the machine from SONiC environment
 - User should be able to turn the secure boot feature on/off

# Design

## ONIE and Secure Boot

ONIE is linux based install environment which is widely used for installing other NOS, Diagnostic OS and firmware upgradation images. Opensource project 'shim' is commonly accepted method for booting linux in a UEFI secure boot environment. Same shim based model is also applied to ONIE. shim utilizes two EFI applications mentioned below,
 - shimx64.efi
 - MokManager.efi

UEFI maintains two databases 'db' (Valid keys and certificates) and 'dbx' (revoked keys and certificates). UEFI firmware loads the first stage loader (here, its shim) only iff,
 - db contains a matching cryptographic hash of the image, or a public certificate which can validate the signed image
 and
 - Hash or the certificate should not be part of the 'revoked key database' dbx

shim is a very light weight EFI application who's job is to measure, verify and then load the next bootloader component. It is signed by a private key who\'s public key is available in UEFI database db. It also contains public key certificate of key which is used to sign the next stage loader. If shim is unableto verofy the next stage loader, grub, it defaults to launching the MokManager.efi allowing machine owner to enroll its public keys for verification.

Machine Owner Key (MoK) is the key from machine's owner and used to sign the EFI binaries, linux kernel and bootloaders. MokManager.efi is EFI application to manage the MoK in a secure manner. It is signed by the same key who's public certificate is embedded in shim. It maintains two databases 'mok' and 'mokx' for valid and revoked keys respectively. MokManager.efi provides an interface to add and delete a key into mok or mokx and reset the MOK databases. A typical boot sequence is mentioned in the diagram below,
![Figure1: Boot Sequence](images/boot_sequence.png)

As the HW vendor and ONIE provider are same, additional components would also be provided by the platform vendor. The table below explains it.
![Figure2: Component Details](images/component_details.png)

## Signed ONIE Installable NOS Image Format
It is desirable to use signed NOS installable image. Since the NOS image is intalled via ONIE, it needs to be in an specified format. The new format consist of 3 sections.
 - Executible Installer Data (EID)
 - Digital signature of EID
 - Image Information Block (IIB)

Executible Installer Data is the actual NOS executible image which ONIE discovery mechanism finds and executes. To understand better, this is the NOS binary image had the secure boot not been enabled. As secure boot is enabled now, we need a Digital Signature to cover this EID.

### Image Information Block (IIB)
Image information block is a packed c-style structure consisting of
![Figure3: Image Information Block](images/image_information_block.png)

The ONIE image discovery mechanism would easily identify the 'signed ONIE installable image' by attempting to read the ONIE-image-id from the end of the image. The ONIE image id is fixed for this structure and it is defined as
```
ONIE-Image-Id := 216e9675-be17-46c7-aa71-e525eac83bd2
```

Signature Id too, is a fixed GUID. As currently supported signature type is PKCS#7, which is also identified as UEFI GUID EFI_CERT_TYPE_PKCS7_GUID, below is the signature id,
```
EFI_CERT_TYPE_PKCS7_GUID := 4aafd29d-68df-49ee-8aa9-347d375665a7
```

Signature offset and siganture length are self explanatory. Here is a typical example of the IIB,
|  Field Name       |   Value
| :---------------: | :---------------
|  ONIE-Image-Id    | 216e9675-be17-46c7-aa71-e525eac83bd2
|  Signature-Id     | 4aafd29d-68df-49ee-8aa9-347d375665a7
|  Signature-Offset | 157286400
|  Signature-Length | 1675


# CLI:

 
# Unit Test 


# Internal Design Information:

## Supported Platforms



