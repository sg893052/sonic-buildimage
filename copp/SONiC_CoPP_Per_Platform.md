# SONiC CoPP Per Platform

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
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/12/2021  |   Michael Li       | Initial version                   |

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
The CoPP per Platform feature enables separate CoPP configurations for each SONiC platform which helps enhance CPU packet path protection and prioritization. The default CoPP policer rates and CPU queue scheduling weights can be tuned based on the platform's CPU packet I/O performance. CoPP can be further tuned using the existing CoPP mgmt based on specific application requirements.

The feature also allows CoPP configs to be loaded based on platform usage profiles. For example, the SONiC config generator can disable loading L2 related CoPP traps when the platform is configured with an L3 profile.

## 1.1 Requirements
The existing default CoPP config tuned for faster platforms will not be as effective in protecting the CPU packet path on platforms with slower CPUs. The existing CoPP management was not designed to tune the CoPP config per platform. A new mechanism is required to provide proper CoPP defaults based on platform performance. 

### 1.1.1 Functional Requirements
1. Support an optional CoPP config file per SONiC platform that will override the installation of the main CoPP config file on the target.
2. Provide tuned CoPP config defaults on supported Broadcom SONiC platforms based on CPU type.
3. The platform CoPP config file will support Python J2 config template format to allow SONiC config generator to load CoPP configs into CONFIG_DB based on conditional logic.

### 1.1.2 Configuration and Management Requirements
The existing SONiC CoPP configuration and management UIs will work with the platform CoPP config files.

### 1.1.3 Scaling and Performance Requirements
CPU Packet I/O performance profiling will be performed on supported Broadcom SONiC platforms based on the CPU type to help understand the control protocol scaling limits of the platform. Platforms that use the same CPU will use the same default CoPP policer rates and queue scheduler weights.

### 1.1.4 Warm Boot Requirements

## 1.2 Design Overview
### 1.2.1 Basic Approach
Where is the code coming from? Existing Open-source project? Internal Broadcom codebase? New development? Somewhere else? Mixture?

# 2 Functionality
## 2.1 Target Deployment Use Cases
Wordy description, with diagrams if possible
## 2.2 Functional Description
Wordy description

# 3 Design
## 3.1 Overview
big picture view of the actors involved.
## 3.2 DB Changes
Describe changes to existing DBs or any new DB being added.
### 3.2.1 CONFIG DB
### 3.2.2 APP DB
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
### 3.3.2 Other Process 
Describe changes to other process within SwSS if applicable.

## 3.4 SyncD
Describe changes to syncd if applicable.

# 4 Flow Diagrams
Provide flow diagrams for inter-container and intra-container interactions.

# 5 Error Handling
Provide details about incorporating error handling feature into the design and functionality of this feature.

# 6 Serviceability and Debug
Logging, counters, stats, trace considerations. Please make sure you have incorporated the debugging framework feature. e.g., ensure your code registers with the debugging framework and add your dump routines for any debug info you want to be collected. 

# 7 Warm Boot Support
Describe expected behavior and any limitation.

# 8 Scalability
Describe key scaling factor and considerations.

# 9 Unit Test
List unit test cases added for this feature including warm boot.

# 10 Internal Design Information
Internal BRCM information to be removed before sharing with the community.


