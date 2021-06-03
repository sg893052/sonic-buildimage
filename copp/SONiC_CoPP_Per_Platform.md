# SONiC CoPP Per Platform

# High Level Design Document
#### Rev 0.2

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  
# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |            Author            | Change Description                        |
|:---:|:-----------:|:----------------------------:|-------------------------------------------|
| 0.1 | 04/12/2021  | Michael Li / Aravindu Maneti | Initial version. Add requirements section |
| 0.2 | 05/12/2021  | Michael Li / Aravindu Maneti | Update full HLD                           |

# About this Manual
This document provides general information about the CoPP per Platform feature implementation in SONiC.
# Scope
This document describes the high level design of the CoPP feature enhancement that adds a CoPP config per platform.  Please see the [SONiC CoPP HLD](https://github.com/project-arlo/SONiC/blob/1d6a3ce682f4ab49a3ad1b0eb7e116b2802292eb/doc/copp/SONiC_CoPP.md
) for more details on general SONiC CoPP feature.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| CoPP                     | Control Plane Policing              |

# 1 Feature Overview
The CoPP per Platform feature enables separate CoPP configurations for each SONiC platform which helps enhance CPU packet path protection and prioritization. The default CoPP policer rates and CPU queue scheduling weights can be tuned for the platform's CPU packet I/O performance. CoPP can be further tuned using the existing CoPP mgmt based on specific application requirements.

The feature also allows generation of default CoPP configs based on ASIC capabilities and platform usage profiles. For example, the feature can disable specific traps that are not supported on an ASIC family or variant.

## 1.1 Requirements
The default CoPP config is tuned for faster platforms and will not be as effective in protecting the CPU packet path on platforms with slower CPUs. CoPP management can change the config but it was not designed to provide tuned CoPP configurations per platform. A new mechanism is required to provide proper CoPP defaults based on platform performance. 

### 1.1.1 Functional Requirements
1. Support an optional CoPP config file per SONiC platform that will override policer rates, queue assignments, scheduler weights in the base CoPP config file.
2. Support an optional CoPP capabilities config file per ASIC variant for disabling unsupported traps across platforms using a common ASIC.
3. Support an optional CoPP capabilities config file per SONiC platform for disabling unsupported traps on a specific platform that will override the ASIC COPP capabilities config.
4. Provide tuned CoPP config defaults on slower Broadcom SONiC platforms as part of the "SONiC on a diet" and "SONiC for Campus" initiatives.

### 1.1.2 Configuration and Management Requirements
The existing SONiC CoPP configuration and management UIs will work with the platform CoPP config files.

### 1.1.3 Scaling and Performance Requirements
CPU Packet I/O performance profiling will be performed on slower Broadcom SONiC platforms to help understand the protocol scaling limits of the platform. CoPP policer rates and CPU queue scheduling weights will be tuned on slower platforms identified as part of the "SONiC on a diet" and "SONiC for Campus" initiatives.

### 1.1.4 Warm Boot Requirements


# 2 Functionality
## 2.1 Target Deployment Use Cases
The CoPP per platform feature enables proper protection for the CPU Pkt I/O path on slower platforms identified as part of the "SONiC on a diet" and "SONiC for Campus" initiatives.

## 2.2 Functional Description
SONiC supports a variety of platforms with different combinations of ASIC capabilities and CPU performance but only supports a single CoPP configuration. To provide proper CPU packet path protection, CoPP configs should be customized based on platform capabilities. The CoPP per platform feature provides flexible customization of default CoPP configs for each SONiC platform to enable enhanced CPU packet path protection.

CoPP trap group policer rates can be tuned based on the platform's CPU Pkt IO path performance and protocol scaling requirement. CPU queue assignments can be customized to accomodate for platforms that support fewer number of CPU queues.

A separate CoPP capabilities config is introduced to allow disabling of unsupported CoPP traps based on ASIC type and specific platform. Providing the CoPP capabilities in ConfigDB enables the management infrastructure to provide feedback to users on unsupported configurations. Management infrastructure can check for the existence of CoPP entries in the ConfigDB table to determine if the platform supports the trap (note that CoPP management disables supported traps by setting entries to NULL).

