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
| Rev |     Date    |       Author            | Change Description                                   |
|:---:|:-----------:|:-----------------------:|------------------------------------------------------|
| 0.1 | 04/14/2021  |   Steven Lu             | Initial version for requirements                     |
| 0.2 | 04/20/2021  |   Steven Lu             | Change feature name to Port Link Flap Error Disable  |
| 0.3 | 05/11/2021  |   Steven Lu             | Add design details                                   | 
| 0.4 | 05/24/2021  | Prasanth Kunjum Veettil | Add CLI and RESTCONF details                         |
| 0.5 | 09/16/2021  | Steven Lu               | Removed "Not-err-disabled" state                     |

# About this Manual
This document provides general information about the Port Link Flap Error Disable feature implementation in SONiC.
# Scope
This document describes the high level design of Port Link Flap Error Disable feature. Call out any related design that is not covered by this document

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
| xcvrd                    | Transceiver Daemon                  |

# 1 Feature Overview
The Port Link Flap Error Disable feature uses an exponential decay mechanism to prevent excessive interface flapping events from adversely affecting routing protocols and routing tables in the network. Suppressing port state change events to protect the system resources.

When Port Link Flap Error Disable is enabled, the system monitors the number of times a port link state toggles from "up to down", and not from "down to up".

The sampling interval or window (the time during which the specified toggle threshold can occur before the recovery wait period is activated) is triggered when the first "up to down" transition occurs.

If the port link state toggles from up to down for a specified number of times within a specified period, the interface is physically disabled for the specified recovery wait period. Once the recovery wait period expires, the port link state is re-enabled. However, if the recovery wait period is set to zero (0) seconds, the port link state will remain disabled until it is manually disabled and re-enabled or Port Link Flap Error Disable is disabled on this port.


## 1.1 Requirements
System shall be able to suppress interfaces state change events to protect system resources.
User shall be able to enable or disable the feature on individual interfaces and globally.
The feature must be disabled on all interfaces by default.
The feature shall be supported on physical interfaces.
There must be two sets of configuration parameters (sampling-interval, recovery-interval, and flap-threshold) a per-interface set and a global set. If both global and per-interface are configured, the per-interface values are used only for given interfaces. Global values are used for all other physical interfaces.
If no values are specified by user, a default set of parameters are applied to all interfaces.
User shall be able to save configuration parameters (both global and per-interface).
The configuration parameters (both global and per-interface) must be preserved across device reboot.

### 1.1.1 Functional Requirements
Port Link Flap Error Disable shall use below parameters to supress and protect system.
- flap-threshold
Specifies the number of times a port link state goes from up to down before the recovery wait period is activated. The value ranges from 1 through 50.
- sampling-interval
Specifies the amount of time, in seconds, during which the specified toggle threshold can occur before the recovery wait period is activated. The value ranges from 1 through 65535.
- recovery-interval
Specifies the amount of time in seconds, for which the port remains disabled (down) before it becomes enabled. The value ranges from 0 through 65534. A value of 0 indicates that the port will stay down until an administrative override occurs.

### 1.1.2 Configuration and Management Requirements
- Port Link Flap Error Disable feature default is OFF on all physical interfaces and port-channels
- When Port Link Flap Error Disable is enabled, use below default values:
  flap-threshold: 3
  sampling-interval: 10
  recovery-interval: 300
- User shall be able to specify different sampling-interval, flap-threshold and recovery-interval on a physical interface
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
### 2.1.1 Configuration Commands
- *link-error-disable flap-threshold <flap count> sampling-interval <interval in sec>  recovery-interval <recovery interval in sec>*
Example:
```
sonic(conf-if-Ethernet0)# link-error-disable flap-threshold 10 sampling-interval 3 recovery-interval 10
```
In this example, the values for the parameters are as follows:

The flap-threshold is set at 10 times. This interval is the number of times that the port's link state goes from up to down and down to up before the recovery-interval is activated. Enter a valid value range from 1-50. Default is 3.


The sampling-interval is set to 3 seconds. This time period is the amount of time during which the specified flap-threshold can be crossed. If the flap-threshold is crossed during this sampling-interval, port will be error-disabled. Enter a value between 1 and 65535 seconds. Default is 10.


The recovery-interval is set to 10 seconds. This period of time is the amount of time the port remains disabled (down) before it becomes enabled. Entering 0 indicates that the port will stay down until an administrative override occurs. Enter a value between 0 and 65534 seconds. Default is 300.


