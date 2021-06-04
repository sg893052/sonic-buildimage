

# Watchdog Support for SONiC platforms

## High Level Design Document
**Rev 0.1**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Requirements Overview](#requirements-overview)
  * [Functional Requirements](#functional-requirements)
  * [Configuration and Management Requirements](#configuration-and-management-requirements)
  * [Reboot Requirements](#reboot-requirements)
* [Functional Description](#functional-description)
  * [Design Overview](#design-overview)
  * [System Reboot](#system-reboot)
* [CLI](#cli)
* [Unit Test](#unit-test)

# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision

Rev   |   Date   |  Author   | Change Description
:---: | :-----:  | :------:  | :---------
1.0   | 06/06/20 | Kalimuthu | Initial version
2.0   |          |           |  


# About this Manual

This document describes the Hardware Watchdog support for SONiC supported hardware platforms. This feature enables and configures the hardware watchdog functionality on the SONiC platforms.

# Scope

This document describes the high level design details of Hardware watchdog functionality in SONiC platforms.  It also describes the tools and commands used for configuring the Hardware watchdog in ODM hardware devices. This document covers only the Hardware watchdog support in SONiC and the software watchdog functionality is outside the scope of this document. The hardware watchdog is implemented through standard intel PCH/ICH iTCO watchdog timer. Any other hardware watchdog timer and ODM vendor specific watchdog timers are beyond this scope of this document. Also, the BMC based watchdog is independent of this watchdog mechanism.


# Definition/Abbreviation

### Table 1: Abbreviations

| **Term**     |  **Meaning**                  |
|:-------------|:------------------------------|
| WDT  | Watchdog Timer                        |
| PCH  | Platform Controller Hub               |
| ICH  | IO Controller Hub                     |
| TCO  | Total Cost of Ownership               |



# Requirements Overview

Despite extensive software and hardware testing, faults will still occur in real hardware devices. Even momentary noise spikes on a power supply can lock up a processor occasionally. Watchdog timers provide a last line of defense to prevent system failure with minimal hardware cost.

A watchdog timer (WDT) is a hardware timer that automatically generates a system reset if the main program neglects to periodically service it. It is often used to automatically reset an embedded network device that hangs because of a software or hardware fault. 

The Watchdog timer can detect a fault on an unattended SONiC hardware device and attempt corrective action with a reset. Typically after reset, a register can also be read to determine if the watchdog timer generated the reset or if it was a normal reset. 


## Functional Requirements

- Hardware Watchdog feature in SONiC would support ODM platforms if they support HW Watchdog.
- If the system reset is because of watchdog timeout, it should report the proper reboot cause and should get reflected in the 'show reboot-cause' CLI.
- It should support both SONiC 2.0 platform framework and its prior version of API interfaces.
- It should be enabled by default in all the SONiC hardware platforms.
- It should provide a watchdog refresh framework to refresh the watchdog timer interval periodically and view its status.
- During the kernel panic, it should have API/util interface to disable the hardware reset by extending the watchdog timer for collecting the kdump data.
- It should have API/util interface to view the watchdog current counter value and its status.
- Watchdog timer shall be supported for all the variants of intel iTCO PCH/ICH chipset boards.
- It should show the watchdog reboot reason if platform supports reboot reason because of watchdog timeout.


## Configuration and Management Requirements

### Config commands
- The Watchdog support is enabled by default if platform support the this feature. This is a mandatory feature, it should not be disabled at any cause. 
- There is No CLICK/KLISH cli required to enable this feature. 

### Show commands
- The 'show reboot-cause' CLI command should reflect the reboot cause if the device is rebooted because of hardware watchdog timeout.
- The 'show reboot-cause' command reflects the 'Hardware watchdog reset' as reboot reason if platform supports 'watchdog' reboot reason. 

### Util commands
- Linux command utils is provided to list the following:
    1. Shows the status of the watchdog feature. 
    2. Shows the configured per platform timeout value.
    3. Option to enable/disable the watchdog feature, which should be used only for debugging purpose.
 
## Reboot Requirements
- coldboot 
    - During the cold reboot, watchdog timer should not be stopped. The watchdog timeout value should be tuned, so that the timer would be running till BIOS resets it. 
    - The watchdog timer should timeout and reset the system if it is stuck in the middle of reboot. 
- fast-reboot/warm-reboot
    - During the warm/fast reboot, watchdog timer should not be stopped.
    - The timeout value shall be configured long enough to retain the timer countdown until kernel reinitialises device after warm/fast reboot. 
    - The watchdog timer should timeout and reset the system if it is stuck in the middle of warm/fast reboot. 
- Kernel crash
    - It should disable/extend the watchdog timer during kdump collection.


# Functional Description

## Design Overview

- The hardware watchdog designed to use the intel iTCO watchdog driver. This driver is supported on most of the ODM hardware platforms. 
- The watchdog framework consists of following components:

	- Systemd service
	- Watchdog application daemon and utility tools 
	- Watchdog kernel drivers

- These files are preinstalled during the image build itself. When kernel boots up, the iTCO watchdog driver check for previous reboot reason by reading the watchdog status register and update the status according and initialize the watchdog hardware. During the system bootup, it starts the watchdog service as the first service and that starts the watchdog application daemon for watchdog timeout refresh. 



           +--------------------+                        +--------------------+
           |   SYSTEMD MANAGER  |                        |  KLISH/Click CLI   |
           |                    |                        |                    |
           +--------------------+                        +--------------------+
           | watchdog service   |                        |  show reboot-cause |
           +--------------------+                        +--------------------+ 
                    ^                                             ^
                    |                                             |
                    |                                             |
                    |                       +---------------------V--------------------+                      |
                    |                       |                Watchdog Util             |
                    |                       |               (watchdogutil)             |
                    |                       |                                          |
                    |                       +-----------------+---------+--------------+
                    |                       |  enable/disable | counter | reboot cause |
                    |                       +------------------------------------------+
                    |                       |      Local Unix socket Client Interface  |
                    |                       +------^----------+---^-----+------^-------+
                    |                              |              |            | Request/Response
                    |                              |              |            | (JSON)
                    |                              |              |            |
        +-----------V------------------------------V--------------V------------V-------+
        |          Watchdog Daemon                Local Unix Socket Server Interface   |
        |                                                                              |
        |                                  Platform API                                |
        +------------+----------------------------------------+---------+--------------+
        |     Keepalieve/Refresh        |      enable/disable | counter | reboot cause |
        +------------+------------------+---------------------+---------+--------------+
                  |                                  |              ^            ^
                  |              +-------------------+              |            |
        User      |              |                                  |            |
        ----------|--------------|----------------------------------|------------|-------
        HW/Kernel |              |                                  |            |
                  +<------------------------------------------------+            |
                  |              |                                               |
                  |              |                       +-----------------------+
                  |              |                       |
        +---------V--------------V-----------------------V-------+
        | COUNTERS    |    ENABLE/DISABLE    |     REBOOT CAUSE  |
        +------------------+-------------------------------------+      
        |              Hardware Watchdog (iTCO)                  |
        +--------------------------------------------------------+
            |
            V Hardware CPU reset


- The Watchdog daemon application access the driver through generic platform APIs. For earch platform, these API implement the platform level support for accesing the hardware watchdog. For the iTCO based platforms, it accesss the hardware watchdog through device file and ioctl calls. When the iTCO watchdog driver gets initialized in the kernel, it creates the device file '/dev/watchdogX' in /dev path. 

- The watchdog daemon also creates the socket interface for the request from watchdogutil application. This is required because the watchdog device file can be opended only once and it can't be opened from multiple application. So the watchdog daemon open this for the timer keepalive update and also provides the socket interface for watchdog-util. The watchdog-util sends the request to daemon, the daemon processes the request and sends back the results to watchdog-util through local unix socket. The request and response would be in JSON format.  


## Kernel Config

- In order to support the iTCO watchdog, the following kernel CONFIG should be enabled in the kernel. 

		- CONFIG_WATCHDOG_CORE
		- CONFIG_WATCHDOG
		- CONFIG_ITCO_WDT
		- CONFIG_ITCO_VENDOR_SUPPORT
		- CONFIG_ACPI_WATCHDOG

## iTCO variants

- The iTCO supports the following variants. 
	Version : v4, v5
		The reboot cause can be retrieved from the BOOT_STS bit from Status-2 register.

	Version : v3
	Version : v2, v1 and > v5
		Support for WDAT and WDDT are required from BIOS to read the reboot cause. 


	The following configuration are required for version specific iTCO initialization. 
	iTCO_wdt:
		heartbeat: Watchdog heartbeat in seconds.
			(2<heartbeat<39 (TCO v1) or 613 (TCO v2), default=30)
		nowayout: Watchdog cannot be stopped once started
			(default=kernel config parameter)

	iTCO_vendor_support:
		vendorsupport: iTCO vendor specific support mode, default=0 (none),
			1=SuperMicro Pent3, 2=SuperMicro Pent4+, 911=Broken SMI BIOS


## iTCO Chipset Varients
- Hardware driver for the intel TCO timer based watchdog devices. These drivers are included in the Intel 82801 I/O Controller Hub family (from ICH0 up to ICH10) and in the Intel 63xxESB controller hub.

- On some boards, the driver may fail to reset the chipset's NO_REBOOT flag which prevents the watchdog from rebooting the machine. If this is the case you will get a kernel message like "failed to reset NO_REBOOT flag, reboot disabled by hardware".


## ACPI watchdog migration

- Starting from Intel Skylake the iTCO watchdog timer registers were moved to reside in the same register space with SMBus host controller.  Not all needed registers are available and we need to unhide P2SB (Primary to Sideband) device briefly to be able to read status of required NO_REBOOT bit. The i2c-i801.c SMBus driver used to handle this and creation of the iTCO watchdog platform device.

- It relies on ACPI Watchdog Action Table (WDAT) table to describe the watchdog hardware to the OS. This table contains necessary information about the hardware and also set of actions which are executed by a driver as needed.

- This is required when NO_REBOOT flag is not accessible directly. 


## Reboot reason:

- The iTCO driver is patched to enable access to reboot status from the user space. This enables the driver to provide uniform interface to the application for reading the reboot status. 
- As part of platform API implementation, the get_reboot_reasion() API in the chassis module uses this kernel interface to read the reboot reason from watchdo driver.  

- The following command is used for reading the last reboot reason from hardware.

	# sysctl kernel.reboot_reason

- For non iTCO based platforms, respective platform API should be implemented to update reboot reason. 

- The following table shows the platform list with reboot reason support. 

      :----------------|:------------------------
      |    PLATFORM    | Reboot Reason Supported |          
      :----------------|:------------------------
      |  AS7712_32X    | No                      |
      |  AS9716_32D    | No                      |
      |  AS5712_54X    | No                      |
      |  AS4630_54PE   | Yes                     |
      |  AS5835_54X    | Yes                     |
      |  QUANTA_IX4    | No                      |
      |  QUANTA_IX7    | No                      |
      |  QUANTA_IX8    | No                      |
      |  QUANTA_IX9    | No                      |
      :----------------|:-------------------------



## BMC implementation

- BMC, also known as Baseboard Management Controller, is a background controller that is integrated on the motherboard and independent of the CPU and memory. The system can be booted, shut down, etc. through the BMC. At present, the usual practice is to use the ipmi tool to send commands to the BMC to achieve the purpose of obtaining system information. For example, get CPU temperature, status, Power on, Power off system.

- BMC is the hardware device on the motherboard, so it does not know what operating system is installed on the host. It can only boot, shut down, send interrupt information, and can not directly start the system panic.

- BMC implementation is completely independent of this watchdog mechanism.


## 2.0 Platform driver interface

- SONiC defines Platform APIs, and SONIC 2.0 APIs include support for Watchdog APIs. The HW watchdog feature shall make use of the platform 2.0 API and implements the per platform watchdog feature if supported by platform. 
- The 2.0 platform driver interface supports the following watchdog APIs:
    1. arm()
        - This API enables the Hardware watchdog timer and trigger the watchdog timer to start the count down.

    2. disarm()
        - Stops the count down and disable the watchdog function.

    3. is_armed()
        - Returns the current state the WD function whether it is armed or disarmed.

    4. timeout()
        - This API returns the currently configured WD timeout value which is specific to the hardware platform.  
    
    The default value(180s) will be defined in the base class and it can be overridden by the per platform derived class.

## Non 2.0 Platform driver interface
- For the platforms which do not support 2.0 APIs, a generic API implementation shall be provided to use iTCO driver if supported. 

## Non iTCO based hardware
- For the non iTCO based hardware, respective driver will implement the watchdog functionality.

## Kdump with watchdog

- When the kernel is crashed with panic, the watchdog timer shall be disabled/extended long enough to take the kernel kdump data. 
- If the system is stuck in panic data collection, the watchdog automatically reboot the system. 


## System Reboot

### Coldboot
- coldboot - during the cold reboot, watchdog timer should not be stopped. The watchdog timeout value should be tuned, so that the timer would be running  till BIOS resets it. 

### Warmboot/Fastboot
- fast-reboot/warm-reboot - the timeout value shall be configured long enough to retain the timer countdown until the kernel reinitialises value after reboot. 

## Supported Platforms:
- The hardware watchdog feature is targeted for the following platforms for the current release. 

- Platforms with iTCO based watchdog driver support:
	ACCTON:

      :---------------------------------------------------
      | AS7712_32X                                        |
      | AS9716_32D                                        |
      | AS5712-54X                                        |
      | AS4630_54PE                                       |
      | AS5835_54X                                        |
      |:---------------------------------------------------

	QUANTA:

      :---------------
	  | IX4-64X       |
	  | IX7-BWDE-32X  |
	  | IX8-56X       |
	  | IX9-32X       |
      |:--------------

- Platforms with FPGA(non-iTCO)based watchdog driver support:
	DELL:

       :--------
	   | 9264  |
	   | 9332  |
	   | 5232  |
	   | 5248  |
       |:-------

### Watchdog support platform matrix
- The following are the platform matrix with iTCO watchdog timer support

      :----------------|:--------------------|:------------------------
      |    PLATFORM    |  Watchdog supported | Reboot Reason Supported |          
      :----------------|:--------------------|:------------------------
      |  AS7326_56X    | No                  | No                      |
      |  AS7816_64X    | No                  | No                      |
      |  AS7712_32X    | Yes                 | No                      |
      |  AS9716_32D    | Yes                 | No                      |
      |  AS7726_32X    | No                  | No                      |
      |  AS5712_54X    | Yes                 | No                      |
      |  AS4630_54PE   | Yes                 | Yes                     |
      |  AS5835_54X    | Yes                 | Yes                     |
      |  QIX4-64X      | Yes                 | No                      |
      |  QIX7-BWDE-32X | Yes                 | No                      |
      |  QIX8-56X      | Yes                 | No                      |
      |  QIX9-32X      | Yes                 | No                      |
      :----------------|:--------------------|:-------------------------



# BIOS Upgrade:
- The watchdog functionality might be disabled in some platform because of missing functionaly in BIOS. 
- In order to support the for watchdog functionality, the platform BIOS image should be upgraded with latest version. 
- Following are the BIOS versoin for respective platform

      :----------------|:-----------------------
      |    PLATFORM    |  BIOS VERSION         |    
      :----------------| :----------------------
      |  AS7326_56X    |AS7326 V31  20180201   |
      |  AS7816_64X    |AS7816 V31  20170803   |
      |  AS7712_32X    |AS7712 V36  20170630   |
      |  AS9716_32D    |AS9716 V36  20190325   |
      |  AS7726_32X    |AS7726 V36  10180806   |
      |  AS5712_54X    |AS5712 V36  20180212   |
      |  AS4630_54PE   |AS4630 V513 20190924   |
      |  AS5835_54X    |AS5835 V36  20180212   |
      |  IX4-64X       |IX4 V5.11   20170929   |
      |  IX7-BWDE-32X  |IX7 V5.11   20200212   |
      |  IX8-56X       |IX8 V5.6    20180312   |
      |  IX9-32X       |IX7 V5.6    20180312   |
      :----------------|:-----------------------


    - AS7712-32X:
    https://support.edge-core.com/hc/en-us/articles/900000087626-AS7712-32X-BIOS-v36-20190624-latest-

    - AS5712-54X
    https://support.edge-core.com/hc/en-us/articles/900000070583-AS5712-54X-BIOS-v36-20190719-latest-

- Use the below procedure to upgrade the BIOS
    - Use AMI tool as below link for BIOS upgrade and the tool version is v5.12.03.2074.
    - URL: https://ami.com/en/download-license-agreement/?DownloadFile=Aptio_V_AMI_Firmware_Update_Utility.zip

- BIOS upgrade steps:
    1. Load the AMI tool and BIOS image into a USB drive.
    2. Select the EFI shell as boot option from BIOS boot menu.
    3. On the EFI shell, run the following command to upgrade the BIOS.
        shell> AfuEfix64.efi <BIOS image name> /P /B /K /ME

        Example:
        shell> AfuEfix64.efi AS7712_BIOS_V36_20190624.bin /P /B /K /ME


## Unsupported Hardware Platforms:

- Some ODM platforms do not support the Watchdog functionality. In such cases, the HW watchdog application service should remain dormant.
- The watchdog feature is not supported on the following platform.

      :------------------------------
	  | AS7326_56X (Not supported)  |
	  | AS7726_32X (Not supported)  |
	  | AS7816_64X (Not supported)  |
      |:-----------------------------

- For Dell platforms, it is disabled due to insufficient timeout value supported. It will be enabled when they support new FPGA and the new timeout value.

# Serviceability
- When the system gets rebooted because of watchdog timeout, the following string gets stored in the reboot cause file.

    - cat /host/reboot-cause/previous-reboot-cause.txt
      Hardware Watchdog Reset

- The reboot cause file is collected as part of tech-support collection. 

# CLI:

- The following show CLI command is updated to reflect the reboot cause because of watchdog timeout. 

	# show reboot-casue

	Example:
	# show reboot-cause
      Hardware Watchdog Reset

    Show reboot cause is shown only if platform supports it. For the current releaes, the following platform supports the reboot reason. 

       |:--------------
       |  AS4630_54PE  |
       |  AS5835_54X   |
       |:--------------



# Unit Test


  | SNO |  Unit Testcase 
 :------| :----------------------------------------------------
	 1 | Verify the watchdog reset by killing the watchdog daemon process/hardware hung simulation.
	 2 | Verify the reboot reason when system reboots because of watchdog timeout. 
	 3 | Verify the watchdog enable/disable support .
	 4 | Verify the watchdog with kdump collection.
	 5 | Verify the watchdog reading of watchdog timeout value.
	 6 | Verify the watchdog status when platform that doesn't support 180s timeout.
	 7 | Verify the watchdog status when platform that doesn't support watchdog feature.
	 8 | Verify the cold reboot when watchdog timer is enabled.
	 9 | Verify the warm/fast reboot when watchdog timer is enabled.
	10 | Verify the verify the watchdogutil command line interface.
	11 | Verify the watchdog service auto start during boot and killing the watchdog daemon.
	12 | Verify the 2.0 API interface.
	13 | Verify the non 2.0 API interface.


