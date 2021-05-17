# Feature Name
Portgroup
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
| 0.1 | 08/11/2020  |   Steven Lu        | Initial version                   |

# About this Manual
This document provides general information about the portgroup feature implementation in SONiC.

# Scope
This document describes the high level design of portgroup feature.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
|                          |                                     |

# 1 Feature Overview
Some platforms have limitation that certain ports have to be setup at same speed together. The ports for which the speed has to be set together are grouped into multiple port groups, for the platforms that have this limitation. The portgroup configuration command can be used to configure speed of ports in same portgroup, and interface configuration command will not able to use.


## 1.1 Requirements

### 1.1.1 Functional Requirements
- User shall be able to configure a group of ports' speed together using portgroup command
- User shall be able to display which ports belong to a portgroup
- User shall be able to configure "mixed" speed within a portgroup if silicon allows.


## 1.2 Design Overview
### 1.2.1 Basic Approach
Add "port-group" section in platform-def.json file to define portgroup member ports and speed capabilties.
The front-end CLI need check against "port-group" section in platform-def.json to determine which speed is allowed in the portgroup it current platform has portgroup define. And reject speeds not supported in the portgroup.


# 3 Design
## 3.1 Overview
For platforms need portgroup. Section "port-group" need added to platform-def.json file to define portgroup information.

    ```
    "port-group": {
        "1": {
            "members": "Ethernet0-11",
            "valid_speeds": ["25000",["10000","1000"]]
        },
        "2": {
            "members": "Ethernet12-23",
            "valid_speeds": ["25000",["10000","1000"]]
        },
        "3": {
            "members": "Ethernet24-35",
            "valid_speeds": ["25000",["10000","1000"]]
        },
        "4": {
            "members": "Ethernet36-47",
            "valid_speeds": ["25000",["10000","1000"]]
        }
    }
    ```
In "port-group" section, it defines:
- each portgroup
- member ports belong to a portgroup
  member ports can be list individualy like
     ```
     "Ethernet0,Ethernet1,Ethernet3,Etherne4"
     ```
  or use range like
     ```
     "Ethernet36-47"
     ```
- valid speeds of each portgroup.
  "valid_speeds" lists valid speeds in a list like
     ```
     ["speed1","speed2",...]
     ```
  If there are cases that "mixed" speed can be supported, it can be nested like below:
     ```
     ["speed1", ["speed2", "speed3"]]
     ```
  it means that this portgroup can be switched to speed1 and speed2. And when in speed2, individual port speed can be changed to speed3.

## 3.2 User Interface
### 3.2.1 CLI
#### 3.2.1.1 Configuration Commands
This command configures portgroup speed.
- Usage:
    config portgroup speed <portgroup> <speed>

- Example:
  ```
  admin@sonic:~$ sudo config portgroup speed 1 10000
  Config portgroup 1 speed 10000
  ```

#### 3.2.1.2 Show Commands

This command displays portgroup information of current platform. It displays each portgroup in current platform and ports belong to each portgroup and valid speeds for each portgroup.

- Usage:
    show portgroup

- Example:
  For platform does not have portgroup
  ```
  admin@sonic:~/logs$ show portgroup
  Platform does not support portgroup.
  ```

  For platform does have portgroup
  ```
  admin@sonic:~$ show portgroup
    portgroup          ports          valid speeds
  -----------  -------------  --------------------
            1   Ethernet0-11  25000, [10000, 1000]
            2  Ethernet12-23  25000, [10000, 1000]
            3  Ethernet24-35  25000, [10000, 1000]
            4  Ethernet36-47  25000, [10000, 1000]
  ```
  25000, [10000, 1000] means that port group support 25000, 10000 and 1000 speeds.
  [10000, 1000] means individual ports within that portgroup can change speed between 10000/1000 using 'config interface speed' command.