This config command can be executed on a range of interfaces as well. Example:
```
sonic(conf-if-range-eth**)# link-error-disable flap-threshold 10 sampling-interval 3 recovery-interval 10
```

The following command is used to enable the link-error-disable with default values for flap-threhsold, sampling-interval and recovery-interval:

```
sonic(conf-if-Ethernet0)#link-error-disable
```

This command to enable link-error-disable with default parameters is supported for the range of interfaces as well, as shown in the below example:

```
sonic(conf-if-range-eth**)# link-error-disable
```


Example for disabling link-flap error-disable on a port:
```
sonic(conf-if-Ethernet0)#no link-error-disable
```
This command shall be supported on interface range as well. Example:
```
sonic(conf-if-range-eth**)#no link-error-disable
```

- *[no] errdisable recovery cause link-flap*
This is a global command to enable the link-flap error-disable feature. This is an existing command tree and link-flap CLI node is added.

Link-flap feature has to be enabled globally to start this feature even when port level configurations are already present.
When the user executes 'no errdisable recovery cause link-flap' to disable the feature at system level, the current monitoring for link-flaps on all the ports will be stopped.

```
sonic(config)# errdisable recovery cause link-flap
sonic(config)# no errdisable recovery cause link-flap
```

### 2.1.2 Show Commands

"show errdisable recovery" is an existing CLI command. This output will be updated to list the ports in recovery period.
- *show errdisable recovery*
This command displays the err-disable recovery features. Link-flap is one among the features where in err-disable recovery option can be enabled.
Example:
```
sonic#show errdisable recovery
Err-Disable Reason    Timer Status
-----------------------------------
udld                  Disabled
bpduguard             Disabled
xcvrd                 Disabled
link-flap             Enabled

Interfaces that will be enabled at the next timeout:
Interface      Errdisable reason      Time left(sec)
-----------------------------------------------------
Ethernet0      link-flap              24
```
- *show errdisable link-flap*
Status and configuration details of link-flap error-disable is shown with this command.
The ports which does not have non-default error disable configurations will not be displayed in the output.

Example:
```
sonic#show errdisable link-flap
Interface  Flap-threshold  Sampling-interval   Recovery-interval Status
---------------------------------------------------------------------------
Ethernet0  10              3                   30                Errdisabled
Ethernet4  10              3                   60                On
Ethernet8  5               10                  300               Off
```

The possible status values are  
1. Errdisabled: The number of link flaps in a sampling interval crossed the threshold and port is currently in err-disabled state.  
2. On:  The err-disable is enabled, but number of flaps in sampling intervals has not cross the configured threshold.  
3. Off: The err-disable parameters are configured but it is not enabled.  

# 2.2 Functional Description

# 3 Design
## 3.1 Overview
## 3.2 DB Changes
### 3.2.1 CONFIG DB
For individual physcial interface
"PORT|Ethernet124":
{
    "error-disable": "on|off",
    "flap-threshold": "3",
    "sampling-interval": "5",
    "recovery-interval": "10"
},

### 3.2.2 APP DB
For individual physcial interface
"PORT_TABLE|Ethernet124":
{
    "error-disable": "on|off",
    "flap-threshold": "3",
    "sampling-interval": "5",
    "recovery-interval": "10"
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
    "flap-threshold": "3",
    "sampling-interval": "5",
    "recovery-interval": "10"
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
POST "<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable-port/port=<ifname>/link-flap"
Request body:
{
  "openconfig-errdisable-ext:link-flap": {
    "config": {
      "error-disable": <string>,
      "flap-threshold": <number>,
      "sampling-interval": <number>,
      "recovery-interval": <number>
    }
  }
}

Example:
```
POST "<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable-port/port=Ethernet24/link-flap"
{
  "openconfig-errdisable-ext:link-flap": {
    "config": {
      "error-disable": "on",
      "flap-threshold": 10,
      "sampling-interval": 20,
      "recovery-interval": 300
    }
  }
}
```

GET "<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable-port/port=<ifname>/link-flap/state"

Example:
```
GET "<REST-SERVER:PORT>/restconf/data/openconfig-errdisable-ext:errdisable-port/port=Ethernet24/link-flap/state"
Response data:
{
  "openconfig-errdisable-ext:state": {
    "time-left": 14
  }
}
```

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

