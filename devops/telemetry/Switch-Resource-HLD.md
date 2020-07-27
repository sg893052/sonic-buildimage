# Switch Resource Management in SONiC

# High Level Design Document
#### Rev 0.1



# Table of Contents

* [List of Tables](#list-of-tables)

* [Revision](#revision)

* [About This Manual](#about-this-manual)

* [Scope](#scope)

* [Definition/Abbreviation](#definitionabbreviation)

* [1. Feature Overview](#1-feature-overview)

	* [1.1 Requirements](#11-requirements)

	* [1.2 Design Overview](#12-design-overview)

* [2. Functionality](#2-functionality)

	* [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)

	* [2.2 Functional Description](#22-functional-description)

		* [2.2.1 Tomahawk 2 (BCM56970)](#221-tomahawk-2-BCM56970)

		* [2.2.2 Tomahawk 3 (BCM56980)](#222-tomahawk-3-BCM56980)

		* [2.2.3 Trident 3 (BCM56870)](#223-trident-3-BCM56870)

		* [2.2.4 Interaction with switch config-profiles](#224-Interaction-with-switchconfig-profiles)

* [3. Design](#3-design)

* [4. User Interface](#4-user-interface)

* [5. Flow Diagrams](#5-flow-diagrams)

* [6. Error Handling](#6-error-handling)

* [7. Serviceability and Debug](#7-serviceability-and-debug)

* [8. Warm Boot Support](#8-warm-boot-support)

* [9. Scalability](#9-scalability)

* [10. Unit Test](#10-unit-test)





# List of Tables

* [Table 1: Abbreviations](#table-1-abbreviations)

* [Table 2: Tomahawk 2 resource allocation](#table-2-tomahawk-2-resource-allocation )

* [Table 3: Tomahawk 2 scaling numbers](#table-3-tomahawk-2-scaling-numbers )

* [Table 4: Tomahawk 3 resource allocation](#table-4-tomahawk-3-resource-allocation )

* [Table 5: Tomahawk 3 scaling numbers](#table-5-tomahawk-3-scaling-numbers)

* [Table 6: Trident 3 resource allocation](#table-6-trident-3-resource-allocation )

* [Table 7: Trident 3 scaling numbers](#table-7-trident-3-scaling-numbers)

* [Table 8: Trident 3 max resource allocation](#table-8-trident-3-max-resource-allocation )

* [Table 9: Trident 3 max setting scaling numbers](#table-9-trident-3-max-setting-scaling-numbers)


# Revision

| Rev  |    Date    |              Author              | Change Description |
| ---- | :--------: | :------------------------------: | ------------------ |
| 0.1  | 07/17/2020 | Prabhu Sreenivasan               | Initial version    |


# About this Manual

This document provides information on Switch Resource Management feature content in SONiC.



# Scope
This document captures Switch Resource Management feature requirement and provides a design overview:



# Definition/Abbreviation

### Table 1: Abbreviations
| **Term** | **Meaning** |
| -------- | ---------------------------------------- |
| L2 | Layer 2   |
| L3  | Layer 3  |
|ALPM| Algorithmic Lower Prefix Match|
| EM | Exact Match |



# 1 Feature Overview

Switch resource management feature allows users to distribute the shared switch internal resources among different switch features needing them. It allows users the flexibility to allocate switch internal resources as per the feature needs, where additional resources can be allocated to an features while allocating less to less-used features.

The only supported resources configurable as of today are the shared memory used by L2, L3, ALPM and exact-match features. Drop-monitor feature uses exact-match entries for its internal function. The maximum number of flows that drop-monitor feature can operate depends on the available exact-match entries. Drop-monitor flow setting allows users control the use of exact-match entries by the feature. The allowed values are 'none', 'min' and 'max'. Setting drop-monitor-flows to values other than 'none' can negatively impact the L2, L3 and/or ALPM scaling limits on the device.


## 1.1 Requirements

### 1.1.1 Functional Requirements

- 1.1.1.0 - SONiC to set aside a minimum number of exact-match entries to allow drop-monitor feature to work without needing a switch reboot. This impacts L2, L3 and/or ALPM scaling limits as they share a common switch resource.
- 1.1.1.1 - SONiC to allow users to control the use of exact-match resource by drop-monitor feature. System shall set aside as much resources required for this configuration and thereby allow other applications to scale up using the remaining memory.
- 1.1.1.2 - A switch reboot is required to make drop-monitor-flow setting effective on the device.
- 1.1.1.3 - Any dependent resource calculation and allocation are automatically taken care of by the feature.
- 1.1.1.4 - It shall also be possible to set drop-monitor-flow setting to 'none' whereby the whole of the available shared memory is allocated for L2, L3 and ALPM scaling (Buzznik defaults).

### 1.1.2 Configuration and Management Requirements
- 1.1.2.0 - A KLISH CLI command shall be provided to configure the drop-monitor-flow setting under the switch-resource global config mode.
- 1.1.2.1 - It shall allow the user to set the value as 'min' (default option) to allocate minimum possible resources to drop-monitor.
- 1.1.2.2 - It should allow the user to set the value as 'max' to allocate maximum resource to drop-monitor.
- 1.1.2.3 - It shall allow the user to set the value as 'none' to avoid allocating any resource to drop-monitor.
- 1.1.2.4 - A KLISH CLI command should be provided to view the configured setting and also display the setting in use. These two settings will be different until a reboot of the switch.
- 1.1.2.5 - The KLISH CLI command should also provide a direction to the user whether a reboot is required to bring the current configuration in use.

### 1.1.3 Scalability Requirements
NA

### 1.1.4 Warm Boot Requirements

- 1.1.4.1 - Users must be allowed to perform warm-reboot, fast-reboot while using a switch resource management configuration.
- 1.1.4.2 - A warm-reboot of the switch while the "needs-reboot" flag is set may cause warm-boot failure.

### 1.1.5 Requirement Traceability


| Requirement Number | Description | Supported in 3.1 |
| ------------------ | ------------------------------------------------------------ | ---------------- |
| 1.1.2.2 | It should allow the user to set the value as 'max' to allocate maximum resource to drop-monitor.  | No |


## 1.2 Design Overview
The switch resource management feature is being designed and delivered as part of the Broadcom SONiC and will not be available in SONiC community releases.

A switch resource manager daemon (resrcmgrd) runs on the switch host (outside of all containers) and listens to CONFIG-DB tables related to the feature. The daemon invokes a platform specific script in response to any change in configuration and thus propagates the configuration as system settings (bcm file changes). A switch reboot is required to apply the newly applied setting and can be done by user as per their convenience.


# 2 Functionality
## 2.1 Target Deployment Use Cases

Switch resource manager helps scale specific applications based on the target deployment use cases. The primary target use of the feature is to allow drop-monitor-flows setting to allocate as much exact match memory as required to cater to the required drop monitor flows targeted for specific deployment use cases.


## 2.2 Functional Description

In the Buzznik release of SONiC, there are no exact-match tables allocated and hence drop-monitor feature will not work until the user configures the drop-monitor-max-flows configuration and reboots the device to bring that into effect.

Buzznik+ allocates a minimum number of exact-match tables from the shared memory and helps drop-monitor feature to function without needing a switch reboot. But it in turn takes a toll on the L2 or L3 or ALPM scaling limits supported by the device. Switch resource manager feature lets users allocate as many exact match tables required to support the drop-monitor flows needed on a specific deployment. It also lets users allocate zero exact match tables and hence allows other features L2, L3 and ALPM scale to their maximum.

Switch resource manager feature re-arranges the remaining shared resources across L2, L3 and ALPM. The re-arrangement is platform specific and tailor made for each switch variant.

Drop Monitor Max Flow calculation:

Drop Monitor firmware team has published the max supported flow scaling values for switch type. The value depends on multiple factors including number of exact match entries and SRAM memory available on R5 CPU. The supported drop monitor max flow is 57,000 flows due to SRAM constraints.

| **Device** | **Drop Monitor Flows** |
| -------------- | ------------------------------- |
| TH2 |             57,000 |
| TH3 |             57,000 |
| TD3 |             57,000 |

Note: On SONiC, the maximum drop-monitor supported flow is set to 40K based on the marketing input.

Considering flows are learned across all pipes, the max number flows per pipe is calculated as max-flows / num-pipes. Number of fpem_mem_entries required is twice the value of max_flows_per_pipe.

Note: In case if all the flows are learned on a single pipe, the calculation gets skewed.

The following sections provide details on how the shared resources are allocated for each switch type in SONiC.



### 2.2.1 Tomahawk 2 (BCM56970)

UFT allocation and sharing on TH2:

	* bank 0-1 are dedicated L2 banks (4k entries per bank) = 8K L2 reserved
	* bank 6-9 are dedicated L3 banks (2k entries per bank) = 8K L3 reserved
	* bank 2-5 are shared banks (64k L2/L3 or 32k FPEM entries or 96k ALPM entries per bank)
	* ALPM needs either 2 banks or 4 banks depending on ALPM bank mode.


Maximum drop-monitor flows calculation:
```
max drop-monitor flows per pipe => 57,000/4 = 14,250
fpem_mem_entries => 2 * 14,250 = 28,500
```
That means fpem_mem_entries=28,500 is required to be set on TH2 for achieving a max 57,000 drop-monitor flows spread across all pipes. Please note that a single UFT bank on TH2 translates to 32K fpem_entries and hence a single UFT bank is sufficient to support the maximum drop monitor flows on it. But due to limitations of UFT hardware design, a minimum of two UFT banks should be allocated for exact-match to reduce the hash collisions. 2 UFT banks translates to 64K fpem_mem_entries

#### Table 2: Tomahawk 2 resource allocation

| SDK config | L3 profile (flow=none)| L3 profile (flow=min) | L2 profile (flow=none)| L2 profile (flow=min)
| ---------------- | :----------: | :----------: | :----------: | ----------- |
| fpem_mem_entries |  0 | 64K | 0 | 64K |
| l2_mem_entries| 40K | 8K |256K| 136K |
| l3_mem_entries| 40K | 8K | 8K |8K |


#### Table 3: Tomahawk 2 scaling numbers

| Resource name | L3 profile (flow=none) | L3 profile (flow=min) | L2 profile (flow=none) | L2 profile (flow=min) |Description |
| ---------------- | :----------: | :----------: |:----------: | :----------: |-------------------------- |
| exact-match |  0  | 64K |   0 | 64K |Max drop-monitor flows supported |
| L2 |  40K |  8K |256K |  136K |Max number of MAC |
| L3 | 196K |196K |  -  |   -  |Max number L3 routes|




### 2.2.2 Tomahawk 3 (BCM56980)

UFT allocation and sharing on TH3:

	* bank 0-3 are shared with FPEM and ALPM (16K FPEM entries per bank) = 64K FPEM
	* bank 4-7 are dedicated for ALPM ( 45K ALPM entries per bank)
	* 8K reserved L2 (not part of UFT)
	* 16K reserved L3 (not part of UFT)
	* 0,2 or 4 banks can be allocated for FPEM

Maximum drop-monitor flows calculation:
```
max drop-monitor flows per pipe => 57,000/8 = 7125
fpem_mem_entries => 2 * 7125 = 14,250
```
That means fpem_mem_entries=14,250 is required to be set on TH3 for achieving a max 57,000 drop-monitor flows spread across all pipes. Please note that a single UFT bank on TH3 translates to 16K fpem_entries and hence a single UFT bank is sufficient to support the maximum drop monitor flows on it. But due to limitations of UFT hardware design, a minimum of two UFT banks should be allocated for exact-match to reduce the hash collisions. 2 UFT banks translates to 32K fpem_mem_entries.


#### Table 4: Tomahawk 3 resource allocation

| SDK config | L3 profile (flow=none)| L3 profile (flow=min)| L2 profile (flow=none)| L2 profile (flow=min)
| ---------------- | :----------: | :----------: | :----------: | ----------- |
| fpem_mem_entries   |  0  | 32K | - | - |
| l2_mem_entries| 8K | 8K | - | - |
| l3_mem_entries| 16K | 16K | - |- |


#### Table 5: Tomahawk 3 scaling numbers

| Resource name | L3 profile (flow=none) | L3 profile (flow=min) | L2 profile (flow=none) | L2 profile (flow=min) |Description|
| ---------------- | :----------: | :----------: |:----------: | :----------: |-------------------------- |
| exact-match |0|32K |  -  |  - |Max drop-monitor flows supported |
| L2 |   8K |  8K |  -  |  -  |Max number of MAC |
| L3 | 196K | 196K |  -   |   - |Max number L3 routes|







### 2.2.3 Trident 3 (BCM56870)

UFT allocation and sharing on TD3:

	* bank 0-1 are dedicated L2 banks (16k entries per bank) = 32K L2 reserved
	* bank 10-11 are dedicated L3 banks (8k entries per bank) = 16K L3 reserved
	* bank 2-9 are shared banks (32k L2/L3 or 16k FPEM entries or 48k ALPM entries per bank)
	* ALPM needs either 4 banks or 8 banks depending on ALPM bank mode.



Minimum drop-monitor flows calculation:

A minimum of two UDF banks need to be allocated for exact-match to avoid hash collisions. This translates to 2 * 16K = 32K fpem_mem_entries.
32K fpem_mem_entries can support 32K/2 = 16K drop-monitor flows per pipe. That means 32K fpem_mem_entries supports 32K drop-monitor flows spread across two pipes on TD3.

#### Table 6: Trident 3 resource allocation

| SDK config | L3 profile (flow=none)| L3 profile (flow=min) | L2 profile (flow=none)| L2 profile (flow=min)
| ---------------- | :----------: | :----------: | :----------: | ----------- |
| fpem_mem_entries     |  0 | 32K | 0 |32K |
| l2_mem_entries| 40K | 40K |288K | 224K |
| l3_mem_entries| 40K |40K |  16K | 16K |


#### Table 7: Trident 3 scaling numbers

| Resource name | L3 profile (flow=none) | L3 profile (flow=min) | L2 profile (flow=none) | L2 profile (flow=min) |Description |
| ---------------- | :----------: | :----------: |:----------: | :----------: |-------------------------- |
| exact-match | 0  |  32K |   0 | 32K |Max drop-monitor flows supported |
| L2 |  40K |  40K | 288K | 224K |Max number of MAC |
| L3 |  81K |  81K |  - | - |Max number L3 routes|




Maximum drop-monitor flows calculation:
```
Max drop-monitor flows per pipe => 57,000/2 = 28,500fpem_mem_entries => 2 * 28,500 = 57,000.
```
That means fpem_mem_entries=max-flow spread across two pipes on TD3. Please note that a single UFT bank on TD3 translates to 16K fpem_entries and hence four UFT banks are sufficient to support the maximum drop monitor flows on it. Four UFT banks translate to 16K*4 = 64K fpem_mem_entries.
Hence to support 57,000 drop-monitor flow, we need to have the below configuration

#### Table 8: Trident 3 max resource allocation

| SDK config | L3 profile (flow=none)| L3 profile (flow=max) | L2 profile (flow=none)| L2 profile (flow=max)
| ---------------- | :----------: | :----------: | :----------: | ----------- |
| fpem_mem_entries      |   0      | 64K  |  0         |   64K  |
| l2_mem_entries           |  40K   | 32K |  288K   | 160K |
| l3_mem_entries           |  40K   | 16K |    16K   |   16K |

 
#### Table 9: Trident 3 max setting scaling numbers

| Resource name | L3 profile (flow=none) | L3 profile (flow=max) | L2 profile (flow=none) | L2 profile (flow=max) |Description |
| ---------------- | :----------: | :----------: |:----------: | :----------: |-------------------------- |
| exact-match |  0  |  64K |   0 | 64K |Max drop-monitor flows supported |
| L2 |  40K |  32K | 288K | 160K |Max number of MAC |
| L3 |  81K |  81K |  -   |   -  |Max number L3 routes|

### 2.2.4 Interaction with switch config-profiles
SONiC has two switch profiles defined namely L2 and L3 to customize it based on L2 or L3 deployments. L3 profile is the default profile on a fresh installation.On platforms where drop-monitor is supported two UTF banks are carved outfor exact match tables from the L2 and L3 profiles. Hence the default L2 and L3 profiles values got updated for the drop-monitor feature. The resource allocation can be changed using the CLI command provided with switch resource management feature. The switch resource allocation falls back to the profile default values whenever a new profile is chosen by the user.


# 3 Design

## 3.1 Overview
Refer to section 2.

## 3.2 DB Changes
A new CONFIG_DB Table is added to allow users to configure the drop-monitor-flows parameter. A COUNTER_DB entry is added which reflects the current drop-monitor-flows setting running on the system.

### 3.2.1 CONFIG DB
SWITCH_RESOURCE_TABLE

```
;Stores switch resource configuration
key  = SWITCH_RESOURCE_TABLE|name ; name is one of the supported switch resource name.
                                  ; The only supported name as of today is DROP_MONITOR
flows = NONE / MIN                ; supported flows value as string
```
### 3.2.2 APP DB
None.

### 3.2.3 STATE DB
None.

### 3.2.4 ASIC DB
None.

### 3.2.5 COUNTER DB

SWITCH_RESOURCE_STATE_TABLE
```
;Stores switch resources running state
key  = SWITCH_RESOURCE_STATE_TABLE:name ; name is one of the supported switch resource name.
                                        ; The only supported name as of today is DROP_MONITOR
flows = NONE / MAX                      ; supported flows values as string

```
## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
No changes proposed.

### 3.3.2 Other Process
No changes proposed.

## 3.4 SyncD
No changes proposed.

## 3.5 SAI
No changes proposed.

# 4 User Interface
### 4.1 Data Models
SONiC YANG:

```

module sonic-switch-resource {
  namespace "http://github.com/Azure/sonic-switch-resource";
  prefix sswresource;
  yang-version 1.1;

  import sonic-extension {
    prefix sonic-ext;
  }


  organization
    "SONiC";

  contact
    "SONiC";

  description
    "SONiC Switch Resource yang model";

  revision 2020-07-07 {
    description
      "Initial revision.";
  }

  typedef switch-resource-type {
    type enumeration {
      enum DROP_MONITOR {
        description
          "Drop Monitor Resource";
      }
    }
    description
      "The Different Resource Types of Switch Resource Management  ";
  }


  typedef flow-scale-limit {
    type enumeration {
      enum "NONE" {
        description
          "No flow scale  configured.";
      }
      enum "MIN" {
        description
          "Minimum flow scale configured.";
      }
    }
    description
      "Drop Monitor Flow scale limit ";

  }

  container sonic-switch-resource {
    container SWITCH_RESOURCE_TABLE {
      list SWITCH_RESOURCE_TABLE_LIST {
        key "name";

        leaf name {
          type switch-resource-type;
          description
            "Types of Switch Resources";
        }


        leaf flows {
          type flow-scale-limit;
          description
            "Drop Monitor Flow scale options ";
        }

      }
    }

    container SWITCH_RESOURCE_STATE_TABLE {

      sonic-ext:db-name "COUNTERS_DB";
      sonic-ext:key-delim ":";

      config false;
      description "state switch resource table";

      list SWITCH_RESOURCE_STATE_TABLE_LIST {
        key "name";

        leaf name {
          type switch-resource-type;
          description
            "Types of Switch Resources";
        }


        leaf flows {
          type flow-scale-limit;
          description
            "Shows user-configured flow scale when the system boots up ";
        }
      }
    }
  }
}



```

module openconfig-switch-resource {

    yang-version "1";

    // namespace
    namespace "http://openconfig.net/yang/system/switch-resource/extension";

    prefix "oc-swresource";

    // import some basic types
    import openconfig-extensions { prefix oc-ext; }

    // meta
    organization
        "SONiC";

    contact
        "SONiC";

    description
        "This module contains a collection of YANG definitions for the
        Switch Resources Management for resource like Exact Match Entries. 
        It has Global settings.";

    revision "2020-07-06" {
        description
            "Initial draft revision.";
        reference "0.1.0";
    }

    oc-ext:openconfig-version "1.0.2";

    // typedef statements

    typedef switch-resource-type {
        type enumeration {
            enum DROP_MONITOR {
                description
                    "Drop Monitor Resource";
            }
        }
        description
            "The Different Resource Types of Switch Resource Management  ";

    }

    typedef flow-scale-limit {
        type enumeration {
            enum "NONE" {
                description
                    "No flow scale configured.";
            }
            enum "MIN" {
                description
                    "minumum flow scale configured.";
            }
        }
        description
            "Drop Monitor Flow Scale Options";

    }

    // grouping statements

    grouping switch-resource-config {
        description
            "Switch Resources Configuration parameters";
        leaf name {
            type switch-resource-type;

            description
                "Resource name.";
        }


        leaf flows {
            type flow-scale-limit;
            description
                "Drop Monitor Flow Scale  limit";
        }

    }

    grouping switch-resource-top {
        description
            "Top level grouping for attributes for Switch Resources .";

        container swresource {
            description
                "Parameters related to switch resource manager configuration and status.";

            list resource {
                key "name";
                description
                    "Switch Resource Name.";


                leaf name {
                    type leafref {
                        path "../config/name";
                    }
                    description
                        "Key name"; 
                }

                container config {
                    description
                        "Configuration parameters of Switch Resources"; 

                    uses switch-resource-config;
                }

                container state {
                    config false;
                    description
                        "State parameters of Switch Resources";
                    uses switch-resource-config;
                }
            }
        }
    }
}


```

### 4.2 CLI
#### 4.2.1 Configuration Commands

KLISH

Configure Drop Monitor Flow configuration
```
    sonic(config-switch-resource)# drop-monitor flows min

```
Remove Drop Monitor Flows Configurations
```
    sonic(config-switch-resource)# drop-monitor flows none
```

CLICK
Configure Drop Monitor Flow configuration
```
    config switch-resource drop-monitor flows min

```
Remove Drop Monitor Flows Configurations
```
    config switch-resource drop-monitor flows none
```

#### 4.2.2 Show Commands

**show switch-resource drop-monitor**

This command displays the current drop-monitor switch resource configuration currently in use.

 **Usage:**
    show switch-resource drop-monitor

 **Example:**

```
show switch-resource drop-monitor

Drop-Monitor flows
----------------------
Configured           : none
In use               : min
Needs reboot         : true
```

#### 4.2.3 Debug Commands
N/A

#### 4.2.4 IS-CLI Compliance
N/A

### 4.3 REST API Support
REST APIs are supported via OC YANG objects.

### 4.4 Service and Docker Management
A new service named resrcmgrd is started on switch host during startup.No new dockers are added.

# 5 Flow Diagrams
NA

# 6 Error Handling
No changes proposed.

# 7 Serviceability and Debug
NA

# 8 Warm Boot Support
This feature does not impact the warm-reboot capability of the switch. But a change in switch resource configuration during warm-reboot of the switch may cause failure. It is recommended to make sure the switch resource configuration is applied to hardware before triggering a warm-boot.


# 9 Scalability

Allocating exact-match entries for drop-monitor reduces the scale of L2, L3, or/and ALPM entries supported by the switch. Please refer to the section [2.2 Functional Description](#22-functional-description) for impact on each of the supported platforms.



# 10 Unit Tests
The following tests are limited to testing the switch resource management feature and does not cover drop-monitor functionality or scale testing.

- Verify that the system becomes operational when exact-match entries are enabled on the switch.
- Verify that the drop-monitor functionality works on a newly installed device without any specific commands to allocate exact-match entries to drop-monitor.
- Verify the "show switch-resource drop-monitor" command displays the current drop-monitor settings
- Verify the command to allocate / de-allocate exact-match entries on the switch for drop-monitor feature




### Conclusion

By default UFT exact-match entries are enabled on platforms where drop-monitor feature is supported. This allows drop-monitor feature to work without requiring a reboot. But this impacts the L2, L3 and ALPM scaling constants as exact-match is a shared resource. A CLI command is made available to disable the use of exact-match entries and bring back the scaling numbers to the original maximum value.
