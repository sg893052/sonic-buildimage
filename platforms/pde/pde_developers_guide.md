# Document Name
SONiC Platform Development Environment (PDE) Developers Guide
#### Rev 2.2

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  * [Introduction](#introduction)
  * [SONIC Overview](#sonic-overview)
  * [Broadcom SONiC PDE](#broadcom-sonic-pde)
  * [Unit Testing](#unit-testing)    

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision

| Revision | Date       | Author        | Change Description                                           |
| -------- | ---------- | ------------- | ------------------------------------------------------------ |
| 2.2      | 01/05/2020 | Bill Schwartz | Updated document for new Buzznik PDE features.               |
| 2.1      | 08/24/2019 | Bill Schwartz | Added platform and silicon test harness sections with detail on each module. |
| 2.0      | 06/19/2019 | Bill Schwartz | Initial version.                                             |



# About this Manual


# Scope
This document describes the high level design details on how the SONiC PDE is constructed as well as details on the PDE test suite. The PDE is available to ODMs and others looking to add new platform support, and it optimizes the development and qualification process. It offers a pre-canned, minimal code package to which the ODM can add their necessary platform driver files and static configuration files (required by SONiC to properly initialize SAI and the switching silicon). Furthermore, the PDE will provide a test suite where platform developers can quickly test their drivers and configuration files to resolve issues more easily without relying on the full SONiC application and infrastructure to be in place.


# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                           |
|--------------------------|---------------------------------------|
| ODM                      | Original Design Manufacturer          |
| PDE                      | Platform Development Environment      |
| PDK                      | Platform Development Kit              |
| PDDF                     | Platform Driver Development Framework |

# Introduction

Over the past few years the popularity and interest for using Open Compute SONiC (Software for Open Networking in the Cloud) on datacenter switches has increased. Broadcom contributes to the Switch Abstraction Interface (SAI) for devices using Broadcom switching silicon running SONiC, and various SONiC application components. Broadcom simplifies the SONiC platform porting and qualification process through the Platform Development Environment (PDE). This document outlines the PDE, and the Broadcom SONiC certification process.

# SONiC Overview

This document is not intended to replace the existing SONiC online documentation (<https://azure.github.io/SONiC/>). As SONiC is an Open Compute project with active development occurring across many different contributors and companies, architecture changes and feature changes occur frequently. All efforts are made to keep technical details in this document synchronized with SONiC documentation and code, so please notify your Broadcom ODM team lead member if any discrepancies are noticed.

# Broadcom SONiC PDE 

The SONiC build process is a time and hardware intensive process that makes new platform enablement and active development a cumbersome effort. Furthermore, debugging in the SONiC run-time environment can be challenging. For these reasons, Broadcom created a Platform Development Environment (PDE) which provides an optimized build producing a minimal SONiC image with only the PDE container. The end result is a Debian based OS with the official SONiC Debian kernel, automated PDE platform test harness and automated PDE SAI test suite.

## PDE Layout 

The Broadcom SONiC PDE is available in two forms: part of a full SONiC image generated using the standard SONiC build process; or as an optimized repository generated using "make initpde" after cloning the repository.  The PDE has the same directory structure as the publicly available SONiC. The advantage of this approach is a streamlined platform developer environment which seamlessly integrates into SONiC, and enables platform testing in a production deployment.

The PDE directory structure in the SONiC hierarchy is described below.

### sonic-buildimage repository (https://github.com/Azure/sonic-buildimage)

| Directory/File                                               | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| rules/docker-pde.mk                                          | Makefile for PDE docker.                                     |
| rules/sonic-pde-tests.mk                                     | Makefile for PDE pytests.                                    |
| dockers/docker-pde                                           | This directory contains all the related docker initialization base image scripts. |
| device/$BCM_SONIC_ODM  device/$BCM_SONIC_ODM/$BCM_SONIC_PLATFORM device/$BCM_SONIC_ODM/$BCM_SONIC_PLATFORM/$BCM_SONIC_HWSKU | The device directory contains the ODM platform and HWSKU specific configuration and python scripts necessary to support various SONiC features. This directory is installed through ONIE on the SONiC PDE file system to support the existing SONiC python scripts. |
| platform/broadcom/sonic-platform-modules-$BCM_SONIC_ODM platform/broadcom/sonic-platform-modules-$BCM_SONIC_ODM/$PDE_PLATFORM_MODULE_NAME | The platform directory contains the ODM platform drivers required to support the target platform in the SONiC runtime operating system. For example, CPLD drivers might be needed for transceiver EEPROM access or LPMODE control. The PDE build process generates a Debian package with these drivers and installation scripts, which are installed during the ONIE install. |







### sonic-platform-pde-pde repository (https://github.com/Azure/sonic-platform-pdk-pde)

| Directory/File                         | Description                                                  |
| -------------------------------------- | ------------------------------------------------------------ |
| src/creat_pde_platform.sh              | Script to automatically generate a new platform              |
| src/sonic-platform-pde/debian          | Directory containing the makefiles and packaging configuration for the PDE |
| src/sonic-platform-pde/sonic-odm-diag  | Directory containing the ODM specific diagnostics they wish to be included in the SONiC PDE |
| src/sonic-platform-pde/sonic-pde-tests | Directory containing the platform pytests required for platform certification and testing |
| src/sonic-platform-pde/sonic-pde-tools | Directory containing the PDE development tools (pre-emhasis tuning, etc) |
| src/sonic-platform-pde/template        | Directory contains template files for the creat_pde_platform.sh script |




## Build server requirements

The following minimum hardware and software packages are required for compiling the Broadcom SONiC PDE. For information and requirements on building the full SONiC build, please consult the SONiC Wiki ([https://github.com/Azure/SONiC/wiki­](https://github.com/Azure/SONiC/wiki%1f))

Hardware:

4GB RAM minimum

Multi-core CPU

100GB hard drive (** recommended for full SONiC build)

Software:

Ubuntu 16 / Debian 9 system or virtual machine

### Setting up the build environment

```
$ sudo apt-get update
$ sudo apt-get -y install ssh build-essential manpages-dev automake libtool \
  ccache cscope vim git gitweb flex bison texinfo gettext cifs-utils \
  doxygen wget swig openssl p7zip-full libncurses-dev libelf-dev \
  libdwarf-dev zlib1g-dev libssl-dev libc6-dev-i386 perl gawk debhelper \
  equivs tftp tftpd openbsd-inetd docker.io
$ sudo usermod -a -G docker $USER

```

To verify your docker installation, please follow the instructions below:

1. Log out and then log in to ensure the user/group privilege updates have been activated

2. Run the docker hello-world

   ```
   $ docker run hello-world
   ```

   

Documentation for the platform test harness is generated automatically using sphinx. The following dependencies are required.

```
$sudo apt-get install python3-sphinx
```

for python 3 or

```
$sudo apt-get install python-sphinx
```



## Adding a new platform

Prior to any development work, the Broadcom development team populates the ODM git repository with an initial PDE, which is extracted from the sonic-buildimage repository.

### Auto-generating a new platform

Broadcom has created a script "creat_pde_platform.sh" which is packaged with the PDE which will aide in quick platform addition to the SONiC build.  When executed, this script will automatically modify the platform module/ONIE makefiles and create ODM device/platform directories based on templates provided in the PDE.

```
Usage: ./creat_pde_platform.sh ODMFAMILY=<ODM family name> PLATFORM=<platform name> DEVICE=<ONIE Platform name>


```



### Porting Guide

The information below is intended to expand on the existing online SONiC documentation. Always refer to the below link for the latest information on porting platforms.

<https://github.com/Azure/SONiC/wiki/Porting-Guide>

### Platform Drivers

SONiC architecture relies on the linux sysfs file system to communicate between the higher level device drivers to expose things like transceiver EEPROMs, GPIOs, and LEDs that typically are accessible via I2C, FPGA/CPLD, etc. These drivers are compiled during the SONiC / PDE build process and packaging process and installed during the ONIE installation.

Broadcom also provides the Platform Driver Development Framework (PDDF) for a data driven driver model. See the PDDF document for more details and usage.

### Common Device-Specific File Details

All configuration files that are specific to a particular platform or hardware SKU are located under the /device subdirectory and organized by VENDOR. Under each VENDOR directory, there should be at least one platform directory where the directory name **MUST** match the ONIE platform name.

#### Hardware Platform Files 

| File              | Required | Description                                                  |
| ----------------- | -------- | ------------------------------------------------------------ |
| default_sku       | Yes      | This file declares the hardware SKU that should be loaded during the install as well as specifying a SONiC run-time template. |
| installer.conf    | Yes      | Provides the serial UART information required for console redirection by grub and the linux kernel. |
| led_proc_init.soc | Yes      | This file loads the Broadcom LED Microprocessor code used to provide activity and link support when the switch is operational. |
| sensors.conf      | No       | This file is used by lm_sensors package / sensord daemon and defines the platform environmental sensors (temperature sensors, fans, etc). Format is specified in the sensors.conf [documentation.](https://linux.die.net/man/5/sensors.conf) |
| fancontrol        | No       | Along with the lm_sensor package, this package calculates fan speeds and set the fan speeds to the corresponding values. More information can be found in the fancontrol [documentation](https://linux.die.net/man/8/fancontrol). |
| plugins/          | No*      | This directory contains the legacy platform API 1.0 plugins for the PSU, eeprom, and SFPs.   These plugins are not required if platform2.0 APIs are supported. |
| sonic_platform/   | Yes*     | This directory contains the platform API 2.0 plugins.  Starting with the Buzznik Broadcom release, all new platforms should provide this level of API support.  For more information on these plugs, see [SONiC PR 285](https://github.com/Azure/SONiC/pull/285). |



#### Hardware SKU Files

port_config.ini -> Configuration file providing the interface name, lanes, alias, index and speed of each port supported in this configuration. Online documentation regarding this file is limited however an example of two port entries can be found below.



Sample port_config.ini (4 ports only)

```
# name lanes alias index speed

Ethernet0 64,66,67,68 Eth1 1 100000

Ethernet4 69,70,71,72 Eth2 2 100000

<skip ports to show lane map assignments>

Ethernet80 1,2,3,4 Eth21 21 100000

Ethernet84 5,6,7,8 Eth22 22 100000
```



sai.profile -> Configuration file and path which provides SONiC and the PDE information on which config.bcm should be loaded during SAI initialization for the particular HWSKU.

<SWITCH_SILICON_PORT_CONFIG>.config.bcm -> This file is loaded during SAI switch initialization and configures the switching silicon.

Broadcom requires the naming to follow the below convention to easily identify the silicon configuration:

<chip_family><platform_signature>.config.bcm  

So for example, sai.profile and the config.bcm would look like the following for a TD3 system named "pdepoc" with 48 25GB ports and 8 100GB ports:

```
$cat sai.profile 
SAI_INIT_CONFIG_FILE=/usr/share/sonic/hwsku/td3-pdepoc-48x25G+8x100G.config.bcm

```



## Building the PDE

Broadcom made several changes to the SONiC build process to reduce build time for developers. As a result of these changes, some configuration changes need to be made to support the PDE build environment on a new system.

Tthe below steps need to be performed to build the PDE ONIE install binary:

1)  Modify rules/config
    

Point DEFAULT_KERNEL_CACHE_PATH ,SONIC_DPKG_CACHE_SOURCE, and LOCAL_SAI_DEBS_PATH to your repository directory (the kern_debcache,dpkg_cache , and sai_debs directories have been included)

```
DEFAULT_KERNEL_PROCURE_METHOD = cache

DEFAULT_KERNEL_CACHE_PATH = /home/$USER/repos/odm_feat_odm_hwq/kern_debcache

SONIC_DPKG_CACHE_METHOD=cache

SONIC_DPKG_CACHE_SOURCE=/home/$USER/repos/odm_feat_odm_hwq/dpkg_cache

LOCAL_SAI_DEBS_PATH=/home/$USER/repos/odm_feat_odm_hwq/sai_debs/
```

Ensure ENABLE_PDE=y is uncommented

```
# ENABLE_PDE - Enable platform development environment
ENABLE_PDE = y 
```



Now issue the following commands to build the PDE.

```
$make initpde
$make configure PLATFORM=broadcom
$make target/sonic-broadcom-pde.bin
```

Note: You will see several error / warning messages as the PDE repository provided consists of multiple nested git repositories. Broadcom internal makefile changes attempt to look at these repositories but will fail as they have all been locally copied rather than cloned in the PDE. For a full example build output, please see section 8.3

The output ONIE file (target/sonic-broadcom-pde.bin) is used for installation.

## Installing the PDE ONIE image

Install the generated file "sonic-broadcom-pde.bin" on the switch with ONIE using http, tftp, or local USB, if supported. Note the PDE installer is nondestructive. Use the ONIE "Uninstall OS" option to prepare the switch for the PDE install.

## Updating the PDE image after install

If SONiC (either full SONiC install or PDE image) is already installed on the target platform, the ONIE installer is not needed for updating the PDE. The existing "sonic_installer" script can be used to update the PDE image on the target platform.

## PDE Utilities

As part of the PDE, several utilities have been included to ease in platform development and testing.  Each of these utilities are described below.

### Dynamic Pre-emphasis Tuning

In 2019, SONiC incorporated a new feature to support dynamic pre-emphasis programming based on inserted transceiver type [(see SONiC PR 328)](https://github.com/Azure/SONiC/pull/328/files).   In order to assist the ODM in creating the various configuration files required for this support, Broadcom has developed a transceiver tuning utility and incorporated it into the PDE.  User instructions for this utility can be found by looking in the PDE repository source (sonic-pde-tools/xcvr-tuning/xcvr-tuning.md).

### Performance Benchmarking 

During Broadcom SONiC qualification, various issues and defects have been have been seen that are related to platform hardware design (CPU SKU, amount of RAM, disk storage, etc).  For this reason, the PDE has incorporated a popular github utility "nench" for providing benchmark statistics for the platform.  This data is useful in determining and comparing the performance of the platform versus other SONiC supported platforms. 

The utility can be launched from the host running as root as the example below shows.  For more information on this tool, please visit the [nench github site.](https://git.io/nench.sh)



```
## root@sonic:/home/admin# pde-bench 

 nench.sh v2019.07.20 -- https://git.io/nench.sh

##  benchmark timestamp:    2020-01-06 14:38:34 UTC

Processor:    Intel(R) Atom(TM) CPU  C2338  @ 1.74GHz
CPU cores:    2
Frequency:    1750.000 MHz
RAM:          3.8G
Swap:         -
Kernel:       Linux 4.9.0-11-2-amd64 x86_64

Disks:
loop0  386.5M  HDD
loop1      4G  HDD
sda   29.8G  SSD
sdb    1.9G  HDD

CPU: SHA256-hashing 500 MB
    16.864 seconds
CPU: bzip2-compressing 500 MB
    42.569 seconds
CPU: AES-encrypting 500 MB
    6.535 seconds

ioping: seek rate
    min/avg/max/mdev = 65.2 us / 195.3 us / 7.30 ms / 74.8 us
ioping: sequential read speed
    generated 4.17 k requests in 5.00 s, 1.02 GiB, 833 iops, 208.5 MiB/s

dd: sequential write speed
    1st run:    36.81 MiB/s
    2nd run:    36.81 MiB/s
    3rd run:    36.81 MiB/s
    average:    36.81 MiB/s

IPv4 speedtests
    your IPv4:    192.19.231.xxxx

​```
Cachefly CDN:         6.95 MiB/s
Leaseweb (NL):        0.48 MiB/s
Softlayer DAL (US):   29.42 MiB/s
Online.net (FR):      0.00 MiB/s
OVH BHS (CA):         5.34 MiB/s
​```

## No IPv6 connectivity detected

root@sonic:/home/admin# 
```



### SFP I2C Stress Test

An SFP I2C stress test has been included in the PDE docker that will perform multiple reads of populated SFP transceivers (minimum of 2 are required)  and verify the data read is valid.  To launch this test, run "pde-stress" as root on the host.

```
root@sonic:/home/admin# pde-stress 
Initiating 180 seconds SFP I2C stress test...
########################################################################################################################################################################################################################################################################################################################################################################################################################
PASS
root@sonic:/home/admin

```



### Port Breakout

While dynamic port breakout (port flexing) is not supported in SONiC version 201904, customers are expecting to be able to customize the port configuration of a switch. Rather than require manual modification of the port_config.ini, Broadcom developed a tool that automatically reconfigures the port_config.ini based on user input. The system must be rebooted in order for these changes to take effect.

For example, the below entries of a sample port_config.ini show 8 100GB ports. Using the utility packaged in the host OS (/usr/local/bin/port_breakout.py), these ports can be expanded to 4x10 or 4x25gb.

Please note this is an experimental tool and not yet fully supported by Broadcom.   

Before breakout:

```
Ethernet48 97,98,99,100 hundredGigE49 48 100000 100000,40000

Ethernet52 105,106,107,108 hundredGigE50 49 100000 100000,40000

Ethernet56 113,114,115,116 hundredGigE51 50 100000 100000,40000

Ethernet60 121,122,123,124 hundredGigE52 51 100000 100000,40000

Ethernet64 77,78,79,80 hundredGigE53 52 100000 100000,40000

Ethernet68 65,66,67,68 hundredGigE54 53 100000 100000,40000

Ethernet72 69,70,71,72 hundredGigE55 54 100000 100000,40000

Ethernet76 125,126,127,128 hundredGigE56 55 100000 100000,40000
```

After breakout:

```
root@sonic:/home/admin# port_breakout.py -d -p Ethernet48 -o 4x10

Breaking port Ethernet48 to 4x10 in ini ...

Ethernet48 97 tenGigE49:1 48 10000 10000

Ethernet49 98 tenGigE49:2 48 10000 10000

Ethernet50 99 tenGigE49:3 48 10000 10000

Ethernet51 100 tenGigE49:4 48 10000 10000

Ethernet52 105,106,107,108 hundredGigE50 49 100000 100000,40000

Ethernet56 113,114,115,116 hundredGigE51 50 100000 100000,40000

Ethernet60 121,122,123,124 hundredGigE52 51 100000 100000,40000

Ethernet64 77,78,79,80 hundredGigE53 52 100000 100000,40000

Ethernet68 65,66,67,68 hundredGigE54 53 100000 100000,40000

Ethernet72 69,70,71,72 hundredGigE55 54 100000 100000,40000

Ethernet76 125,126,127,128 hundredGigE56 55 100000 100000,40000
```

Please note, the config.bcm file must be configured so that these ports can be expanded properly. Please work with your Broadcom project lead if you need assistance modifying the config.bcm file for port breakout.

Port breakout is not currently supported as an automated test suite and is covered in the manual test documentation. ODMs must ensure that all supported breakout ports (e.g., 100GB and 400GB) can be expanded and pass traffic properly.


## Auto generating documentation

To generate html documentation for test cases:

```
$cd src/sonic-platform-pde/pde-test-host/docs

$make html
```

HTML output is located at “build/html/index.html”. Each test module has a description for each test case supported as well as an example of the json key/value pairs that are used for each test. See JSON configuration files below.

## Vendor Diagnostics 

It is highly encouraged that ODMs use the SONiC PDE infrastructure to run custom platform diagnostic utilities, provide firmware update applications, etc. While the current PDE infrastructure does not support automated install of custom utilities, customers can take advantage of the PDE docker and install custom applications as needed. It is strongly encouraged that all utilities and scripts are contained in a Debian package for easy distribution and installation. Please work with the Broadcom platform team lead if you wish to include any custom applications in the PDE.

# Unit Testing

Unit tests in the PDE execute in isolation from a majority of the SONiC application containers. The PDE repository explicitly excludes the SONiC containers. When the PDE is run as part of a full SONiC build, the PDE container disables conflicting SONiC containers.

Unit testing consists of two components: automated platform tests in the PDE platform test harness and manual tests and are described in more detail below and produce a test report. The manual tests are documented in a spreadsheet.

## Platform Test Harness

The platform test harness is a suite of pytest modules which will test various aspects of the platform.  For more information on pytest, please see [pytest online documenation.](https://docs.pytest.org/en/latest/)

Below is a table of current test modules with a few test examples For each module.  For full test documentation, please refer to the source code or the auto-generated sphinx documentation.

| Filename         | Test examples:                                               |
| ---------------- | ------------------------------------------------------------ |
| test_bmc.py      | Tests that the BMC is present and passes self test.          |
| test_config.py   | Tests the platform config.bcm file for errors and required settings for Broadcom silicon. |
| test_cpld.py     | Number of CPLDs described match what is present in hardware, tests CPLD read / write capability |
| test_eeprom.py   | Tests that the system EEPROM contains a valid MAC and serial number. |
| test_fan.py      | Tests to ensure the fan plugin is reporting the proper number and state of each fan. |
| test_leds.py     | Tests front port panel activity and links status leds as well as other front panel LEDs as applicable. |
| test_platform.py | Tests the various supported platform drivers and ensure they can be  unloaded and loaded without error. |
| test_port.py     | Placeholder for any front panel port tests.                  |
| test_psu.py      | Test number and state of PSUs supported by plugin match system configuration. |
| test_rtc.py      | Tests that the RTC can be accessed and set.                  |
| test_sai.py      | Tests the front panel ports to ensure that they can pass traffic by making SAI calls to initialize the switch and ports. |
| test_sfp.py      | Test number of SFPs supported by plugin match system configuration, tests SFP present. |
| test_thermal.py  | Tests the number of temperature sensors match the system configuration and that temperatures can be retrieved for each. |
| test_usb.py      | Test that USB device can be mounted, accessed.               |

All the tests can be executed by running the following command from the host:

```
root@sonic:/home/admin# pde-test 
usage: /usr/local/bin/pde-test <help/list/commands>
```

```
root@sonic:/home/admin# pde-test list
__init__.py	 data	    test_bmc.py     test_os.py	      test_sfp.py
__init__.pyc	 debug	    test_config.py  test_platform.py  test_thermal.py
__pycache__	 nvram	    test_cpld.py    test_port.py      test_usb.py
_saiut.so	 sai-shell  test_eeprom.py  test_psu.py
brcm_bcm_scache  saiut.py   test_fan.py     test_rtc.py
conftest.py	 saiut.pyc  test_leds.py    test_sai.py
root@sonic:/home/admin# pde-test list -v test_psu.py
__init__.py	 data	    test_bmc.py     test_os.py	      test_sfp.py
__init__.pyc	 debug	    test_config.py  test_platform.py  test_thermal.py
__pycache__	 nvram	    test_cpld.py    test_port.py      test_usb.py
_saiut.so	 sai-shell  test_eeprom.py  test_psu.py
brcm_bcm_scache  saiut.py   test_fan.py     test_rtc.py
conftest.py	 saiut.pyc  test_leds.py    test_sai.py
root@sonic:/home/admin# pde-test 
```

```
root@sonic:/home/admin# pde-test -v test_psu.py
============================= test session starts ==============================
platform linux2 -- Python 2.7.13, pytest-4.6.9, py-1.8.1, pluggy-0.13.1 -- /usr/bin/python
cachedir: .pytest_cache
rootdir: /usr/local/sonic_pde_tests
collected 9 items                                                              

test_psu.py::test_for_num_psus PASSED                                    [ 11%]
test_psu.py::test_for_psu_present PASSED                                 [ 22%]
test_psu.py::test_for_psu_notpresent PASSED                              [ 33%]
test_psu.py::test_for_psu_status PASSED                                  [ 44%]
test_psu.py::test_for_psu_serial_num SKIPPED                             [ 55%]
test_psu.py::test_for_psu_model SKIPPED                                  [ 66%]
test_psu.py::test_for_psu_voltage SKIPPED                                [ 77%]
test_psu.py::test_for_psu_current SKIPPED                                [ 88%]
test_psu.py::test_for_psu_power SKIPPED                                  [100%]

===================== 4 passed, 5 skipped in 51.44 seconds =====================
root@sonic:/home/admin# 
```




## JSON Configuration Files

The platform test harness relies on two configuration files (test-<sonic_platform>-config.json and platform-<sonic_platform>-config.json. These files are used by the pytest modules to test the SONiC platform plugins and drivers against what is supported / present on the switch. 

Template files are located in following directory.

If using on a switch:

/usr/lib/python2.7/dist-packages/sonic_pde_tests/data/templates

If using source code:

src/sonic-platform-pde/sonic-pde-tests/sonic_pde_tests/data/template/



For a new platform, the ODM developer should take the existing template files and rename/create a copy to the following locations.

If using on a switch:

/usr/lib/python2.7/dist-packages/sonic_pde_tests/data/platform/test-<sonic_platform>-config.json

/usr/lib/python2.7/dist-packages/sonic_pde_tests/data/platform/platform-<sonic_platform>-config.json

If using source code:

src/sonic-platform-pde/sonic-pde-tests/sonic_pde_tests/data/platform/test-<sonic_platform>-config.json

src/sonic-platform-pde/sonic-pde-tests/sonic_pde_tests/data/platform/platform-<sonic_platform>-config.json

When the PDE tests are executed, they will check to see if a platform-config.json or test-config.json file is located in the "/home/pde" directory of the PDE container. If these files are present they will be used for the pytest test functions. If these files do not exist, then the json files are expected to be in the data/platform” directory referenced above.  If the platform specific files are not found in either the "/home/data/" or "src/sonic-platform-pde/sonic-pde-tests/sonic_pde_tests/data/platform/" directories, then new platform and test configuration json files will be created automatically with template values.



### platform-config.json

The platform-config.json file specifies what the platform supports. Below is a platform-config.json file for a system that supports 2 PSUs, 3 CPLDs, 1 system EEPROM, 12, fans, etc.

When adding support for a new platform, the developer should take the latest template file update it according to what the new platform supports.    

In each pytest test case, the json data which is required by each test is described in more detail.    For example, in test_psu.py, testcase "test_for_num_psus" will use the "PLATFORM":{"num_psus"} value to determine if the number of PSUs supported by the PSU plugin match.



```
def test_for_num_psus(json_config_data):
    """Test Purpose:  Verify that the numer of PSUs reported as supported by the PSU plugin matches what the platform supports.

Args:
    arg1 (json): platform-<sonic_platform>-config.json

Example:
    For a system that physically supports 2 power supplies

    platform-<sonic_platform>-config.json
    {
        "PLATFORM": {
            "num_psus": 2
        }
    }
    """
assert _wrapper_get_num_psus() == json_config_data['PLATFORM']['num_psus'],"System plugin reports that {} PSUs are supported in platform".format(platform_psuutil.get_num_psus())
```



Example platform-config.json

```
{
  "PLATFORM": {
    "num_psus": 2,
    "num_cplds": 3,
    "num_syseeproms": 1,
    "num_fans": 12,
    "num_ports": 58,
    "num_sfps": 58,
    "num_temps": 6,
    "num_serviceports": 1,
    "serviceport": "eth0",
    "thermal_policy_support": "true",
    "modules" : {
     "FAN": {
        "support": "true"
      },
      "TEMP": {
        "support": "true"
      },
      "CPLD": {
        "support": "false"
      },
      "SYSLED": {
        "support": "false"
      },
      "PSU": {
        "support": "true"
      }
    },
    "drivers": {
      "CPLD": {
        "driver_info": {
          "support": "true",
          "type": "ODM",
          "name": "odm_i2c_cpld"
        }
      },
      "SFP": {
        "driver_info": {
          "support": "false"
        }
      },
      "PSU": {
        "driver_info": {
          "support": "false"
        }
      },
      "FAN": {
        "driver_info": {
          "support": "false"
        }
      },
      "TEMP": {
        "driver_info": {
          "support": "false"
        }
      },
      "SYSLED": {
        "driver_info": {
          "support": "true",
          "type": "ODM",
          "name": "odm_56x_leds"
        }
      },
      "EEPROM": {
        "driver_info": {
          "support": "true",
          "type": "GENERIC",
          "name": "at24"
        }
      },
      "MAC": {
        "driver_info": {
          "support": "true",
          "type": "GENERIC",
          "name": "tg3"
        }
      }
    }
  }
}
```



### test-config.json

The test-config.json file is used by the pytest modules to test against the switch hardware configuration.  In the example below, the switch being tested has 2 PSUs present, PSU 1 is not plugged in, while PSU 2 is plugged in.   



When adding support for a new platform, the developer should take the latest template file ("src/sonic-platform-pde/sonic-pde-tests/sonic_pde_tests/data/template/test-config.json") and copy / update it according to the configuration of the device under test (DUT) to the location ("src/sonic-platform-pde/sonic-pde-tests/sonic_pde_tests/data/platform/platform-$ONIE_PLATFORM_NAME-config.json")



Similar to the platform-config.json, each pytest module will document which fields of the test-config.json are applicable to the test case.  For example, in the module test_sai.py, the test case "test_for_switch_port_traffic" will look at the test-config.json to see which ports are connected.



```
def test_for_switch_port_traffic(json_test_data):
    """
    Test Purpose:  Verify the per port packet transmission

Args:
    arg1 (json): test-<sonic_platform>-config.json

Example:
    For a system with 2 back-to-back self-loops

​    test-<sonic_platform>-config.json
​    {
​        "PLATFORM": {
​            "SELF_LOOPS" : [
​                "Ethernet0:Ethernet24",
​                "Ethernet4:Ethernet28"
​            ]
​        }
​    }
"""
try:
​    lnks = json_test_data['PLATFORM']['SELF_LOOPS']
except KeyError:
​    pytest.skip("test configuration not found")

rv = load_platform_portdict()
assert rv is not None, "Unable to load the port dictionary"

rv = start_switch_core()
assert rv == saiut.SAI_STATUS_SUCCESS, "Unable to initialize the switch"

...
```



Example test-config.json

```
{
  "PLATFORM": {
    "powercontrol": true,
    "PSU": {
      "present": [
        1,
        2
      ],
      "status": [
        false,
        true
      ],
      "PSU2": {
        "mfr_id":"3Y POWER",
        "model":"YM-2651Y",
        "output_voltage":12,
        "psu_serial_num": "SA070V581834001921"
      }
    },
    "SFP": {
      "present": [
        0,
        1,
        2,
        6,
        7,
        45,
        47,
        64,
        68,
        72,
        76
      ]
    },
    "USB": {
      "enable": "no",
      "device": "sdb",
      "mountpoint": "/media/usb-storage"
    },
    "EEPROM": {
      "mac": "80:a2:35:26:07:5e",
      "ser": "odmserialnumber",
      "model": "odmmodel"
    },
    "CPLD": {
      "CPLD1": {
        "version": "2"
      },
      "CPLD2": {
        "version": "1"
      }
    },
    "FAN": {
      "present": [
        1,
        2,
        3,
        4,
        5,
        6,
        7,
        8,
        9,
        10,
        11,
        12
      ],
      "FAN1": {
        "direction": "exhaust"
      },
      "FAN2": {
        "direction": "exhaust"
      },
      "FAN3": {
        "direction": "exhaust"
      },
      "FAN4": {
        "direction": "exhaust"
      },
      "FAN5": {
        "direction": "exhaust"
      },
      "FAN6": {
        "direction": "exhaust"
      },
      "FAN7": {
        "direction": "exhaust"
      },
      "FAN8": {
        "direction": "exhaust"
      },
      "FAN9": {
        "direction": "exhaust"
      },
      "FAN10": {
        "direction": "exhaust"
      },
      "FAN11": {
        "direction": "exhaust"
      },
      "FAN12": {
        "direction": "exhaust"
      }
    },
    "THERMAL_POLICY":
    {
       "service_name": "odm-platform-monitor",
       "POLICY_NUM":3,
       "F2B":
       {
          "0": [0,39,38],
          "1": [40,44,75],
          "2": [45,100,100]
       },
       "B2F":
       {
          "0": [0,39,38],
          "1": [40,44,75],
          "2": [45,100,100]
       },
       "FAN_REMOVED_DUTY":100
    },
    "MAC":
    {
       "MAC1":{
         "ifname": "eth0",
         "macaddr": "80:a2:35:26:07:5e"
       }
    },
    "SYSLED":
    {
       "FANTRAY":{
           "FANTRAY1":{
              "color":"GREEN",
              "state" : "SOLID"
            },
           "FANTRAY2":{
              "color":"GREEN",
              "state" : "SOLID"
            },
           "FANTRAY3":{
              "color":"GREEN",
              "state" : "SOLID"
            },
           "FANTRAY4":{
              "color":"GREEN",
              "state" : "SOLID"
            }
       },
       "PSU":{
           "PSU1":{
              "color":"GREEN",
              "state" : "SOLID"
           },
           "PSU2":{
              "color":"GREEN",
              "state" : "SOLID"
           }
       },
       "FAN":{
         "color":"GREEN",
         "state" : "SOLID"
       },
       "SYS":{
         "color":"GREEN",
         "state" : "SOLID"
       },
       "LOC":{
         "color":"GREEN",
         "state" : "SOLID"
       }
    },
    "CONFIG":
    {
      "required": {
        "config.bcm": [
          "parity_enable=1"
        ]
      }
    },
    "OS":
    {
      "flooding": {
        "dmesg": [
          "error"
        ],
        "syslog": [
          "i2c",
          "usb",
          "pci"
        ]
      }
    },
    "PMON":
    {
      "syslog": [
        "psud",
        "fand"
      ]
    },
    "SELF_LOOPS": [
      "Ethernet6:Ethernet7"
    ]
  }
}
```



## Manual tests

Manual test cases are provided to the ODM in the form of a XML spreadsheet. Each manual test case is executed with results recorded in the spreadsheet and returned to Broadcom.



