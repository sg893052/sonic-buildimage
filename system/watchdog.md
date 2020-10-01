

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
- A Watchdog daemon along with systemd service shall be added for refreshing the watchdog timer interval periodically.
- During the kernel panic, it should disable the hardware reset by extending the watchdog timer for collecting the kdump data.
- It should support a command to disable/enable the watchdog functionality and to view the watchdog current counter value and its status.
- Watchdog timer shall be supported for all the variants of intel iTCO PCH/ICH chipset boards.



## Configuration and Management Requirements

### Config commands
- The Watchdog support is enabled by default. This is a mandatory feature, it should not be disabled at any cause. 

### Show commands
- The 'show reboot-cause' CLI command should reflect the reboot cause if the device is rebooted because of hardware watchdog timeout.
 
## Reboot Requirements
- coldboot - during the cold reboot, watchdog timer should not be stopped. The watchdog timeout value should be tuned, so that the timer would be running till BIOS resets it. 
- fast-reboot/warm-reboot - the timeout value shall be configured long enough to retain the timer countdown until kernel reinitialises device after reboot. 


# Functional Description

## Design Overview

- The hardware watchdog designed to use the intel iTCO watchdog driver. This driver is supported on most of the ODM hardware platforms. The watchdog framework consists of following components:

	- Systemd service
	- Watchdog application daemon and utility tools 
	- Watchdog kernel drivers

- These files are preinstalled during the image build itself. When kernel boots up, the iTCO watchdog driver check for previous reboot reason by reading the watchdog status register and update the status according and initialize the watchdog hardware. During the system bootup, it starts the watchdog service as the first service and that starts the watchdog application daemon for watchdog time out refresh. 



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


- The Watchdog daemon application access the driver through watchdog device file. When the watchdog driver gets initialized in the kernel, it creates the device file '/dev/watchdogX' in /dev path. The watchdog daemon also creates the socket interface for the request from watchdogutil application. This is required because the watchdog device file can be opended only once and it can't be opened from multiple application. So the watchdog daemon open this for the timer keepalive update and also provides the socket interface for watchdog-util. The watchdog-util sends the request to daemon, the daemon processes the request and sends back the results to watchdog-util through local unix socket. The request and response would be in JSON format.  


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


## Systemd watchdog service

- The watchdog daemon opens the device and provides the necessary refresh to keep the system from resetting. It can test process table space, memory usage, file accessibility, work overload, file table overflow, IP address ping, network interface traffic, temperature, running processes and more. If the tests fail, then watchdog causes a shutdown.

- Usually a userspace daemon will notify the kernel watchdog driver via the /dev/watchdog special device file that userspace is still alive, at regular intervals.  When such a notification occurs, the driver will usually tell the hardware watchdog that everything is in order, and that the watchdog should wait for yet another little while to reset the system.  If userspace fails (RAM error, kernel bug, whatever), the notifications cease to occur, and the hardware watchdog will reset the system (causing a reboot) after the timeout occurs.

## ACPI watchdog migration

- Starting from Intel Skylake the iTCO watchdog timer registers were moved to reside in the same register space with SMBus host controller.  Not all needed registers are available and we need to unhide P2SB (Primary to Sideband) device briefly to be able to read status of required NO_REBOOT bit. The i2c-i801.c SMBus driver used to handle this and creation of the iTCO watchdog platform device.

- It relies on ACPI Watchdog Action Table (WDAT) table to describe the watchdog hardware to the OS. This table contains necessary information about the hardware and also set of actions which are executed by a driver as needed.

- This is required when NO_REBOOT flag is not accessible directly. 


## Reboot reason:

- The iTCO driver is patched to enable access to reboot status from the user space. This enables the driver to provide uniform interface to the application  for reading the reboot status. 

- The following command is used for reading the last reboot reason from hardware.

	# sysctl kernel.reboot_reason


## BMC implementation

- BMC, also known as Baseboard Management Controller, is a background controller that is integrated on the motherboard and independent of the CPU and memory. The system can be booted, shut down, etc. through the BMC. At present, the usual practice is to use the ipmi tool to send commands to the BMC to achieve the purpose of obtaining system information. For example, get CPU temperature, status, Power on, Power off system.

- BMC is the hardware device on the motherboard, so it does not know what operating system is installed on the host. It can only boot, shut down, send interrupt information, and can not directly start the system panic.

- BMC implementation is completely independent of this watchdog mechanism.


## 2.0 Platform driver interface
- SONiC defines Platform APIs, and SONIC 2.0 APIs include support for Watchdog APIs. The HW watchdog feature shall make use of the platform 2.0 API implementation if supported by platforms. 
- For platforms which do not support 2.0 APIs, a generic API implementation shall be provided to use iTCO driver if supported. No other implementation shall be supported.

## Kdump with watchdog
- When the kernel is crashed with panic, the watchdog timer shall be extended long enough to take the kernel kdump data. If the system is stuck in panic data collection, the watchdog automatically reboot the system. 


## System Reboot

### Coldboot
- coldboot - during the cold reboot, watchdog timer should not be stopped. The watchdog timeout value should be tuned, so that the timer would be running  till BIOS resets it. 

### Warmboot/Fastboot
- fast-reboot/warm-reboot - the timeout value shall be configured long enough to retain the timer countdown until the kernel reinitialises value after reboot. 

## Supported Platforms:
- The hardware watchdog feature is implemented and tested on the following platforms for the current release. 
	ACCTON:
		x86_64-accton_as7326_56x-r0
		x86_64-accton_as7816_64x-r0
		x86_64-accton_as7726_32x-r0
		x86_64-accton_as7712_32x-r0
		x86_64-accton_as9716_32D-r0
		x86_64-accton_as4630_54pe-r0
		x86_64-accton_as5712-54x-r0

	QUANTA:
		QUANTA_BWDE
		QuantaIX4
		QuantaIX8
		QuantaIX9

	DELL:
		9264
		9332
		5232
		5248

## Unsupported Hardware Platforms:
- Some ODM platforms do not support the Watchdog functionality. In such cases, the HW watchdog application service should remain dormant.

# Serviceability
- When the system gets rebooted because of watchdog timeout, the following string gets stored in the reboot cause file.

  # cat /host/reboot-cause/previous-reboot-cause.txt
    Hardware Watchdog Reset

- The reboot cause file is collected as part of tech-support collection. 

# CLI:

- The following show CLI command is updated to reflect the reboot cause because of watchdog timeout. 

	# show reboot-casue

	Example:
	# show reboot-cause
	  Hardware Watchdog Reset

# Unit Test


  | SNO |  Unit Testcase 
 :------| :----------------------------------------------------
	1 | Verify the watchdog reset by killing the watchdog daemon process/hardware hung simulation.
	2 | Verify the reboot reason when system reboots because of watchdog timeout. 
	3 | Verify the watchdog enable/disable support .
	4 | Verify the watchdog disable during kdump collection.
	5 | Verify the watchdog reading of watchdog timeout value.
	6 | Verify the 2.0 API interface.


