# System Readiness

System Readiness

# High Level Design Document

#### Rev 0.1

# Table of Contents
 * [List of Tables](#list-of-tables)
 * [Revision](#revision)
 * [About This Manual](#about-this-manual)
 * [Scope](#scope)
 * [Definition/Abbreviation](#definitionabbreviation)
 * [Feature Overview](#FeatureOverview)
 * [Requirements](#Requirements)
 * [Design](#Design)
 * [Unit Test](#UnitTest)


# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |   Date  |    Author    | Change Description        |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/26/2021 |  Senthil Kumar Guruswamy    | Initial version          |

# About this Manual

This document provides general information about System readiness in SONiC.

# Scope

This document describes the high-level design of the System readiness in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**         | **Meaning**             |
|--------------------------|-------------------------------------|
TBD

# 1 Feature Overview

SONiC is an open-source network operating system based on Debian Linux, integrated with various systemd services. 
At present, system readiness is based on only core docker services(swss ,bgp,teamd,pmon,syncd,database,mgmt-framework) and the portinitdone status.

In this HLD, the system readiness will be enhanced to consider the overall active systemd services consisting of
1. All docker services instead of just a few core docker services.
2. Vital host services.
3. The portready status along with portinit.

The existing "show system status" click command and its output will not be disturbed as many scripts have already been developed to check on this particular output string.
New click command "show system status all" will be introduced for knowing the overall system status which will be helpful in debugging and can be integrated to techsupport dump.

## 1.1 Requirements

### 1.1.1 Functional Requirements
System Readiness:
- Identify the vital host systemd services 
- Introduce HOST_SERVICE table in CONFIG_DB to include vital host services. FEATURE table already exists for all docker services.
- Sysmonitor to check system status of all the service units from the above tables[FEATURE,HOST_SERVICE] & the Port ready status and then show and post 'FULL SYSTEM READY' status in the STATE_DB.
  Service status Details:
	- Check only the status of "enabled" services(host+docker).
	- Docker Services:
		- In addition to checking the dockers, check for all the processes(critical+non-critical) inside the docker containers.
		- Also, Enforce all docker owners to implement supevisord proc_exit_listener to exit docker on its process exit.
		- Multiple instances of a process inside docker cannot be monitored from Host.
	- List down the failed services and its status.

  Port Ready status Details:
	- As part of show system status, today we consider portInitDone message from SWSS. But this is only the initial port creation in DB, and no way reflects the actual port status. This is the portInitDone from PORTTABLE. However there is one more portInitDone updated by portsyncd in the state DB by portsyncd. This is updated when host interfaces are created in the kernel. We plan to use this one.
- Provide configuration commands to enable/disable host features/services.
- hostcfgd will subscribe to FEATURE table(s) and take appropriate enable/disable action on the feature state being modified.
- New Click CLI
	- "show system status all" to be introduced to cover the overall system status.
	- This CLI to display the existing core system status as well as the overall system status with the failed services' systemctl status.
	- During the techsupport data collection, the new click CLI to be included for debugging.
	- Click commands to enable/disable host services and also to see the status of all the host services to be introduced.


### 1.1.2 Configuration and Management Requirements
- Click CLI to be introduced to show the overall system status in addition to the existing 'show system status' CLI which covers the core services.
	"show system status all"
- Implement config hostservice state Click comamnd to enable/disable host features/services.
	"config hostservice state <service> <enabled/disabled>"
- Implement show hostservice status Click command to see status of the host services.
	"show hostservice status"

### 1.1.3 Scalability Requirements
- NA

### 1.1.4 Warm/fast/cold Boot Requirements
-TBD
 
# 2 Design
## 2.1 Overview

TBD

## 2.2 Current Model

TBD

## 2.3 Feature Details

TBD

## 2.4 KLISH/CLICK Commands

TBD 

## 2.5 Developer Guidelines

TBD

# 3 Unit Test

TBD


