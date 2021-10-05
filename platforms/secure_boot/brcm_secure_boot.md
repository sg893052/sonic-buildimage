# Secure Boot - Enterprise SONiC 

## High Level Design Document
**Rev 1.2**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Feature Overview](#feature-overview)
  * [Functional Requirements](#functional-requirements)
* [Design](#design)
  * [SHIM and Secure Boot](#shim-and-secure-boot)
  * [Key Databases](#key-databases)
  * [Secure Boot Sequence](#secure-boot-sequence)
  * [Onie and Secure Boot](#onie-and-secure-boot)
  * [Signed ONIE Installable NOS Image Format](#signed-onie-installable-nos-image-format)
    * [Image Information Block](#image-information-block)
  * [Secure Boot in SONiC](#secure-boot-in-sonic)
    * [secure-sonic utility](#secure-sonic-utility)
    * [Reusing the ONIE shim](#reusing-the-onie-shim)
    * [Kernel Lock down](#kernel-lock-down)
    * [Setting up secure boot](#setting-up-secure-boot)
    * [sonic-installer](#sonic-installer)
    * [onie-nos-install](#onie-nos-install)
* [User Interface](#user-interface)
  * [Data Models](#data-models)
  * [REST URIs](#rest-uris)
  * [CLI](#cli)
  * [onie-nos-install](#onie-nos-install)
  * [sonic-installer](#sonic-installer)
* [Supported Platforms](#supported-platforms)
* [Unit Tests](#unit-tests)
* [Appendix](#appendix)
  * [Implementation Notes](#implementation-notes)



# Revision

Rev   |   Date   |    Author   | Change Description
:---: | :-----:  | :---------: | :---------
1.0   | 06/29/21 | Fuzail Khan | Initial version
1.1 | 09/27/21 | Fuzail Khan/ Rajendra Dendukuri | Design updates 
1.2 | 10/12/21 | Rajendra Dendukuri | Review comments 


# About this Manual

This document describes the Secure Boot feature enabled in Enterprise SONiC Network Operating System (NOS). It describes the process of extending the chain of trust to the Enterprise SONiC NOS image and how individual components of the boot process are combined to form an end to end secure boot solution.

# Scope

Secure boot is enabled at the very basic level of UEFI BIOS (UEFI). Having secure boot enabled in UEFI serves as the root of trust (RoT). This trust is extended to various stages of booting i.e. from UEFI to bootloader to ONIE or UEFI to bootloader to NOS.

This document covers the process of extending the chain of trust to the Enterprise SONiC installer image. The H/W supplier and the ONIE software suppliers are assumed to be the same or at the least assumed to be working closely. The platform comes out of the factory with a secure boot enabled UEFI BIOS, ONIE image and cryptographic keys required for image verification already installed.

Packaging, booting and installation of firmware updates including and not just limited to CPLD/BMC/UEFI firmware in the Secure Boot environment are beyond the scope of this document.

# Definition/Abbreviation

There are a lot of security related terminologies and jargon mentioned in this document. The below mentioned list can be used for reference.

### Table 1: Abbreviations

| **Term**     |  **Meaning**                                                    |
|:-------------|:----------------------------------------------------------------|
| NOS          | Network Operating System                                        |
| UEFI         | Unified Extensible Firmware Interface                           |
| BIOS         | Basic Input Output System                                       |
| ROT          | Root of trust                                                   |
| TPM          | Trusted Platform Module                                         |
| PCR          | Platform Configuration Register, within TPM                     |
| MOK          | Machine Owner Key                                               |
| DER          | DER is the binary format for data structures described by ASN.1 |
| GUID         | Globally Unique Identifier                                      |
| IIB          | Image Information Block, part of ONIE installer image format |
| PEM          | A base64 ASCII representation of DER                            |
| CMS          | Cryptographic Message Syntax [RFC-5652](https://datatracker.ietf.org/doc/html/rfc5652)  |

# Feature Overview 

In an ONIE enabled computing environment, end users put their trust in:
 - Various HW components such as FPGA, CLPDs, Boot firmware
 - Software e.g. ONIE, GRUB bootloader, NOS installers and NOS

Root of trust (RoT) generally is a core component which can be trusted explicitly. Usually this RoT is UEFI. This trust is then propagated throughout the next components
of the boot process. A chain of trust is formed where one component of the boot process measures, verifies and executes the next component. If the verification fails at any one stage, the boot process is aborted.

ONIE supports secure boot by including a signed ONIE software, various applications, cryptographic keys and cryptographic certificates management tools  which are all used to verify the boot process.

The secure boot concepts used on ONIE are also adopted to extend the chain of trust to Enterprise SONiC. The following components of the Enterprise SONiC image are signed
 - grub
 - linux kernel
 - Enterprise SONIC installer image (sonic-broadcom-xxxx.bin)

 In addition to that the following packages are included in the NOS  and are used for cryptographic key management.
 - mokutil
 - sbsigntool

The signed NOS installer image will be verified by the ONIE software which has already booted into a secure boot mode. This results in extending the chain of trust to the NOS image being installed. Thus unverified code cannot be installed on it. Since the NOS image can be loaded using the sonic-installer command as well, checks similar to the one performed by the ONIE nos-installer script are used to validate the downloaded NOS image prior to installing it.

The signed components of the NOS are used to verify  the next component in the boot chain. E.g grub verifies linux kernel.

The cryptographic keys installed in the factory are visible to both ONIE and NOS.

## Functional Requirements

A proposal for the following functional requirements are discussed in this document:

 - A trusted ONIE image should be allowed to be installed on the switch
 - Only a trusted NOS image must be allowed to be installed on the switch from the ONIE environment
 - Users must be allowed to sign Enterprise SONiC NOS images with a cryptographic key of their choice to create a signed image in an ONIE Installable Image Format.
 - Users must be allowed to sign grub and Linux kernel embedded in the Enterprise SONiC image using a cryptographic key of their choice. The same cryptographic key is installed on the switch.
 - When operating in a secure boot environment, upon failure in verification of the boot component, an error message indicating the verification failure must be displayed and halt the boot procedure. This ensures that the switch doesn't end up in a reboot cycle.
 - When a boot time verification failure is reported, the user must be able to load a different NOS or ONIE to recover the switch
 - When secure boot is active, only a trusted SONiC-NOS image should be loaded on the machine from SONiC environment
 - When secure boot is active, upon failure in verification of the image being installed from ONIE prompt or SONiC environment, the image install process must be aborted and reported as a failure
 - A user is allowed to modify secure boot configuration either by using UEFI BIOS option or using the mokutil tool available in SONiC and ONIE. The user is required to connect to the switch using a serial console to confirm these configuration changes.
 - When secure boot is inactive, a previously installed image must continue to work and allow installation of a signed and unsigned NOS image



The following functional requirements are planned for a future release:
 - Linux kernel modules must be signed using a cryptographic key of the user's choice. The signed modules are verified by the trusted kernel before they are installed (insmod'ed) into the kernel.

 - The initramfs used by the NOS must be signed using a cryptographic key of the user's choice. The initramfs is verified by the trusted GRUB bootloader before it is loaded into RAM and passed as a boot argument to the trusted kernel 

 - The squashfs root filesystem used by the SONiC NOS must be signed using a cryptographic key of the user's choice. A signature of the squashfs image is verified by the trusted initramfs before it is expanded and used as the root mount.

   

# Design

## SHIM and Secure Boot

The opensource project shim software package is used as the first-stage bootloader on secure boot enabled SONiC switches.

Shim then becomes the root of trust for all the UEFI executables provided by SONiC. It contains a key which can be used to  sign SONiC Linux kernel and  GRUB. The chain of trust can then be extended by the kernel to other software components.

shim version 15 is recommended to be used for implementing Secure Boot in SONIC .

The shim software packages provides the below EFI applications which are useful to manage the secure boot mode
 - shimx64.efi
 - MokManager.efi

The shimx64.efi is the first stage boot loader executable which is verified and loaded by UEFI. The MokManager.efi is used to manage keys stored in the Machine Owner Key database. If the signature validation of the second stage bootloader fails, the mokmanager.efi is loaded giving the user an opportunity to install new keys. Any new keys imported by the user while using SONiC, need to be confirmed after a reboot. To confirm such secure boot configuration changes, the MokManager.efi is launched during the next bootup.  

Administrators can use the mokutil Linux command to view and manage the secure boot configurations.

## Key Databases

UEFI maintains two databases 'db' (Valid keys and hashes) and 'dbx' (revoked keys and hashes). UEFI firmware loads the first stage loader, shim, only if  a matching cryptographic hash of the image or a public certificate which can validate the signed image is found in db

Similar to the UEFI provided key databases, the shim also provides support for two key databases MoK(Valid keys and hashes) and MoKx(revoked keys and hashes)

Use the mokutil command to dump the keys stored in the key database.

```
mokutil --db
mokutil --dbx
mokutil --list-enrolled
mokutil --mokx --list-enrolled
```

In SONiC secure boot, it is recommended to use keys to verify image signatures instead of image hashes for administrative conveniences.



## Secure Boot Sequence

The below mentioned image explains how each boot stage is verified starting from the left. UEFI verifies the shimx64.efi using the key stored in the UEFI db. It also ensures that it is not signed using a key stored in the UEFI dbx. The shim application has the machine owner key stored in MoK or UEFI db. Shim uses the keys found in MoK to validate the signature of the grub bootloader. The grub bootloader then uses shim API interface to validate Linux kernel. When shim is not able to verify either grub or Linux kernel, MokManager.efi is loaded to provide an interface to add/delete a key into mok/mokx. shim also uses mokx, UEFI dbx to perform a check if the binaries are not signed using revoked keys.



![Figure1: Boot Sequence](images/boot_sequence.png)





## ONIE and Secure Boot

ONIE is a Linux based install environment which is widely used for installing NOS images including SONiC, Diagnostic OS and firmware (e.g CPLD) upgradation images. The 'shim' software package is also used to boot ONIE in secure boot mode. 


 > NOTE: Secure Boot is not supported on legacy BIOS systems. The system must support UEFI-BIOS to enable Secure Boot.



As the HW vendor and ONIE provider are typically the same, additional components need to be provided by the platform vendor. The below table mentioned in the [ONIE secure boot specification ](http://mirror.opencompute.org/onie/docs/ONIESecureBootv2.pdf) provides the details.
![Figure2: Component Details](images/component_details.png)

In SONiC secure boot implementation, the ONIE shim is also used by SONiC to verify SONiC grubx64.efi and SONiC kernel. It is recommended that SONiC users work closely with the hardware vendor to have the designed public keys used to sign SONiC be included either in ONIE shim or  the UEFI db when rolling out the switch from manufacturing. Installing keys and modifying BIOS settings to enable secure boot is a manual process and it is convenient to get it setup during the manufacturing process.



## Signed ONIE Installable NOS Image Format

While the secure boot feature provides a guarantee that only valid code is executed, there are no checks in place to prevent a user from installing a new image which has malicious contents.  In such a scenario, the switch will fail to start up on the next reboot. To avoid such inconvenient situations, it is desirable to verify the downloaded image before installing it onto the disk.  The [ONIE secure boot specification ](http://mirror.opencompute.org/onie/docs/ONIESecureBootv2.pdf) defines a scheme to sign an ONIE installer image in a well defined format. The ONIE installer image can now be considered to have 3 sections.

 - Executable Installer Data (EID)
 - Digital signature of EID
 - Image Information Block (IIB)

Executable Installer Data is the traditional NOS executable image which has been used by various systems that use ONIE. To sign this executable installer data, two additional sections have been added. The digital signature is the cryptographic hash generated in the PKCS #7: Cryptographic Message Syntax. To generate the cryptographic hash, the sha1sum of EID is calculated and used as the message text which is encrypted. Since ONIE installable images are typically large in size, by using the shal1sum of the EID as the message, it is faster to perform multiple checks using multiple stored keys. The public key counter part of the secret key used to generate the signature is expected to be stored on the SONiC switch. The public keys used are stored as certificates in either the MoK or UEFI db. This allows the same keys to be used to generate the installer signature and also sign the kernel or grub binaries.



The image information block is used to store the signature information as described in the next section. 



### Image Information Block

Image information block is a packed c-style structure of 48 bytes consisting of the below mentioned sub-components as per the  [ONIE secure boot specification ](http://mirror.opencompute.org/onie/docs/ONIESecureBootv2.pdf)
![Figure3: Image Information Block](images/image_information_block.png)

The ONIE image discovery mechanism and the sonic-installer command checks if the downloaded if downloaded image is indeed a 'signed ONIE installable image' by attempting to read the ONIE-image-id from the end of the image. The ONIE image id is identified by the GUID.

```
ONIE-Image-Id := 216e9675-be17-46c7-aa71-e525eac83bd2
```

The Signature Id is also expressed as a GUID. The current supported signature type is PKCS#7, which is also identified as UEFI GUID EFI_CERT_TYPE_PKCS7_GUIDd,
```
EFI_CERT_TYPE_PKCS7_GUID := 4aafd29d-68df-49ee-8aa9-347d375665a7
```

Signature offset and signature length are self explanatory. The below is a sample IIB of an ONIE installer image:

|  Field Name       |   Value |
| :---------------: | ----------------|
|  ONIE-Image-Id    | 216e9675-be17-46c7-aa71-e525eac83bd2 |
|  Signature-Id     | 4aafd29d-68df-49ee-8aa9-347d375665a7 |
|  Signature-Offset | 157286400 |
|  Signature-Length | 705 |



As part of the proposed changes to enable secure boot in SONiC, enhancements to ONIE are also proposed. Current ONIE open source code does not have the support to read, verify and execute the signed NOS installer. All enhancements made to ONIE are contributed to open source ONIE.



## Secure Boot in SONiC

To start SONiC in secure boot mode, a few changes have been made to SONiC's installation and boot procedure. The changes made to SONiC do not affect platforms which do not have secure boot enabled.

In Secure Boot enabled platforms, a monolith bootloader grubux64.efi is created and is used as the second stage bootloader. Using a single EFI as the bootloader simplifies the signing process. The other option will result in signing all the grub modules as well as the grub binary. The Linux kernel binary used in Secure Boot enabled platforms is the same except the presence of a signature when used in Secure Boot enabled mode.

Secure Boot requires the binaries to be signed using a private key. This process is simple if we are building an image from source. Additional steps can be added to sign the binary during the build process. However, Enterprise SONiC can be a binary release given to our channel partners to re-sell to others. Secure Boot in SONiC enables the choice of signing the contents of a SONiC binary image after it has been created. 



### secure-sonic utility

A new tool, secure-sonic, is introduced which can be used to sign the monolith grub bootloader (grubx64.efi) and Linux kernel (vmlinuz) inside a SONiC image.  This tool is packaged along with the sonic_utilities wheel package and is available to be used on the SONiC switch. It is a standalone UNIX style shell script and can be used on a Linux host to sign SONiC images. See the below for detailed information on the usage of the *secure-sonic* tool. sbsigntool and openssl packages are required to use the *secure-sonic* tool to sign a SONiC image.


```
 SONiC signing utility - 0.9

 Usage: secure-sonic < --create | --extract > --installer-dir <directory> --image <filename>
                            or
                     --copy-shim --installer-dir <directory> --shim <shim-efi> --mok-mgr <mok-efi>
                            or
                     --export-efi-certs --keys-dir <directory>
                            or
                     --quiet
                            or
                     --sb-state
                            or
                     --sign <grub|kernel> --installer-dir <directory> --key <private-key> --cert <cert-file>
                            or
                     --sign --image <filename> --key <private-key> --cert <cert-file>
                            or
                     --sign-all --image <filename> --key <private-key> --cert <cert-file>
                            or
                     --strip-sign --image <filename>
                            or
                     --verify --image <filename> --cert <cert-file>
                            or
                     --verify --image <filename> --use-efi-certs
                            or
                     --verify --image <filename> --use-efi-certs --strip-sign
                            or
                     --version
                            or
                     --help

 Utility to extract, sign and create SONiC images

 COMMAND LINE OPTIONS

 -c | --create
     Create a SONiC installer image

 -D | --installer-dir | --keys-dir
      Specify directory where SONiC installer image is to be extracted or created from.
      It is also used to sign components of a SONiC image. It is also used to specify the
      directory where stored keys in EFI variables can be exported to.

 -e | --export-efi-certs
     Export certificates stored in EFI variables to the specified keys directory. The
     certificates stored in MoK, MOKx, db and dbx databases are exported.

 -f | --use-efi-certs
     Use certificates stored in EFI variables to validate the digital signature of an
     input SONiC image

 -h | --help
     Help. Prints this text.

 -H | --shim
     Input shimx64.efi filename

 -i | --image
     Specify input SONiC installer image filename

 -k | --key
     Input public cryptographic key certificate used for digitally signing  the
     input image. This file is required to be in the PEM format.

 -l | --sb-state
     Display secure boot status.This command Returns:
                                0 if secure boot is active
                                1 if secure boot is supported and disabled
                                2 if secure boot is not supported

 -M | --copy-shim
     Command option to copy input shim and mokmanager EFI binaries into the input
     SONiC installer image

  -o | --mok-mgr
     Input mokmanager.efi filename

 -q | --quiet
     Do not display verbose out of the commands

 -r | --cert
     Input public cryptographic key certificate used for digitally signing and verifying the
     input image. This file is required to be in the PEM format.

 -s | --sign
     Sign specified component of  SONiC installer image

 -S | --sign-all
     Sign kernel and bootloader of the input SONiC installer image and append a digital
     signature to the installer image

 -t | --strip-sign
     Remove the digital signature from the input SONiC installer image

 -v | --version
     Display secure-sonic script version

 -V | --verify
     Verify the digital signature of the specified SONiC installer image

 -x | --extract
     Extract input SONiC installer image
```



Below is a sample output of signing an Enterprise SONiC image

```
bash-4.2$ ./secure-sonic --sign-all --image sonic-broadcom-enterprise-advanced.bin \
                                     --key $KEYS_DIR/brcm-SHIM-secret-key.pem \
                                     --cert $KEYS_DIR/brcm-SHIM-cert.pem
================================================================================
Inspecting SONiC image sonic-broadcom-enterprise-advanced.bin
================================================================================
SONiC image checksum matched
================================================================================
Extracting SONiC image sonic-broadcom-enterprise-advanced.bin into /tmp/tmp.hKqL6AnAsg
================================================================================
Archive:  /tmp/tmp.hKqL6AnAsg/installer/fs.zip
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/initrd.img-4.19.0-9-2-amd64
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/grubx64.efi
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/shimx64.efi
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/mmx64.efi
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/vmlinuz-4.19.0-9-2-amd64
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/config-4.19.0-9-2-amd64
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/boot/System.map-4.19.0-9-2-amd64
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_agc032-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_agc032-r0/platform-modules-agc032_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-juniper_qfx5200-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-juniper_qfx5200-r0/sonic-platform-juniper-qfx5200_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_belgite-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_belgite-r0/platform-modules-belgite_0.9_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_silverstone_dp-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_silverstone_dp-r0/platform-modules-silverstone-dp_0.9_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_silverstone-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_silverstone-r0/platform-modules-silverstone_0.9_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-juniper_qfx5210-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-juniper_qfx5210-r0/sonic-platform-juniper-qfx5210_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag9032v2a-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag9032v2a-r0/platform-modules-ag9032v2a_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-bcm_xlr-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-bcm_xlr-r0/sonic-platform-brcm-xlr-gts_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_bes2348t-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_bes2348t-r0/sonic-platform-alphanetworks-bes2348t_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snj61d0_320f-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snj61d0_320f-r0/sonic-platform-alphanetworks-snj61d0-320f_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snj60d0_320f-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snj60d0_320f-r0/sonic-platform-alphanetworks-snj60d0-320f_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snj60b0_320f-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snj60b0_320f-r0/sonic-platform-alphanetworks-snj60b0-320f_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snh60b0_640f-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snh60b0_640f-r0/sonic-platform-alphanetworks-snh60b0-640f_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snh60a0_320fv2-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-alphanetworks_snh60a0_320fv2-r0/sonic-platform-alphanetworks-snh60a0-320fv2_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix7t_dnv-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix7t_dnv-r0/sonic-platform-extreme-slx9250-32c_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8f_dnv-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8f_dnv-r0/sonic-platform-extreme-slx9150-48y_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix9_bwde-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix9_bwde-r0/sonic-platform-quanta-ix9-32x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8a_bwde-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8a_bwde-r0/sonic-platform-quanta-ix8a-bwde-56x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8c_bwde-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8c_bwde-r0/sonic-platform-quanta-ix8c-56x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8_rglbmc-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix8_rglbmc-r0/sonic-platform-quanta-ix8-56x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix7_bwde-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix7_bwde-r0/sonic-platform-quanta-ix7-bwde-32x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix7_rglbmc-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix7_rglbmc-r0/sonic-platform-quanta-ix7-32x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix4_bwde-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix4_bwde-r0/sonic-platform-quanta-ix4-64x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix1b_rglbmc-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-quanta_ix1b_rglbmc-r0/sonic-platform-quanta-ix1b-32x_1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_et-6248brb-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_et-6248brb-r0/platform-modules-et-6248brb_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag5648-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag5648-r0/platform-modules-ag5648_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag9064-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag9064-r0/platform-modules-ag9064_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag9032v1-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-delta_ag9032v1-r0/platform-modules-ag9032v1_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_seastone_2-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_seastone_2-r0/platform-modules-seastone2_0.9_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_e1031-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_e1031-r0/platform-modules-haliburton_0.9_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_seastone-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-cel_seastone-r0/platform-modules-dx010_0.9_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7315_27xb-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7315_27xb-r0/sonic-platform-accton-as7315-27xb_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7312_54xs-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7312_54xs-r0/sonic-platform-accton-as7312-54xs_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5835_54t-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5835_54t-r0/sonic-platform-accton-as5835-54t_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as9716_32d-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as9716_32d-r0/sonic-platform-accton-as9716-32d_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5835_54x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5835_54x-r0/sonic-platform-accton-as5835-54x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5812_54t-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5812_54t-r0/sonic-platform-accton-as5812-54t_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5812_54x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5812_54x-r0/sonic-platform-accton-as5812-54x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_minipack-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_minipack-r0/sonic-platform-accton-minipack_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as4630_54pe-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as4630_54pe-r0/sonic-platform-accton-as4630-54pe_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7726_32x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7726_32x-r0/sonic-platform-accton-as7726-32x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as6712_32x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as6712_32x-r0/sonic-platform-accton-as6712-32x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7716_32xb-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7716_32xb-r0/sonic-platform-accton-as7716-32xb_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7326_56x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7326_56x-r0/sonic-platform-accton-as7326-56x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7312_54x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7312_54x-r0/sonic-platform-accton-as7312-54x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7716_32x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7716_32x-r0/sonic-platform-accton-as7716-32x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7816_64x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7816_64x-r0/sonic-platform-accton-as7816-64x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5712_54x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as5712_54x-r0/sonic-platform-accton-as5712-54x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7712_32x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-accton_as7712_32x-r0/sonic-platform-accton-as7712-32x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s9200_64x-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s9200_64x-r0/sonic-platform-ingrasys-s9200-64x_1.1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s8810_32q-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s8810_32q-r0/sonic-platform-ingrasys-s8810-32q_1.1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s9100-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s9100-r0/sonic-platform-ingrasys-s9100_1.1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s8900_64xc-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s8900_64xc-r0/sonic-platform-ingrasys-s8900-64xc_1.1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s8900_54xc-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-ingrasys_s8900_54xc-r0/sonic-platform-ingrasys-s8900-54xc_1.1.0_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9432f_c3758-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9432f_c3758-r0/platform-modules-z9432f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5224f_c3538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5224f_c3538-r0/platform-modules-s5224f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_n3248pxe_c3338-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_n3248pxe_c3338-r0/platform-modules-n3248pxe_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_n3248x_c3338-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_n3248x_c3338-r0/platform-modules-n3248x_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_n3248te_c3338-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_n3248te_c3338-r0/platform-modules-n3248te_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_s3000_c2338-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_s3000_c2338-r0/platform-modules-s3000_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_s6100_c2538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_s6100_c2538-r0/platform-modules-s6100_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9100_c2538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9100_c2538-r0/platform-modules-z9100_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_z9100_c2538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_z9100_c2538-r0/platform-modules-z9100_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5296f_c3538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5296f_c3538-r0/platform-modules-s5296f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5248f_c3538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5248f_c3538-r0/platform-modules-s5248f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5232f_c3538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5232f_c3538-r0/platform-modules-s5232f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5212f_c3538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_s5212f_c3538-r0/platform-modules-s5212f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9264f_c3538-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9264f_c3538-r0/platform-modules-z9264f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9332f_d1508-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dellemc_z9332f_d1508-r0/platform-modules-z9332f_1.1_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_s6000_s1220-r0/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-dell_s6000_s1220-r0/platform-modules-s6000_1.1_amd64.deb
 extracting: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/firsttime
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-grub/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/platform/x86_64-grub/grub-pc-bin_2.02+dfsg1-20+deb10u4_amd64.deb
   creating: /tmp/tmp.hKqL6AnAsg/installer/fs/installer-migration-hooks/
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/installer-migration-hooks/08-config-profile-backup
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/installer-migration-hooks/01-local-users-pre
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/installer-migration-hooks/01-kdump-pre
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/installer-migration-hooks/01-dir-preservation
 extracting: /tmp/tmp.hKqL6AnAsg/installer/fs/fs.squashfs
  inflating: /tmp/tmp.hKqL6AnAsg/installer/fs/sonic_version.yml
================================================================================
Signing grubx64.efi
================================================================================
================================================================================
Signing vmlinuz-4.19.0-9-2-amd64
================================================================================
================================================================================
Creating SONiC image sonic-broadcom-enterprise-advanced.bin.unsigned from contents of /tmp/tmp.hKqL6AnAsg
================================================================================
  adding: boot/ (stored 0%)
  adding: boot/grubx64.efi (deflated 67%)
  adding: boot/System.map-4.19.0-9-2-amd64 (deflated 79%)
  adding: boot/shimx64.efi (deflated 59%)
  adding: boot/vmlinuz-4.19.0-9-2-amd64 (deflated 4%)
  adding: boot/mmx64.efi (deflated 60%)
  adding: boot/grubx64.efi.unsigned (deflated 67%)
  adding: boot/config-4.19.0-9-2-amd64 (deflated 76%)
  adding: boot/initrd.img-4.19.0-9-2-amd64 (deflated 1%)
  adding: boot/vmlinuz-4.19.0-9-2-amd64.unsigned (deflated 4%)
  adding: fs.squashfs (deflated 1%)
  adding: installer-migration-hooks/ (stored 0%)
  adding: installer-migration-hooks/01-kdump-pre (deflated 46%)
  adding: installer-migration-hooks/08-config-profile-backup (deflated 40%)
  adding: installer-migration-hooks/01-dir-preservation (deflated 38%)
  adding: installer-migration-hooks/01-local-users-pre (deflated 58%)
  adding: platform/ (stored 0%)
  adding: platform/x86_64-delta_agc032-r0/ (stored 0%)
  adding: platform/x86_64-delta_agc032-r0/platform-modules-agc032_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-alphanetworks_bes2348t-r0/ (stored 0%)
  adding: platform/x86_64-alphanetworks_bes2348t-r0/sonic-platform-alphanetworks-bes2348t_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_s5212f_c3538-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_s5212f_c3538-r0/platform-modules-s5212f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-juniper_qfx5210-r0/ (stored 0%)
  adding: platform/x86_64-juniper_qfx5210-r0/sonic-platform-juniper-qfx5210_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix8f_dnv-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix8f_dnv-r0/sonic-platform-extreme-slx9150-48y_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_n3248pxe_c3338-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_n3248pxe_c3338-r0/platform-modules-n3248pxe_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_z9264f_c3538-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_z9264f_c3538-r0/platform-modules-z9264f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as5835_54t-r0/ (stored 0%)
  adding: platform/x86_64-accton_as5835_54t-r0/sonic-platform-accton-as5835-54t_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix4_bwde-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix4_bwde-r0/sonic-platform-quanta-ix4-64x_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_s5296f_c3538-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_s5296f_c3538-r0/platform-modules-s5296f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7312_54xs-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7312_54xs-r0/sonic-platform-accton-as7312-54xs_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-ingrasys_s9200_64x-r0/ (stored 0%)
  adding: platform/x86_64-ingrasys_s9200_64x-r0/sonic-platform-ingrasys-s9200-64x_1.1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as9716_32d-r0/ (stored 0%)
  adding: platform/x86_64-accton_as9716_32d-r0/sonic-platform-accton-as9716-32d_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7315_27xb-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7315_27xb-r0/sonic-platform-accton-as7315-27xb_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7716_32xb-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7716_32xb-r0/sonic-platform-accton-as7716-32xb_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix8a_bwde-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix8a_bwde-r0/sonic-platform-quanta-ix8a-bwde-56x_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-cel_belgite-r0/ (stored 0%)
  adding: platform/x86_64-cel_belgite-r0/platform-modules-belgite_0.9_amd64.deb (deflated 0%)
  adding: platform/x86_64-alphanetworks_snh60b0_640f-r0/ (stored 0%)
  adding: platform/x86_64-alphanetworks_snh60b0_640f-r0/sonic-platform-alphanetworks-snh60b0-640f_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-ingrasys_s8900_54xc-r0/ (stored 0%)
  adding: platform/x86_64-ingrasys_s8900_54xc-r0/sonic-platform-ingrasys-s8900-54xc_1.1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_s5248f_c3538-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_s5248f_c3538-r0/platform-modules-s5248f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7726_32x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7726_32x-r0/sonic-platform-accton-as7726-32x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-delta_ag9032v1-r0/ (stored 0%)
  adding: platform/x86_64-delta_ag9032v1-r0/platform-modules-ag9032v1_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-ingrasys_s8810_32q-r0/ (stored 0%)
  adding: platform/x86_64-ingrasys_s8810_32q-r0/sonic-platform-ingrasys-s8810-32q_1.1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-juniper_qfx5200-r0/ (stored 0%)
  adding: platform/x86_64-juniper_qfx5200-r0/sonic-platform-juniper-qfx5200_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as5812_54x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as5812_54x-r0/sonic-platform-accton-as5812-54x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-cel_seastone_2-r0/ (stored 0%)
  adding: platform/x86_64-cel_seastone_2-r0/platform-modules-seastone2_0.9_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as5812_54t-r0/ (stored 0%)
  adding: platform/x86_64-accton_as5812_54t-r0/sonic-platform-accton-as5812-54t_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-delta_ag5648-r0/ (stored 0%)
  adding: platform/x86_64-delta_ag5648-r0/platform-modules-ag5648_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_s5224f_c3538-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_s5224f_c3538-r0/platform-modules-s5224f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-alphanetworks_snh60a0_320fv2-r0/ (stored 0%)
  adding: platform/x86_64-alphanetworks_snh60a0_320fv2-r0/sonic-platform-alphanetworks-snh60a0-320fv2_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-bcm_xlr-r0/ (stored 0%)
  adding: platform/x86_64-bcm_xlr-r0/sonic-platform-brcm-xlr-gts_1.0_amd64.deb (deflated 1%)
  adding: platform/x86_64-accton_as5835_54x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as5835_54x-r0/sonic-platform-accton-as5835-54x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as4630_54pe-r0/ (stored 0%)
  adding: platform/x86_64-accton_as4630_54pe-r0/sonic-platform-accton-as4630-54pe_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-cel_silverstone-r0/ (stored 0%)
  adding: platform/x86_64-cel_silverstone-r0/platform-modules-silverstone_0.9_amd64.deb (deflated 0%)
  adding: platform/x86_64-dell_z9100_c2538-r0/ (stored 0%)
  adding: platform/x86_64-dell_z9100_c2538-r0/platform-modules-z9100_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_n3248te_c3338-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_n3248te_c3338-r0/platform-modules-n3248te_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix7_rglbmc-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix7_rglbmc-r0/sonic-platform-quanta-ix7-32x_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_s5232f_c3538-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_s5232f_c3538-r0/platform-modules-s5232f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix7t_dnv-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix7t_dnv-r0/sonic-platform-extreme-slx9250-32c_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7712_32x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7712_32x-r0/sonic-platform-accton-as7712-32x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-ingrasys_s9100-r0/ (stored 0%)
  adding: platform/x86_64-ingrasys_s9100-r0/sonic-platform-ingrasys-s9100_1.1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-grub/ (stored 0%)
  adding: platform/x86_64-grub/grub-pc-bin_2.02+dfsg1-20+deb10u4_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7326_56x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7326_56x-r0/sonic-platform-accton-as7326-56x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7816_64x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7816_64x-r0/sonic-platform-accton-as7816-64x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-cel_e1031-r0/ (stored 0%)
  adding: platform/x86_64-cel_e1031-r0/platform-modules-haliburton_0.9_amd64.deb (deflated 0%)
  adding: platform/x86_64-cel_seastone-r0/ (stored 0%)
  adding: platform/x86_64-cel_seastone-r0/platform-modules-dx010_0.9_amd64.deb (deflated 0%)
  adding: platform/firsttime (stored 0%)
  adding: platform/x86_64-accton_as7716_32x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7716_32x-r0/sonic-platform-accton-as7716-32x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as6712_32x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as6712_32x-r0/sonic-platform-accton-as6712-32x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dell_s3000_c2338-r0/ (stored 0%)
  adding: platform/x86_64-dell_s3000_c2338-r0/platform-modules-s3000_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix8c_bwde-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix8c_bwde-r0/sonic-platform-quanta-ix8c-56x_1.0_amd64.deb (deflated 2%)
  adding: platform/x86_64-quanta_ix7_bwde-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix7_bwde-r0/sonic-platform-quanta-ix7-bwde-32x_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-alphanetworks_snj60b0_320f-r0/ (stored 0%)
  adding: platform/x86_64-alphanetworks_snj60b0_320f-r0/sonic-platform-alphanetworks-snj60b0-320f_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-delta_et-6248brb-r0/ (stored 0%)
  adding: platform/x86_64-delta_et-6248brb-r0/platform-modules-et-6248brb_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_n3248x_c3338-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_n3248x_c3338-r0/platform-modules-n3248x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dell_s6100_c2538-r0/ (stored 0%)
  adding: platform/x86_64-dell_s6100_c2538-r0/platform-modules-s6100_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_z9100_c2538-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_z9100_c2538-r0/platform-modules-z9100_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-cel_silverstone_dp-r0/ (stored 0%)
  adding: platform/x86_64-cel_silverstone_dp-r0/platform-modules-silverstone-dp_0.9_amd64.deb (deflated 0%)
  adding: platform/x86_64-alphanetworks_snj60d0_320f-r0/ (stored 0%)
  adding: platform/x86_64-alphanetworks_snj60d0_320f-r0/sonic-platform-alphanetworks-snj60d0-320f_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_z9332f_d1508-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_z9332f_d1508-r0/platform-modules-z9332f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix8_rglbmc-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix8_rglbmc-r0/sonic-platform-quanta-ix8-56x_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-dellemc_z9432f_c3758-r0/ (stored 0%)
  adding: platform/x86_64-dellemc_z9432f_c3758-r0/platform-modules-z9432f_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-delta_ag9032v2a-r0/ (stored 0%)
  adding: platform/x86_64-delta_ag9032v2a-r0/platform-modules-ag9032v2a_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-dell_s6000_s1220-r0/ (stored 0%)
  adding: platform/x86_64-dell_s6000_s1220-r0/platform-modules-s6000_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as5712_54x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as5712_54x-r0/sonic-platform-accton-as5712-54x_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_minipack-r0/ (stored 0%)
  adding: platform/x86_64-accton_minipack-r0/sonic-platform-accton-minipack_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-ingrasys_s8900_64xc-r0/ (stored 0%)
  adding: platform/x86_64-ingrasys_s8900_64xc-r0/sonic-platform-ingrasys-s8900-64xc_1.1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix1b_rglbmc-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix1b_rglbmc-r0/sonic-platform-quanta-ix1b-32x_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-quanta_ix9_bwde-r0/ (stored 0%)
  adding: platform/x86_64-quanta_ix9_bwde-r0/sonic-platform-quanta-ix9-32x_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-alphanetworks_snj61d0_320f-r0/ (stored 0%)
  adding: platform/x86_64-alphanetworks_snj61d0_320f-r0/sonic-platform-alphanetworks-snj61d0-320f_1.0_amd64.deb (deflated 0%)
  adding: platform/x86_64-delta_ag9064-r0/ (stored 0%)
  adding: platform/x86_64-delta_ag9064-r0/platform-modules-ag9064_1.1_amd64.deb (deflated 0%)
  adding: platform/x86_64-accton_as7312_54x-r0/ (stored 0%)
  adding: platform/x86_64-accton_as7312_54x-r0/sonic-platform-accton-as7312-54x_1.1_amd64.deb (deflated 0%)
  adding: sonic_version.yml (deflated 29%)
Successfully created SONiC image sonic-broadcom-enterprise-advanced.bin.unsigned
================================================================================
Signing SONiC binary image sonic-broadcom-enterprise-advanced.bin.unsigned
================================================================================
Image Digest : a9bab5576b1b34fd7c29962806f534c5f17b37fc
Generating signature for the image digest..
Image Information Block:
ONIE-Image-Id : 216e9675-be17-46c7-aa71-e525eac83bd2
Signature-Id : 4aafd29d-68df-49ee-8aa9-347d375665a7
Signature-Offset : 1278926888
Signature-Length : 705
Successfully created signed SONiC image sonic-broadcom-enterprise-advanced.bin.signed
================================================================================
Verifying signature of sonic-broadcom-enterprise-advanced.bin.signed
Using Key /projects/csg_sonic/sonic_builds/secureboot/brcm-keys/brcm-SHIM-cert.pem
================================================================================
Image Information Block :
ONIE-Image-Id : 216e9675-be17-46c7-aa71-e525eac83bd2
Signature-Id : 4aafd29d-68df-49ee-8aa9-347d375665a7
Signature-Offset : 1278926888
Signature-Length : 705
Image Digest : a9bab5576b1b34fd7c29962806f534c5f17b37fc
Created intermediate files:
sonic-broadcom-enterprise-advanced.bin.signed.sig
sonic-broadcom-enterprise-advanced.bin.signed.iib
sonic-broadcom-enterprise-advanced.bin.signed.sha1sum
Decrypting image signature and comparing it with the image digest..
a9bab5576b1b34fd7c29962806f534c5f17b37fc
Verification successful
```

### Reusing the ONIE shim

When Enterprise SONiC is installed on a switch, the GRUB boot menu is set up to load SONiC or ONIE. This requires SONiC GRUB bootloader to chain load ONIE shim. To successfully allow the chain loading operation, ONIE and SONiC need to use the same shim binary. When the SONiC boot entry is created in the EFI partition, the ONIE shim is copied over to the */boot/efi/EFI/SONIC-OS* directory. This simplifies the process of validating incoming SONiC NOS image signature as the same key embedded in the shim is seen by ONIE and SONiC. Keys in UEFI db or MoK can be used to avoid this key sharing, however that requires manual enrollment steps. To avoid these manual steps, users can choose to upgrade ONIE to introduce a shim image with a different certificate. Even though this approach is restrictive it simplifies and automates key management and sharing between ONIE and SONiC. Since ONIE is an open source software, end users or Enterprise SONiC channel partners will be able to rebuild it without contacting the Enterprise SONiC vendors.



### Kernel Lock down

When Secure Boot mode is activate, the Linux kernel is started in "lockdown" mode. This disables various features that can be used to modify the kernel:

- Loading kernel modules that are not signed by a trusted key. By default, this will block out-of-tree modules including DKMS-managed drivers. 
  - This restriction has been bypassed in SONiC secure boot.
- Using kexec to start an unsigned kernel image.
  - SONiC warm-reboot, fast-reboot and kdump features internally use kexec system call. Since these operations kexec into a signed kernel no issues should be noticeable
- Hibernation and resume from hibernation.
- User-space access to physical memory and I/O ports.
  - Some platform user space drivers may choose to access peripheral devices using memory mapped I/O. In lockdown mode those platform drivers may not work. For secure boot enabled platforms, care should be taken that all platform drivers operate in the same way as they have been working when secure boot is not enabled.
- Module parameters that allow setting memory and I/O port addresses.
- Writing to MSRs through `/dev/cpu/*/msr`.
- Use of custom ACPI methods and tables.
- ACPI APEI error injection.
- Installing BPF filters
  - This restriction has been bypassed in SONiC secure boot



### Setting up secure boot

To use a SONiC switch in secure boot active mode, multiple components need to be setup in a sequence. While there are other ways to achieve the end goal are possible, the following is the recommended procedure:

1. Enter UEFI BIOS configuration, install the platform key and the db key and enable secure boot mode
2. Using ONIE rescue iso, install secure boot enabled ONIE signed using the db key. NOS vendor key is embedded in the ONIE shim
3. Install SONiC NOS image which is signed using the vendor key



This ensures that the software components being installed are all verified before installing and secure boot is always active to ensure that ONIE rescue image, ONIE and SONiC that the switch may boot into are verified at all times.



Users can choose to disable secure boot by entering the UEFI BIOS and disabling secure boot configuration. The switch will continue to boot like before but the Linux kernel will not enter the locked down mode. New ONIE or SONiC images installed in this mode are no longer verified. If the user wishes to enable secure boot again later, it is recommended that the user goes through the above mentioned steps to ensure verified code is installed. If no changes are made to the critical pieces of the installed image like kernel, shim and grub bootloader it may continue to operate in secure boot mode. However if the user tampered with these components, the system may fail to boot.




###  sonic-installer

The *sonic-installer* command in SONiC allows the user to download and install a SONiC image. When used in secure boot active mode, the sonic-installer first validates the signature on the downloaded SONiC image and installs it only after verifying that the signature is valid. In case of failure the image install process is aborted. See below for an example:

```
sonic-installer install -y http://10.59.132.240:9009/projects/csg_sonic/rd925731/repo/dev/sonic-400-secure-boot/target/sonic-vs.bin.signed
Downloading image...
...98%, 1113 MB, 17276 KB/s, 1 seconds left...    Image Install initiation
Command: /usr/local/bin/secure-sonic --sb-state
Secure boot is active, verifying image signature
Command: /usr/local/bin/secure-sonic --verify --strip-sign --use-efi-certs --image /tmp/sonic_image
================================================================================
Verifying signature of /tmp/sonic_image
================================================================================
Image Information Block :
ONIE-Image-Id : 216e9675-be17-46c7-aa71-e525eac83bd2
Signature-Id : 4aafd29d-68df-49ee-8aa9-347d375665a7
Signature-Offset : 1187862568
Signature-Length : 706
Image Digest : 5ec441ffad3e35ea3d74218c85f5046c639cf0d6
Created intermediate files:
/tmp/sonic_image.sig
/tmp/sonic_image.iib
/tmp/sonic_image.sha1sum
Removing the signature from /tmp/sonic_image
================================================================================
Exporting keys stored in EFI variables
================================================================================
Exporting EFI DB keys...
Exporting platform key...
Exporting key exchange key...
Exporting Machine Owner DB keys...
DB-0001.der
DB-0002.der
DB-0003.der
KEK-0001.der
MOK-0001.der
PK-0001.der
Exporting EFI DB revoked keys...
DBX-0001.der
DBX-0002.der
Testing if /tmp/sonic_image is signed using a revoked key listed in the EFI dbx...
Using DBX-0002.der
Using DBX-0001.der
Testing if /tmp/sonic_image is signed using a key listed in the Machine Owner Key database...
Using DB-0003.der
Using PK-0001.der
Using KEK-0001.der
Using DB-0002.der
Testing if the matching key DB-0002.der is a revoked key in the mokx database.
Signature verification successfull using the key DB-0002.der.

Installing image SONiC-OS-broadcom_sonic.0-dirty-20210925.140120 and setting it as default...
Command: /tmp/sonic_image
[ 1029.078702] print_req_error: I/O error, dev fd0, sector 0

<SNIP>

[ TPCM ] Taking backup of running TPC dockers into new image for migration
Command: mkdir -p /tmp/image-broadcom_sonic.0-dirty-20210925.140120-fs
Command: mount -t squashfs /host/image-broadcom_sonic.0-dirty-20210925.140120/fs.squashfs /tmp/image-broadcom_sonic.0-dirty-20210925.140120-fs
Command: sonic-cfggen -d -y /tmp/image-broadcom_sonic.0-dirty-20210925.140120-fs/etc/sonic/sonic_version.yml -t /tmp/image-broadcom_sonic.0-dirty-20210925.140120-fs/usr/share/sonic/templates/sonic-environment.j2
Command: umount -rf /tmp/image-broadcom_sonic.0-dirty-20210925.140120-fs
Command: rm -rf /tmp/image-broadcom_sonic.0-dirty-20210925.140120-fs
Command: sync;sync;sync

Command: sleep 3

Done
Image Download successful
Command: rm -f /tmp/sonic_image
```

If an unsigned image  is provided, the install process is aborted. The current images are left untouched.

```
root@sonic:/home/admin# sonic-installer install root@sonic:/home/admin# sonic-installer install -y http://10.59.132.240:9009/projects/csg_sonic/rd925731/repo/dev/sonic-400-secure-boot/target/sonic-vs.bin
Downloading image...
...99%, 1126 MB, 20244 KB/s, 0 seconds left...    Image Install initiation
Command: /usr/local/bin/secure-sonic --sb-state
Secure boot is active, verifying image signature
Command: /usr/local/bin/secure-sonic --verify --strip-sign --use-efi-certs --image /tmp/sonic_image
================================================================================
Verifying signature of /tmp/sonic_image
================================================================================
Error: Signature header not found in /tmp/sonic_image
onie_image_guid: 00000000-0000-0000-0000-000000000000, expecting 216e9675-be17-46c7-aa71-e525eac83bd2
signature_guid: 00000000-0000-0000-0000-000000000000, expecting 4aafd29d-68df-49ee-8aa9-347d375665a7

Signature verification failed. Aborting...
Command: rm -f /tmp/sonic_image

Aborted!
root@sonic:/home/admin#

```

If a signed image without a valid signature is provided, the install process is aborted. The current images are left untouched.
```
root@sonic:/home/admin#
root@sonic:/home/admin# sonic_installer install installer.test2-key
Warning: 'sonic_installer' command is deprecated and will be removed in the future
Please use 'sonic-installer' instead
New image will be installed, continue? [y/N]: y
 Image Install initiation
Command: /usr/local/bin/secure-sonic --sb-state
Secureboot active

Secure boot is active, verifying image signature
Command: /usr/local/bin/secure-sonic --verify --strip-sign --use-efi-certs --image ./installer.test2-key
================================================================================
Verifying signature of ./installer.test2-key
================================================================================
Image Information Block :
ONIE-Image-Id : 216e9675-be17-46c7-aa71-e525eac83bd2
Signature-Id : 4aafd29d-68df-49ee-8aa9-347d375665a7
Signature-Offset : 2955378728
Signature-Length : 733
Image Digest : 0a17c7aedac93b852881811d44dcb012be9d7c6a
Created files:
./installer.test2-key.sig
./installer.test2-key.iib
./installer.test2-key.sha1sum
./installer.test2-key
================================================================================
Exporting keys stored in EFI variables
================================================================================
Exporting EFI DB keys...
Exporting platform key...
Exporting key exchange key...
Exporting Machine Owner DB keys...
DB-0001.der
DB-0002.der
KEK-0001.der
PK-0001.der
Exporting EFI DB revoked keys...
Testing if ./installer.test2-key is signed using a revoked key listed in the EFI dbx...
Testing if ./installer.test2-key is signed using a key listed in the Machine Owner Key database...
Using KEK-0001.der
Using DB-0001.der
Using DB-0002.der
Using PK-0001.der
Error: Failed to validate signature of ./installer.test2-key using the keys stored in the EFI database.

Signature verification failed. Aborting...
Command: rm -f ./installer.test2-key

Aborted!
root@sonic:/home/admin#
```


If a signed image with a revoked signature is provided, the install process is aborted. The current images are left untouched.

```
root@sonic:/home/admin# sonic-installer install -y http://10.59.132.240:9009/projects/csg_sonic/rd925731/repo/dev/sonic-400-secure-boot/target/sonic-vs.bin.signed
Downloading image...
...98%, 1114 MB, 17830 KB/s, 1 seconds left...    Image Install initiation
Command: /usr/local/bin/secure-sonic --sb-state
Secure boot is active, verifying image signature
Command: /usr/local/bin/secure-sonic --verify --strip-sign --use-efi-certs --image /tmp/sonic_image
================================================================================
Verifying signature of /tmp/sonic_image
================================================================================
Image Information Block :
ONIE-Image-Id : 216e9675-be17-46c7-aa71-e525eac83bd2
Signature-Id : 4aafd29d-68df-49ee-8aa9-347d375665a7
Signature-Offset : 1187862568
Signature-Length : 733
Image Digest : a646e1173c101a786767c8333b67eb465d027895
Created intermediate files:
/tmp/sonic_image.sig
/tmp/sonic_image.iib
/tmp/sonic_image.sha1sum
Removing the signature from /tmp/sonic_image
================================================================================
Exporting keys stored in EFI variables
================================================================================
Exporting EFI DB keys...
Exporting platform key...
Exporting key exchange key...
Exporting Machine Owner DB keys...
DB-0001.der
DB-0002.der
DB-0003.der
KEK-0001.der
MOK-0001.der
PK-0001.der
Exporting EFI DB revoked keys...
DBX-0001.der
DBX-0002.der
Testing if /tmp/sonic_image is signed using a revoked key listed in the EFI dbx...
Using DBX-0002.der
Using DBX-0001.der
Error: SONiC image /tmp/sonic_image is signed using a revoked key DBX-0001.der.

Signature verification failed. Aborting...
Command: rm -f /tmp/sonic_image

Aborted!
```



When secure boot is not active, the *sonic-installer* command accepts both signed and unsigned images. It ignores the signature appended to the signed image.



### onie-nos-install

When operating in secure boot active mode, the ONIE install tools (onie-nos-install and onie-self-update)  also validate the incoming image. Downloaded image needs to have a valid signature which can be verified. If a valid signature is not found, the install procedure is halted. See below for example logs:



*Upgrading ONIE using an unsigned ONIE image*

```
ONIE:/ # onie-self-update -e http://10.59.132.240:9009/projects/csg_sonic/sonic_
builds/secureboot/kvm_onie_iso/onie-updater-x86_64-kvm_x86_64-r0.unsigned
discover: Rescue mode detected. No discover stopped.
Info: Attempting http://10.59.132.240:9009/projects/csg_sonic/sonic_builds/secureboot/kvm_onie_iso/onie-updater-x86_64-kvm_x86_64-r0.unsigned ...
Connecting to 10.59.132.240:9009 (10.59.132.240:9009)
installer            100% |*******************************| 17311k  0:00:00 ETA
ONIE: Executing installer: http://10.59.132.240:9009/projects/csg_sonic/sonic_builds/secureboot/kvm_onie_iso/onie-updater-x86_64-kvm_x86_64-r0.unsigned
ONIE: Secure Boot mode is enabled
Failure: Signature header not found in /var/tmp/installer. Please use a valid signed ONIE installer
ONIE:/ #
```



*Upgrading ONIE using an invalid signed ONIE image*

```
ONIE:/ # onie-nos-install http://10.52.145.123:9009/wrong_image/sonic-broadcom-enterprise-advanced.bin.signed
discover: Rescue mode detected. No discover stopped.
Info: Attempting http://10.52.145.123:9009/wrong_image/sonic-broadcom-enterprise-advanced.bin.signed ...
Connecting to 10.52.145.123:9009 (10.52.145.123:9009)
installer            100% |*******************************|  2825M  0:00:00 ETA
ONIE: Executing installer: http://10.52.145.123:9009/wrong_image/sonic-broadcom-enterprise-advanced.bin.signed
ONIE: Secure Boot mode is enabled
ONIE: Extracting the image signature ...
ONIE: Removing the image signature and the information block from the installer image ...
ONIE: A signed ONIE Installer Image detected. Installer: /var/tmp/installer, Signature: /var/tmp/installer.sign
ONIE: Verifying the signature using EFI keys ...
DB-0001.der
DB-0002.der
KEK-0001.der
MOK-0001.der
PK-0001.der
Failure: Signed ONIE Installer image verification failed
Failure: Signature verification of /var/tmp/installer failed
ONIE:/ #
```



*Upgrading ONIE using a valid signed ONIE image*

```
ONIE:/ # onie-self-update -e http://10.59.132.240:9009/secureboot/kvm_onie_iso/onie-updater-x86_64-kvm_x86_64-r0
discover: Rescue mode detected. No discover stopped.
Info: Attempting http://10.59.132.240:9009/secureboot/kvm_onie_iso/onie-updater-x86_64-kvm_x86_64-r0 ...
Connecting to 10.59.132.240:9009 (10.59.132.240:9009)
installer            100% |*******************************| 17312k  0:00:00 ETA
ONIE: Executing installer: http://10.59.132.240:9009/secureboot/kvm_onie_iso/onie-updater-x86_64-kvm_x86_64-r0
ONIE: Secure Boot mode is enabled
ONIE: Extracting the image signature ...
ONIE: Removing the image signature and the information block from the installer image ...
ONIE: A signed ONIE Installer Image detected. Installer: /var/tmp/installer, Signature: /var/tmp/installer.sign
ONIE: Verifying the signature using EFI keys ...
DB-0001.der
DB-0002.der
DB-0003.der
KEK-0001.der
MOK-0001.der
PK-0001.der
DBX-0001.der
DBX-0002.der
ONIE: Signed ONIE installer image verification successfull
ONIE: Signature verification of /var/tmp/installer is successful
Verifying image checksum ... OK.
Preparing image archive ... OK.
Success: test_install_sharing mechanism
<SNIP>
ONIE: Success: Firmware update URL: http://10.59.132.240:9009/secureboot/kvm_onie_iso/onie-updater-x86_64-kvm_x86_64-r0
ONIE: Success: Firmware update version: sonic_sb_customizations_dev-09240133-dirty
ONIE: Rebooting...
ONIE:/ # discover: Rescue mode detected. No discover stopped.
```



*Installing an Enterprise SONiC image using an un-signed image*

```
ONIE:/ # onie-nos-install http://10.59.132.240:9009/target/sonic-vs.bin
discover: Rescue mode detected. No discover stopped.
Info: Attempting http://10.59.132.240:9009/target/sonic-vs.bin ...
Connecting to 10.59.132.240:9009 (10.59.132.240:9009)
installer            100% |*******************************|  1131M  0:00:00 ETA
ONIE: Executing installer: http://10.59.132.240:9009/target/sonic-vs.bin
ONIE: Secure Boot mode is enabled
Failure: Signature header not found in /var/tmp/installer. Please use a valid signed ONIE installer
ONIE:/ #
```



*Installing an Enterprise SONiC image using an image signed with an invalid key*

```
ONIE:/ # ip addr add 10.59.133.75/20 dev eth0; route add default gw 10.59.128.1
eth0
ONIE:/ # onie-nos-install http://10.59.132.240:9009/projects/csg_sonic/sonic_bui
lds/daily/4.0.0/broadcom/sonic_4.0.0_daily_211004_1430_313/sonic-broadcom-enterp
rise-advanced.bin.signed
discover: Rescue mode detected. No discover stopped.
Info: Attempting http://10.59.132.240:9009/projects/csg_sonic/sonic_builds/daily/4.0.0/broadcom/sonic_4.0.0_daily_211004_1430_313/sonic-broadcom-enterprise-advanced.bin.signed ...
Connecting to 10.59.132.240:9009 (10.59.132.240:9009)
installer            100% |*******************************|  2825M  0:00:00 ETA
ONIE: Executing installer: http://10.59.132.240:9009/projects/csg_sonic/sonic_builds/daily/4.0.0/broadcom/sonic_4.0.0_daily_211004_1430_313/sonic-broadcom-enterprise-advanced.bin.signed
ONIE: Secure Boot mode is enabled
ONIE: Extracting the image signature ...
ONIE: Removing the image signature and the information block from the installer image ...
ONIE: A signed ONIE Installer Image detected. Installer: /var/tmp/installer, Signature: /var/tmp/installer.sign
ONIE: Verifying the signature using EFI keys ...
DB-0001.der
DB-0002.der
KEK-0001.der
MOK-0001.der
PK-0001.der
Failure: Signed ONIE Installer image verification failed
Failure: Signature verification of /var/tmp/installer failed
ONIE:/ #
```


*Installing an Enterprise SONiC image using an image signed with a revoked key*

```
ONIE:/ # onie-nos-install http://10.59.132.240:9009/target/sonic-vs.bin.signed
discover: Rescue mode detected. No discover stopped.
Info: Attempting http://10.59.132.240:9009/target/sonic-vs.bin.signed ...
Connecting to 10.59.132.240:9009 (10.59.132.240:9009)
installer            100% |*******************************|  1132M  0:00:00 ETA
ONIE: Executing installer: http://10.59.132.240:9009/target/sonic-vs.bin.signed
ONIE: Secure Boot mode is enabled
ONIE: Extracting the image signature ...
ONIE: Removing the image signature and the information block from the installer image ...
ONIE: A signed ONIE Installer Image detected. Installer: /var/tmp/installer, Signature: /var/tmp/installer.sign
ONIE: Verifying the signature using EFI keys ...
DB-0001.der
DB-0002.der
DB-0003.der
KEK-0001.der
MOK-0001.der
PK-0001.der
DBX-0001.der
DBX-0002.der
Failure: Signed ONIE installer image is signed with a revoked key.
Failure: Signature verification of /var/tmp/installer failed
ONIE:/ #
```



*Installing an Enterprise SONiC image using a valid signed image*

```
ONIE:/ # onie-nos-install http://10.59.132.240:9009/target/sonic-vs.bin.signed
discover: Rescue mode detected. No discover stopped.
Info: Attempting http://10.59.132.240:9009/target/sonic-vs.bin.signed ...
Connecting to 10.59.132.240:9009 (10.59.132.240:9009)
installer            100% |*******************************|  1132M  0:00:00 ETA
ONIE: Executing installer: http://10.59.132.240:9009/target/sonic-vs.bin.signed
ONIE: Secure Boot mode is enabled
ONIE: Extracting the image signature ...
ONIE: Removing the image signature and the information block from the installer image ...
ONIE: A signed ONIE Installer Image detected. Installer: /var/tmp/installer, Signature: /var/tmp/installer.sign
ONIE: Verifying the signature using EFI keys ...
DB-0001.der
DB-0002.der
DB-0003.der
KEK-0001.der
MOK-0001.der
PK-0001.der
DBX-0001.der
DBX-0002.der
ONIE: Signed ONIE installer image verification successfull
ONIE: Signature verification of /var/tmp/installer is successful
Verifying image checksum ...

<SNIP>
Switch CPU vendor is: GenuineIntel
[  868.485547] ext4 filesystem being mounted at /tmp/tmp.2ze7P0 supports timestamps until 2038 (0x7fffffff)
Installed SONiC base image SONiC-OS successfully
ONIE: NOS install successful: http://10.59.132.240:9009/target/sonic-vs.bin.signed
ONIE: Rebooting...
ONIE:/ # discover: Rescue mode detected. No discover stopped.
Stopping: dropbear ssh daemon... done.
```



When secure boot mode is not active, ONIE allows both signed and unsigned installer images. The signature if present is ignored.

# User Interface

### Data Models

```
module: openconfig-platform
  +--rw components
     +--rw component* [name]
<SNIP>
        +--rw software
<SNIP>        
        |  +--rw secure-boot
        |     +--ro state
        |        +--ro supported?   boolean
        |        +--ro status?      enumeration
        +--rw properties
        |  +--rw property* [name]
        |     +--rw name      -> ../config/name
<SNIP>        
```



### REST URIs

```
/restconf/data/openconfig-platform:components/component=Software/software/secure-boot
/restconf/data/openconfig-platform:components/component=Software/software/secure-boot/state
/restconf/data/openconfig-platform:components/component=Software/software/secure-boot/state/status
/restconf/data/openconfig-platform:components/component=Software/software/secure-boot/state/supported
```



###  CLI

**show platform sbstatus**
Use this command to display the current secure boot status.

**Format**	show platform sbstatus
**Mode**	EXEC Mode

```
admin@sonic:~$ show platform sbstatus
SecureBoot is Enabled
```



```
sonic# show platform sbstatus
SecureBoot is not supported on this system
sonic#
```




## Supported Platforms

The enhancements proposed to SONiC and ONIE are not platform specific. However if a decision is made to support secure boot on a certain platform the following components need to be implemented which are out of scope of this document.
 - UEFI enabled BIOS with Secure Boot support
 - ONIE with Secure Boot support
 - Install Keys used to verify the software installed on the system

For the Enterprise SONIC 4.0.0 release the following platforms are used for a demo version of secure boot in Enterprise SONiC
- Edgecore AS7816
- Edgecore AS9716
- Edgecore AS7326
- sonic-vs



# Unit Tests 

| S.No | Test Description                                             |
| ---- | ------------------------------------------------------------ |
| 1    | SONiC installer loads successfully if the verification is successful using sonic-installer |
| 2    | SONiC installer loads successfully if the verification is successful via onie-nos-installer |
| 3    | If the SONiC installer verification fails in SONiC, image installation fails |
| 4    | If the SONiC installer verification fails in ONIE, image installation fails |
| 5    | If the SONiC grub is not signed with a valid key, bootup should fail via onie-nos-installer |
| 6    | If the SONiC kernel image is signed with an invalid key, then bootup should fail via sonic-installer |
| 7    | If the SONiC kernel image is signed with an invalid key, then bootup should fail via onie-nos-installer |
| 8    | System should boot successfully in case the verification succeeds at every stage via sonic-installer |
| 9    | System should boot successfully in case the verification succeeds at every stage via onie-nos-installer |
| 10   | In case an unsigned SONiC installer is loaded in secure boot enabled system via sonic-install, the load should fail |
| 11   | In case an unsigned SONiC installer is loaded in secure boot enabled system via onie-nos-install, the load should fail |
| 12   | If a signed SONIC installer is loaded via sonic-installer on a system where secure boot is not enabled, it should load successfully |
| 13   | If a signed SONIC installer is loaded via onie-nos-installer on a system where secure boot is not enabled, it should load successfully |
| 14   | If secure boot is enabled in BIOS and ONIE loaded has SHIM signed with invalid key then there should be a security violation |
| 15   | Testing various combinations of Secure Boot state stored on the system using CLICK and KLISH APIs |
| 16   | Testing image extraction of the 'secure-sonic' tool using various combinations of arguments |
| 17   | Testing image creation of the 'secure-sonic' tool using various combinations of arguments |
| 18   | Test signing of images using 'secure-sonic' tool using various combinations of arguments |
| 19   | Testing '--sign-all' option of the tool 'secure-sonic' using various combinations of the arguments and keys |
| 20   | Testing '--verify' command of the 'secure-sonic' tool using various combinations of arguments |
| 21   | Testing '--strip' command of 'secure-sonic' tool using various combinations |



# Appendix

## Implementation Notes

The Secure boot process involves signed images and modules to verify the other signed images. Primarily, a secure boot enabled ONIE needs to be generated for the required platform. The steps to generate an ONIE image are outlined [here](https://opencomputeproject.github.io/onie/design-spec/x86_uefi.html#uefi-x86-secure-boot).

The below are a few notes regarding Secure Boot which are also applicable for Enterprise SONiC:
 - Trusted software supplier's public certificate db_cert.der/db_cert.pem (or Microsoft's CA ceritficate) is assumed to be embedded inside the UEFI DB
 - ONIE software vendor's public key certificate (ONIE_VENDOR_CERT_DER) is embedded inside the shim
 - The shim is then signed with a trusted software supplier's **db** private key (or Microsoft key). Referred to as the db.key
 - Grub and Linux kernel are signed with either ONIE_VENDOR_SECRET_KEY or the db.key so that shim can verify them before executing
 - Standard Linux signing tools like *sbsign* can be used to sign the images
 - NOS installer image should be signed with a nos.key and the public key certificate of this nos.key should be accessible to ONIE for verification purpose

> NOTE: X509 public key certificate can be in DER or PEM formats. DER is 'binary' format for the data structures whereas PEM is the base64 'ascii' representation of DER. The content of the certificate is the same in either case, just the data format differs.

 - In the scenario where  different keys are used for signing shim ONIE and NOS, the UEFI DB should hold multiple public key certificates which can be used to validate all these components
 - A single key can be used to sign the shim, ONIE-grub, ONIE-kernel, NOS-installer, NOS-grub and NOS-kernel images. In this case the key management becomes simpler
 - If the key gets compromised or revoked, it can be added to the UEFI's revoked key database (dbx)
 - The following parameters can be followed for generating the cryptographic keys  used to sign and validate images in the secure boot environment

```
Key: 2048bit RSA
Hashing Algorithm: SHA-256
Signature Algorithm: SHA-256 with RSA encryption
Stored Signature Format: DER (binary)
Public Certificate: X509 v3
```
