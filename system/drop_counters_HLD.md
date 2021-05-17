# Configurable Drop Counters in SONiC

# High Level Design Document
#### Rev 1.1

## Overview

The main goal of this feature is to provide better packet drop visibility in SONiC by providing a mechanism to count and classify packet drops that occur due to different reasons.

## 1 Top-level requirements

- This feature is enhancements/hardening of exisiting drop counter feature in SONiC
https://github.com/Azure/SONiC/blob/master/doc/drop_counters/drop_counters_HLD.md
- Add support for more drop counters to SAI.
- Klish support for drop counters feature in SONiC.
- Mirror the possible drop frames to CPU for analysis.

# 2 Requirements

## 2.1 Functional Requirements
1. CONFIG_DB can be configured to create debug counters
2. STATE_DB can be queried for debug counter capabilities
3. Users can access drop counter information via a Klish, GNMI, Rest interfaces.
    1. Users can see what capabilities are available to them
        1. Types of counters (i.e. port-level and/or switch-level)
        2. Number of counters
        3. Supported drop reasons
    2. Users can see what types of drops each configured counter contains
    3. Users can see which drop frames can be mirrored.
    4. Users can add and remove drop reasons from each counter
    5. Users can read the current value of each counter
    6. Users can assign aliases to counters
    7. Users can clear counters
    8. Users can capture mirrored dropped frames.

## 2.2 Configuration and Management Requirements
Configuration of the drop counters can be done via:
* config_db.json
* CLI

## 2.3 Scalability Requirements
Users must be able to use all debug counters and drop reasons provided by the underlying hardware.

Interacting with debug counters will not interfere with existing hardware counters (e.g. portstat). Likewise, interacting with existing hardware counters will not interfere with debug counter behavior.

## 2.4 Supported Debug Counters
* PORT_INGRESS_DROPS: port-level ingress drop counters
* PORT_EGRESS_DROPS: port-level egress drop counters
* SWITCH_INGRESS_DROPS: switch-level ingress drop counters
* SWITCH_EGRESS_DROPS: switch-level egress drop counters