# 3 Design
## 3.1 Overview
## 3.1.1 CoPP Platform Config
An optional CoPP platform config file (copp_platform_config.j2) will be added in the [SONiC device directories](https://github.com/Azure/sonic-buildimage/tree/master/device) where platform specific configuration data is stored ([example for AS7712]((https://github.com/Azure/sonic-buildimage/tree/master/device/accton/x86_64-accton_as7712_32x-r0/Accton-AS7712-32X))). This file will be populated with CoPP trap group entries in json format similar to the CoPP base config file. 

A CoPP config generation script will be added to the sonic-cfggen infrastructure to support overriding the CoPP base config entries with CoPP platform config entries when generating the default CoPP config for the platform.

## 3.1.2 CoPP Capabilities Config
To support disabling of unsupported traps on specific ASICs, a CoPP capabilities config file (copp_capabilities_config.j2) will be added in the SONiC device ASIC directory located below:

```
/sonic-buildimage/device/broadcom/x86_64-broadcom_common/
  x86_64-broadcom_b37/	H	04-Feb-2021	4 KiB
  x86_64-broadcom_b77/	H	02-Mar-2021	4 KiB
  x86_64-broadcom_b85/	H	02-Mar-2021	4 KiB
  x86_64-broadcom_b87/	H	02-Mar-2021	4 KiB
  x86_64-broadcom_b96/	H	02-Mar-2021	4 KiB
  x86_64-broadcom_b97/	H	02-Mar-2021	4 KiB
  x86_64-broadcom_b98/	H	03-Apr-2021	4 KiB
```
These files will define a set of traps that are unsupported by the ASIC family.  By default, all traps from the CoPP base config are supported.
```
{
    "copp_feat_ptp": "false"
    "copp_feat_nat": "false"
}
```
A similar file can be added in the platform directory where the CoPP platform config file is stored to override specific capabilities from the ASIC CoPP capabilities. For example, by default the copp_feat_nat is supported on all TD3 platforms.  However, a specific platform vendor may choose to disable the NAT feature and can add "copp_feat_nat" capability as false in copp_capabilities_config.j2 file for the platform.

### 3.1.2.1 Global CPU Rx Rate Limit
CoPP has a global CPU Rx rate limit implemented in the Pkt IO Driver (BRCM KNET) to limit the aggregate Rx rate to CPU.  The default rate is 30Kpps and can be changed dynamically with the following click command:
```
admin@sonic:~$ sudo config copp ?
Usage: config copp [OPTIONS] COMMAND [ARGS]...

Commands:
  rx-burst  Configure COPP rx-burst <10-10000>
  rx-rate   Configure COPP rx-rate <100-100000>
```

To reduce the global rate for all traffic to CPU on a lower performance platform, set the cpu rate limit in the platform copp_capabilities_config.j2 file.  In the example below, the platform default global CPU rx rate limit is set to 20Kpps.
```
{
    "copp_rx_rate": "20000"
}
```

## 3.2 DB Changes
There are no new changes to SONiC DBs.

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
There are no new changes to Orchagent.
## 3.4 SyncD
There are no new changes to SyncD.

## 3.5 Manageability
### 3.5.1 Config Setup
CoPP platform file handling will be added as a user defined extension to the [Config-setup infrastructure](https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/config-setup.md).

This logic will perform the following functions:
- If a CoPP platform capabilities file exists, use this data with the base CoPP config J2 template to generate a base config file.
- Else if a CoPP ASIC capabilities file exists for this platform, use this data with the base CoPP config J2 template to generate base config file.
- If a copp_platform_config.j2 file exists, merge this data with the generated base config file for the final default CoPP config.

### 3.5.2 Config Save
CoPP management feature introduced [changes in the config save script](https://github.com/BRCM-SONIC/sonic_doc_private/blob/877f06cc40b5829dfc1cc21458702a4130c5b3c3/copp/SONiC_CoPP.md#config-save) to prevent the default CoPP config loaded into ConfigDB from being written into the config_db.json file.  The sonic-cfggen was modified to compare the default CoPP config with the CoPP tables in ConfigDB and write only the diff into the config_db.json file.  Only the user changes will be replayed back on config reload.

This logic will remain unchanged and still applies for the new CoPP platform config.

# 4 Flow Diagrams
Provide flow diagrams for inter-container and intra-container interactions.

# 5 Error Handling
Provide details about incorporating error handling feature into the design and functionality of this feature.

# 6 Serviceability and Debug
Logging, counters, stats, trace considerations. Please make sure you have incorporated the debugging framework feature. e.g., ensure your code registers with the debugging framework and add your dump routines for any debug info you want to be collected. 

# 7 Warm Boot Support
No warmboot support. CoPP functionalities are not expected to work during warm reboot but will be restored after warm reboot. Traffic to CPU will get dropped when CPU is reset until SAI drivers have restored the CPU packet path.

# 8 Scalability
## 8.1 Profiling and Tuning
The CPU packet I/O performance will be profiled on the new platforms targeted for Campus. Linux tools such as iperf and flood ping tests will be performed to gauge the packet I/O performance.  Going forward, the packet I/O performance metric will be added to the PDE benchmark list which is used to help compare system performance across SONiC platforms.

The packet I/O performance will be compared with existing platforms to extrapolate CoPP configurations on the new platforms as a starting point for CoPP tuning.  Single dimensional tests will be performed to test latency sensitive protocols such as BFD against high rate packets (sFlow, ARP flood, IP2ME) cases to further tune policer rates.

## 8.2 Recommended Scaling and Performance System Test cases
Further tuning may be needed based on the results of recommended multi-dimensional system test cases below.
1. Verify max BFD scale do not flap with high rate multi-dimensional protocol traffic (max sFlow sampling rate + max BGP + max ARP + max IP2ME + max subnet) on slower campus platform
2. Verify max LACP scale (fast timer) does not flap with high rate multi-dimensional protocol traffic on slower campus platform
3. Verify no pkt drops on max STP scale with high rate multi-dimensional protocol traffic on slower campus platform
4. Verify no pkt drops on max UDLD scale with high rate multi-dimensional protocol traffic on slower campus platform


# 9 Unit Test
## 9.1 Functional Test Cases
1. Verify CoPP trap policer rate based on platform CoPP config
2. Verify CoPP trap queue assignment based on platform CoPP config
3. Verify disable of CoPP traps based on ASIC capabilities CoPP config
4. Verify disable of CoPP traps based on platform capabilities CoPP config
5. Verify disable of CoPP traps based on platform capabilities CoPP config overriding ASIC capabilities CoPP config

## 9.2 Install and Config Setup Cases
1. Verify platform CoPP config applied using ONIE image install
2. Verify platform CoPP config applied using sonic-installer
3. Verify platform CoPP config applied using ztp config generation
4. Verify platform CoPP config applied using config-setup factory defaults
5. Verify platform CoPP config changes applied on config-reload
6. Verify platform CoPP config applied using sonic-installer upgrade from image before SONiC 4.0.
7. Verify platform CoPP config is NOT applied when using sonic-installer to downgrade from SONiC 4.0 to previous versions.
8. Verify platform CoPP config applied with config-save before sonic-installer upgrade
9. Verify platform + user defined CoPP config applied with config-save before sonic-installer upgrade
10. Verify platform + user defined CoPP config applied with config-save before sonic-installer downgrade to image before SONiC 4.0

## 9.2 Warm Boot Test Cases
1. Verify platform CoPP config is NOT applied on warm-boot upgrade from pre-SONiC 4.0 to SONiC 4.0
2. Verify platform CoPP config is retained on warm-boot downgrade from SONiC 4.0 to pre-SONiC 4.0 image.

## 9.3 Negative Test Cases
1. Verify graceful handling (use base CoPP config) on config-setup merge failure of CoPP platform config
2. Verify graceful handling of invalid CoPP capabilities data


# 10 Internal Design Information



