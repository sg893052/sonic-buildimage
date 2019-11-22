# Broadcom SONiC Versioning and Branding

Version 0.5

## Table of Contents

1. [Revision History](#revision-history)
2. [Objectives](#objectives)
3. [SONiC Software Version](#sonic-software-version)
   1. [Build Version String](#build-version-string)
4. [SONiC Branding](#sonic-branding)
   1. [Build Version](#build-version)
   2. [Brand Names](#brand-names)
5. [SONiC Package](#sonic-package)
6. [Mockups](#mockups)

## Revision History

| Version | Date       | Author             | Change Description                                           |
| ------- | ---------- | ------------------ | ------------------------------------------------------------ |
| 0.1     | 9/11/2019  | Rajendra Dendukuri | Initial draft. Used for Arlo Release                         |
| 0.2     | 10/2/2019  | Rajendra Dendukuri | Updated versioning and branding contents for Arlo+ Release   |
| 0.3     | 10/11/2019 | Rajendra Dendukuri | Updated the document with information about SONiC package file |
| 0.4     | 10/14/2019 | Rajendra Dendukuri | Converted document to markdown format                        |
| 0.5     | 11/22/2019 | Rajendra Dendukuri | Modified release version string to be of p.q.r format        |



## Objectives

1.  Uniquely identify the source snapshot used to build an image, product packaging of the image and the build parameters used while compiling the image.
2.  All builds generated as part of release and daily build process must not have "--dirty" flag associated with the version. The dirty flag  negates the golden rule of being able to identify the source  snapshot used to build.
3.  Allow installation of different packages generated from the same source snapshot/release
4.  Add ability to use of Broadcom internal names for generating version string and NOS brand name. Same ability should be extended to customers to easily make changes to the version string and NOS brand name.
5.  Define Broadcom generated version strings which are concise and meaningful.

## SONiC Software Version

The SONIC software version is the full expanded unique version string that is displayed in the following places:

1.  The CLI command “show version”
2.  The CLI command “sonic\_installer list”
3.  GRUB boot menu when the switch is booting

This is used to uniquely identify a firmware image used on a SONiC switch. It is a combination of [*NOS Name*](#nos-name) and  [*Build Version String*](#build-version-string).

*Format:*
&lt;NOS\_Name&gt;-&lt;Build\_Version\_String&gt;

*Example:*
SONiC-OS-2.1.0-Cloud\_Base
SONIC-OS-2.1\_daily\_190911\_0000\_321-Cloud\_Base

### Build Version String

The build version specifies the source snapshot version and the build parameters used to compile the source snapshot. It is also used as a tag for the docker container images.

*Format:*
&lt;Build\_Identifier&gt;\_&lt;version-id&gt;-&lt;product-packaging&gt;-&lt;build-options&gt;
&lt;Build\_Identifier&gt;

* An optional name tag which identifies the source snapshot. It can
indicate the target release or branch name. It can also indicate the
purpose of the build, daily/ci/sanity etc.*

 2.1\_daily (top of the tree)
 null string (release tag prefix)

&lt;version-id&gt;

*A typical version for quick reference.*

 2.1.0 (release)
 2.1.1 (maintenance release)
 2.1.0_RC1 (release candidate)
 \$datetime_$build-number (Daily)

&lt;product-packaging&gt;

 Cloud_Base (sonic-broadcom-cloud-base.bin)
 Cloud_Advanced (sonic-broadcom-cloud-advanced.bin)
 Generic (sonic-broadcom.bin)

&lt;build-options&gt;

 dbg (With debugging on)
 cov (With code coverage on)
 dbg-cov (With debugging, code coverage on)
 null string (if debugging and code coverage is off)

**Sample Build Versions for Arlo+ Release builds:**

2.1.0-Cloud_Base
2.1.0-Cloud_Base-dbg
2.1.0-Cloud_Advanced
2.1.0-Cloud_Advanced-dbg
2.1.0-Generic
2.1.0-Generic-dbg

**Sample Build Versions for Daily builds:**

2.1_daily_190911_0000_321-Cloud_Base
2.1_daily_190911_0000_321-Cloud_Base-dbg
2.1_daily_190911_0000_321-Cloud_Advanced
2.1_daily_190911_0000_321-Cloud_Advanced-dbg
2.1_daily_190911_0000_321-Generic
2.1_daily_190911_0000_321-Generic-dbg

## SONiC Branding

### Build Version

The build version string described the the section [*Build Version String*](#build-version-string) can be passed as an input to the SONiC build infrastructure in the form of a command-line make variable BUILD\_VERSION.

*Example:*

Below command will result in a software version:*
SONiC-OS-2.1.0-Cloud\_Base-dbg*

`make BUILD_VERSION=2.1.0-Cloud_Base-dbg target/sonic-broadcom-cloud-base.bin`

Below command will result in a software version: SONiC-OS-2.1.0\_RC1

`make BUILD_VERSION=2.1.0_RC1 target/sonic-broadcom.bin`

User can also assign the BUILD\_VERSION value as part of the source file *sonic-buildimage/branding.mk*.

The value provided at the command line takes precedence over the value specified in the source file *sonic-buildimage/branding.mk.*

If BUILD\_VERSION is not specified by the user, SONiC generates an internal version using the logic in function *sonic_get_version() *in [*sonic-buildimage/functions.sh*](https://github.com/Azure/sonic-buildimage/blob/master/functions.sh). No change has been made to this default sonic version logic and it continues to be the same as the versioning scheme used in the community version of SONiC.

### Brand Names

#### NOS Name

The NOS name is used to identify the network operating system that is under use. The NOS name is used as a prefix to construct the [*SONiC software version*](#sonic-software-version) string. NOS Name used by an image is also specified in the file */host/image-\${build\_version}/nos\_name*. NOS Name value can be read using the python API *sonic\_device\_util.get\_sonic\_nos\_name()*

The NOS name used in an image can be modified by editing the variable NOS\_NAME in the source file *sonic-buildimage/branding.mk* and rebuilding the image. The default value used is SONiC-OS.

User can also provide the NOS\_NAME value as part of the make command. The value provided at the command line takes precedence over the value specified in the source file *sonic-buildimage/branding.mk.*

Below command will result in a software version:
SmartOS-2.1.0-Cloud\_Base-dbg

`make NOS_NAME=SmartOS BUILD_VERSION=2.1.0-Cloud_Base-dbg target/sonic-broadcom.bin`

#### Product Name

A SONiC software image can be packaged to include different features and capabilities based on the requirements. The product name uniquely identifies the feature set that is supported in a SONiC software image. The product name is a text string that is displayed in the command “show version”. It is also specified in the file */etc/sonic/sonic\_branding.yml* as the field *product\_name*.

The product name of an image can be specified by editing PRODUCT\_NAME variable in the source file *sonic-buildimage/branding.mk* and rebuilding the image. Default value used for PRODUCT\_NAME is “Generic”.

User can also provide the PRODUCT\_NAME value as part of the make command. The value provided at the command line takes precedence over the value specified in the source file *sonic-buildimage/branding.mk.*

Example:

`make PRODUCT_NAME=”Cloud Base” target/sonic-broadcom-cloud-base.bin`

## SONiC Package

A SONiC installer image (e.g sonic-broadcom-cloud-base.bin) is a self extracting firmware file which contains the branding and version information embedded in it. As the SONiC installer image is a single binary file it not possible to change the branding information without unpacking it, modifying it and repacking it. This results in a different binary file compared to the one released by Broadcom.

However, some customers who receive just the binary image from Broadcom would want some flexibility to influence some changes to the SONiC installer file.

This is made possible by creating a SONiC package which is also a self extracting binary image. The SONiC package file can be used to install SONIC image using ONIE and the sonic\_installer command just like the SONiC installer file. SONiC package file is typically named with *.pkg* file extension (e.g sonic-cloud-base.pkg)*.*

A new tool *build\_pkg.sh* is available to create a SONiC package file. The *build\_pkg.sh* is available in the source code as
*sonic-buildimage/build\_pkg.sh.*

Below is an example to create a SONiC package file to include a user provided *sonic\_branding.yml* which contains user defined Product Name.

**Command Usage:**

```
build_pkg.sh target/sonic-broadcom-cloud-base.bin sonic_branding.yml
```

**Example Usage:**

```
bash-4.2$ cat sonic_branding.yml
product_name: SONiC by ACME Corporation Cloud Standard

bash-4.2$ build_pkg.sh target/sonic-broadcom-cloud-base.bin sonic_branding.yml
Creating a package file to combine target/sonic-broadcom-cloud-base.bin and sonic_branding.yml
Successfully created package file: sonic-broadcom-cloud-base.pkg
```
When user installs the sonic-broadcom-cloud-base.pkg, the sonic\_branding.yml in /etc/sonic directory of the image is replaced with the one provided by user and thus *show version* shows the product
name chosen by the user.

It is to be noted that Broadcom always releases SONiC installer files, customers might choose to use the SONiC package file.



## Mockups

**Arlo+ Release build for Cloud Advanced package**

```
admin@sonic:\~\$ show version
SONiC Software Version: SONiC-OS-2.1.0-Cloud_Advanced
Product: Cloud Advanced SONiC OS - Powered by Broadcom
Distribution: Debian 9.9
Kernel: 4.9.0-9-2-amd64
Build commit: 4ef307ee
Build date: Tue Aug 13 08:22:41 UTC 2019
Built by: sonicbld@sonic-lvn-csg-003
Platform: x86_64-accton_as7326_56x-r0
HwSKU: Accton-AS7326-56X
ASIC: broadcom
Serial Number: 732656X1818028
Uptime: 17:51:44 up 35 min, 1 user, load average: 0.44, 0.59, 1.14
Docker images:

REPOSITORY TAG IMAGE ID SIZE

docker-syncd-brcm-cld-advanced latest 1c32fd90d830 477MB
docker-syncd-brcm-cld-advanced 2.1.0-Cloud_Advanced  1c32fd90d8 30477MB
docker-fpm-frr latest 1c32fd90d830 477MB
docker-fpm-frr 2.1.0-Cloud_Advanced 1c32fd90d830 477MB
docker-orchagent latest df15e4f011e5 447MB
docker-orchagent 2.1.0-Cloud_Advanced df15e4f011e5 447MB
docker-broadview latest 1b0e5c4e982c 457MB
<snip>
```

**Daily build for Cloud Base package with Debugging on**

```
SONiC Software Version: SONiC-OS-2.1_daily_190911_0000_321-Cloud_Base-dbg
Product: Cloud Base SONiC OS - Powered by Broadcom
Distribution: Debian 9.11
Kernel: 4.9.0-9-2-amd64
Build commit: ed50d75f
Build date: Wed Sep 11 07:46:30 UTC 2019
Built by: sonicbld@sonic-lvn-csg-004
Platform: x86_64-bcm_xlr-r0
HwSKU: BCM956960K
ASIC: broadcom
Serial Number: No service tag
Uptime: 17:34:59 up 8 min, 1 user, load average: 1.75, 1.83, 1.11
Docker images:

REPOSITORY TAG IMAGE ID SIZE

docker-orchagent-dbg latest 0636e212dc5c 530MB
docker-orchagent-dbg 2.1_daily_190911_0000_321-Cloud_Base-dbg 0636e212dc5c 530MB
docker-orchagent latest 0636e212dc5c 530MB
docker-orchagent 2.1_daily_190911_0000_321-Cloud_Base-dbg 0636e212dc5c 530MB
docker-syncd-brcm-dbg latest 588222c5442c 526MB
docker-syncd-brcm-dbg 2.1_daily_190911_0000_321-Cloud_Base-dbg 588222c5442c 526MB
docker-syncd-brcm latest 588222c5442c 526MB
docker-syncd-brcm 2.1_daily_190911_0000_321-Cloud_Base-dbg 588222c5442c 526MB
<snip>
```
**Daily build for Generic package with Debugging on**

```
SONiC Software Version:
SONiC-OS-2.1_daily_190911_0000_321-Generic-dbg
Product: Generic
Distribution: Debian 9.11
Kernel: 4.9.0-9-2-amd64
Build commit: ed50d75f
Build date: Wed Sep 11 07:46:30 UTC 2019
Built by: sonicbld@sonic-lvn-csg-004
Platform: x86_64-bcm_xlr-r0
HwSKU: BCM956960K
ASIC: broadcom
Serial Number: No service tag
Uptime: 17:34:59 up 8 min, 1 user, load average: 1.75, 1.83, 1.11

Docker images:

REPOSITORY TAG IMAGE ID SIZE

docker-orchagent-dbg latest 0636e212dc5c 530MB
docker-orchagent-dbg 2.1_daily_190911_0000_321-Generic-dbg 0636e212dc5c 530MB
docker-orchagent latest 0636e212dc5c 530MB
docker-orchagent 2.1_daily_190911_0000_321-Generic-dbg 0636e212dc5c 530MB
docker-syncd-brcm-dbg latest 588222c5442c 526MB
docker-syncd-brcm-dbg 2.1_daily_190911_0000_321-Generic-dbg 588222c5442c 526MB
docker-syncd-brcm latest 588222c5442c 526MB
docker-syncd-brcm 2.1_daily_190911_0000_321-Generic-dbg 588222c5442c 526MB
<snip>
```
