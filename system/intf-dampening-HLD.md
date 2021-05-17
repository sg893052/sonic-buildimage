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
  sample-interval: TBD
  toggle-frequency: TBD
  waiting-period: TBD
- User shall be able to specify different sample-interval, toggle-frequency and waiting-period on a physical interface
- User shall be able to display current Port Link Flap Error Disable confiuration values.
- User shall be able to display current interface status if it was surpresed by Port Link Flap Error Disable
- User shall be able to display Link-Down-Reason if a port is disabled by Port Link Flap Error Disable feature


### 1.1.3 Scalability Requirements
Port Link Flap Error Disable must be supported on all physcial ports. All the platform design, SDK, and ASIC constraints should be considered.


### 1.1.4 Warm Boot Requirements
Interfaces that are configured Port Link Flap Error Disable must be retained across warmboot.


