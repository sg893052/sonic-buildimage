# Feature Name
Port Link Flap Error Disable
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
| Rev |     Date    |       Author       | Change Description                                   |
|:---:|:-----------:|:------------------:|------------------------------------------------------|
| 0.1 | 04/14/2021  |   Steven Lu        | Initial version for requirements                     |
| 0.2 | 04/20/2021  |   Steven Lu        | Change feature name to Port Link Flap Error Disable  |
| 0.3 | 05/11/2021  |   Steven Lu        | Add design details                                   |

# About this Manual
This document provides general information about the Port Link Flap Error Disable feature implementation in SONiC.
# Scope
This document describes the high level design of Port Link Flap Error Disable feature. Call out any related design that is not covered by this document

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| XYZ                      | Term description                    |

# 1 Feature Overview
The Port Link Flap Error Disable feature uses an exponential decay mechanism to prevent excessive interface flapping events from adversely affecting routing protocols and routing tables in the network. Suppressing port state change events to protect the system resources.

When Port Link Flap Error Disable is enabled, the system monitors the number of times a port link state toggles from "up to down", and not from "down to up".

The sampling time or window (the time during which the specified toggle threshold can occur before the wait period is activated) is triggered when the first "up to down" transition occurs.

If the port link state toggles from up to down for a specified number of times within a specified period, the interface is physically disabled for the specified wait period. Once the wait period expires, the port link state is re-enabled. However, if the wait period is set to zero (0) seconds, the port link state will remain disabled until it is manually re-enabled.


## 1.1 Requirements
System shall be able to suppress interfaces state change events to protect system resources.
User shall be able to enable or disable the feature on individual interfaces and globally.
The feature must be disabled on all interfaces by default.
The feature shall be supported on physical interfaces.
There must be two sets of configuration parameters (sample-interval, waiting-period, and toggling-frequency) a per-interface set and a global set. If both global and per-interface are configured, the per-interface values are used only for given interfaces. Global values are used for all other physical interfaces.
If no values are specified by user, a default set of parameters are applied to all interfaces.
User shall be able to save configuration parameters (both global and per-interface).
The configuration parameters (both global and per-interface) must be preserved across device reboot.

### 1.1.1 Functional Requirements
Port Link Flap Error Disable shall use below parameters to supress and protect system.
- toggle-frequency
Specifies the number of times a port link state goes from up to down before the wait period is activated. The value ranges from 1 through 50.
- sample-interval
Specifies the amount of time, in seconds, during which the specified toggle threshold can occur before the wait period is activated. The default value is 0 and indicates that the time is forever. The value ranges from 0 through 65535.
- waiting-period
Specifies the amount of time in seconds, for which the port remains disabled (down) before it becomes enabled. The value ranges from 0 through 65535. A value of 0 indicates that the port will stay down until an administrative override occurs.

### 1.1.2 Configuration and Management Requirements
- Port Link Flap Error Disable feature default is OFF on all physical interfaces and port-channels
- When Port Link Flap Error Disable is enabled, use below default values:
  sample-interval: 10
  toggle-frequency: 3
  waiting-period: 30
- User shall be able to specify different sample-interval, toggle-frequency and waiting-period on a physical interface
- User shall be able to display current Port Link Flap Error Disable confiuration values.
- User shall be able to display current interface status if it was surpresed by Port Link Flap Error Disable
- User shall be able to display Link-Down-Reason if a port is disabled by Port Link Flap Error Disable feature
- When port is supressed or released by Error Disable, shall be logged in syslog.


### 1.1.3 Scalability Requirements
Port Link Flap Error Disable must be supported on all physcial ports. All the platform design, SDK, and ASIC constraints should be considered.


### 1.1.4 Warm Boot Requirements
Interfaces that are configured Port Link Flap Error Disable must be retained across warmboot.


## 1.2 Design Overview
### 1.2.1 Basic Approach
The Interface Error Disable feature is base on SONiC code base and architecture.

### 1.2.2 Modules and Containers
The Interface Error Disable feature exist in below modules and containers:
1. syncd/SAI
2. swss/orchagentd, portmgrd, portsyncd
3. mgmt-framework
4. click


# 2 Functionality
## 2.1 CLI


# 2.2 Functional Description

# 3 Design
## 3.1 Overview
## 3.2 DB Changes
### 3.2.1 CONFIG DB
For individual physcial interface
"PORT|Ethernet124":
{
    "error-disable": "on|off",
    "toggle-frequency": "3",
    "sampling-interval": "5",
    "wait-time-period": "10"
},

### 3.2.2 APP DB
For individual physcial interface
"PORT_TABLE|Ethernet124":
{
    "error-disable": "on|off",
    "toggle-frequency": "3",
    "sampling-interval": "5",
    "wait-time-period": "10"
},

To surpress interface:
"PORT_APP_STATUS_TABLE:Ethernet0" :
{
    "err_disable_status": "up|down"
}


### 3.2.3 STATE DB
Record number of link flaps within sampling-interval:
"PORT_ERR_DISABLE_TABLE|Ethernet0" :
{
    "start-time": time,
    "link-flaps" :counts,
    "supress-time": time,

    "error-disable": "on|off",
    "toggle-frequency": "3",
    "sampling-interval": "5",
    "wait-time-period": "10"
}

### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
### 3.3.1 mgmt-framework/CLI
mgmt-framwrok process CLI inputs and populate Interface Error Disable configurations into CONFIG_DB

### 3.3.2 portmgr
portmgr listens on CONFIG_DB and process Interface Error Disable configurations and produce in APP_DB

### 3.3.3 Orchestration Agent
orchagent listerns APP_DB and produces Interface Error Disable configurations in STATE_DB.
orchagent monitors link events and update PORT_ERR_DISABLE_TABLE in STATE_DB
orchagent takes action if surpress or re-enable port if criteria meet.

## 3.4 SyncD
synd reports link events to swss and enable/disbale ports base on requests from swss.

## 3.5 SAI

## 3.6 User Interface
### 3.6.1 Data Models
Can be reference to YANG if applicable. Also cover gNMI here.

### 3.6.2 CLI
Refer to Functionality

### 3.6.3 REST API Support
### 3.6.4 Service and Docker Management
No new service ot docker introduced

# 4 Error Handling


# 5 Serviceability and Debug
syslog recrods Interface Error Disable configuration changes and link enable/disable triggered by Interface Error Disable

# 7 Warm Boot Support
Interface Error Disable configurations remain same cross warm-reboot

# 8 Scalability

# 9 Unit Test

# 10 Internal Design Information

