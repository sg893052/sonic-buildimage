# Feature Name
Layer 2 Forwarding Enhancements.
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
| 0.1 | 04/11/2019  |   Anil Pandey      | Initial version                   |

# About this Manual
This document provides general information about the Layer 2 Forwarding Enhancements feature implementation in SONiC.
# Scope
This document describes the high level design of Layer 2 Forwarding Enhancements feature. Call out any related design that is not covered by this document

# Definition/Abbreviation
### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| XYZ                      | Term description                    |

# 1 Feature Overview

A workflow discussion is underway that proposes a 2-stage review process for this document: -
1) First Pass: Within ~1 week of feature start. Fills out section 1, including top-level requirements.
2) Second Pass: Document complete, including all sections. Requirements are detailed enough for test case generation, and are immutably numbered.

If smaller features can get to the second pass within 1 to 2 weeks of starting, then the first pass can be skipped.


## 1.1 Requirements

### 1.1.1 Functional Requirements

Requirement guidance: -
- First Pass: Fill out with top-level requirements that put the big picture in place
- Second Pass: Fill out with detailed, immutably numbered requirements from which test cases can be generated

See example file in template folder

### 1.1.2 Configuration and Management Requirements
Which UI's will be provided for the feature? 
How will those UI's be implemented. Options might be:
- "Old" SONiC CLI (as of 201904 release)
- New SONiC Management Framework (Broadcom-provided in 201908 and beyond)
- Use/extend existing non-SONiC UI (e.g. FRR)

### 1.1.3 Scalability Requirements
key scaling factors
### 1.1.4 Warm Boot Requirements

## 1.2 Design Overview
### 1.2.1 Basic Approach
Where is the code coming from? Existing Open-source project? Internal Broadcom codebase? New development? Somewhere else? Mixture?

### 1.2.2 Container
Which container(s) will the code go into? Will there be new container(s)?

### 1.2.3 SAI Overview
Do we expect to need a new SAI API specification? If so, provide rough semantics (information flow across the API)? 
If using existing SAI APIs, then which area?
For non-hardware features this section will be N/A

# 2 Functionality
## 2.1 Target Deployment Use Cases
Wordy description, with diagrams if possible
## 2.2 Functional Description
Wordy description

# 3 Design
## 3.1 Overview
big picture view of the actors involved
## 3.2 DB Changes
Describe changes to existing DBs or any new DB being added
### 3.2.1 CONFIG DB
### 3.2.2 APP DB
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
### 3.3.2 Other Process 
Describe changes to other process within SwSS if applicable

## 3.4 SyncD
Describe changes to syncd if applicable

## 3.5 SAI
Describe new/existing SAI APIs used by this feature

## 3.6 CLI
### 3.6.1 Data Models
Can be reference to YANG if applicable
### 3.6.2 Configuration Commands
### 3.6.3 Show Commands
### 3.6.4 Debug Commands
### 3.6.5 REST API Support

# 4 Flow Diagrams
Provide flow diagrams for inter-container and intra-container interactions

# 5 Error Handling
Provide details about incorporating error handling into the design and functionality of this feature

# 6 Serviceability and Debug
Logging, counters, stats, trace considerations

# 7 Warm Boot Support
Describe expected behavior and any limitation

# 8 Scalability
Describe key scaling factor and considerations

# 9 Unit Test
List unit test cases added for this feature including warm boot

# 10 Internal Design Information
Internal BRCM information to be removed before sharing with the community
 
