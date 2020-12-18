# Chassis Beacon LED

## High Level Design Document
**Rev 0.1**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Feature Overview](#feature-overview)
  * [Functional Requirements](#functional-requirements)
* [CLI](#cli)
* [KLISH CLI](#klish-cli)
* [Unit Test](#unit-test)


# Revision

Rev   |   Date   |  Author   | Change Description
:---: | :-----:  | :------:  | :---------
1.0   | 12/16/20 | Precy Lee | Initial version


# About this Manual

This document describes the Chassis Beacon LED Feature in the SONiC Network Operating System(NOS). This feature helps an user to identify a particular switch in a lab or data center setting.

# Scope

This document describes the high level design of Chassis Beacon LED feature. It describes the CLICK and KLISH commands used to enable, disable, and show status of Chassis Beacon LED. 

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term**     |  **Meaning**                  |
|:-------------|:------------------------------|
| LOC LED      | Locator LED                   |

# Feature Overview 

Chassis Beacon LED feature provides a administrator to identiy a particular switch among others. Chassis Beacon LED (or Locator LED) is typically located on the front panel of a switch. The LED status provides a visual indication for the location of a switch. An adminstrator can execute commands to enable, disable, or show the Beacon/LOC LED.      

## Functional Requirements

Functional requirements include capabilities of operating the Chassis Beacon LED via CLI and KLISH to

 - Enable Chassis Beacon LED: Turn on the LED. The color pattern of the LED is platform specific.     
 - Disable Chassis Beacon LED: Turn off the LED. 
 - Show the State Chassis Beacon LED: Find out the status of the LED, on or off. 

Not every platform supports Beacon LED. Please refer to the [platform-list](https://docs.google.com/spreadsheets/d/1NuhoA2utcDPIIiM6dwrAY8FWrX_21KDfcR3Fw_OEwyE/edit#gid=0) that supports Beacon LED in this release. 


# CLI:

**Enable Chassis Beacon LED**

 This command runs at root privileges. 

- Examples on supported platforms:
  ```
  root:~$ chassis beacon on
  Success

  root:~$ chassis beacon on
  Failed 

  ```

- Examples on non-supported platforms:
  ```
  root:~$ chassis beacon on
  Not supported

  ```

**Disable Chassis Beacon LED**

 This command runs at root privileges. 

- Examples on supported platforms:
  ```
  root:~$ chassis beacon off 
  Success

  root:~$ chassis beacon off 
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  root:~$ chassis beacon off 
  Not supported

  ```

**Show Chassis Beacon LED**

 This command runs at root/admin privileges. 

- Examples on supported platforms:
  ```
  admin:~$ show chassis beacon off 
  green 
  
  Note: Chassis Beacon LED color is platform specific. The output of color can vary 

  ```

- Examples on unsupported platforms:
  ```
  admin:~$ show chassis beacon 
  Not supported

  ```
# KLISH CLI:

**Enable Chassis Beacon LED**

- Examples on supported platforms:
  ```
  sonic# chassis beacon on
  Success

  sonic# chassis beacon on
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  sonic# chassis beacon on
  Not supported

  ```

**Disable Chassis Beacon LED**


- Examples on supported platforms:
  ```
  sonic# chassis beacon off 
  Success

  sonic# chassis beacon off 
  Failed 

  ```

- Examples on unsupported platforms:
  ```
  sonic# chassis beacon off 
  Not supported

  ```

**Show Chassis Beacon LED**


- Examples on supported platforms:
  ```
  sonic# show chassis beacon off 
  green 
  
  Note: Chassis Beacon LED color is platform specific. The output of color can vary 

  ```

- Examples on non-supported platforms:
  ```
  sonic# show chassis beacon 
  Not supported


## SONiC image NetInstall

- SONiC image netinstall clears up the Chassis Beacon Led operation.  


## SONiC image upgrade 

- SONiC image upgrade clears up the Chassis Beacon Led operation.  

## System Reboot
- System reboot clears up the Chassis Beacon Led operation.  

# Unit Test

Run each unit test on both Beacon LED supported platforms and unsupported platforms. 

  | SNO |  Unit Testcase 
 :------| :----------------------------------------------------
    1   | CLICK: Enable Chassis Beacon LED       
    2   | CLICK: Disable Chassis Beacon LED  
    3   | CLICK: Show Status of Chassis Beacon LED   
    4   | KLISH: Enable Chassis Beacon LED  
    5   | KLISH: Disable Chassis Beacon LED 
    6   | KLISH: Show Status of Chassis Beacon LED 



