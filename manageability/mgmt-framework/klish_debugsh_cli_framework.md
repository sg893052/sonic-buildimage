# Feature Name
KLISH Debug Shell CLI framework
# High Level Design Document
#### Rev 0.3

# Revision

# About this Manual
This document provides general information about the executing [debugsh](#debugsh) CLI commands from sonic-cli/[KLISH](#KLISH).

# Scope
This document describes the high level design of providing infra structure for running  debugsh CLI  commands from sonic-cli. 

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term** | **Meaning**                  |
| -------- | ---------------------------- |
| CLI      | Command Line Interface       |
| KLISH    | Kommand Line Interface Shell |
| debugsh  | SONiC Debug Shell            |

# Feature Overview

This feature provides the framework for integrating backend modules debug CLI commands with sonic-cli and the [Management Framework](#management-framework).

Currently debugsh supports 2 kinds of debug commands

* debugsh commands supported

  * Backend or remote commands : Used to dump data stored in the heap memory of the backends. This information is typically not available in the redis DB. These commands will be supported in sonic-cli.

* debugsh commands not supported

  * Local or flex CLIs : These commands are used to gather information from various sources. For example backend, redis database, Linux commands, and the SDK shell. The information is then processed to show user friendly output like consistency checking, DB to Hardware mapping etc. These commands will not be supported.

**Only the debugsh backend commands are to be support in sonic-cli, and Management Framework.**

# Requirements

## Functional Requirements

Execute backend modules debugsh CLI commands from sonic-cli.

## Configuration and Management Requirements
N/A

## Scalability Requirements
N/A

## Warm Boot Requirements
N/A

# Design

## Overview

debugsh uses a PUB-SUB mechanism to communicate with the backends to build its CLI tree, and execute commands with the backends. In a similar fashion, the [Management Framework](#management-framework) common app module, with transformer callback annotations, will implement Yang RPCs to communicate with the backends. The KLISH will use the Yang RPC RESTConf operations to execute commands.

Please see the [Flow Diagram](#flow-diagram).

## DB Changes
No planned DB Schema Changes

## User Interface

### Data Models

Outline of RPCs to be implemented

```
sonic-debugsh.yang

    rpc help
      input: tokens               (Eg: show system internal)
      output: hierarchical help   (Eg:
                                       Possible completions:
                                       aclsvcd     ACL Services daemon
                                       ...
                                       vxlanmgr    vxlanmgr related commands
                                  )
    rpc exec
      input: tokens               (Eg: show system internal vxlanmgr global)
      output: list of strings
                       (Eg:

                            VxlanHwCapability Enable 
                           Feature Capability: Vxlan(Enable) Vlan_Vni_Num(4096)

                            ============== Pending Request Count===============
                            tunnel_map:0 nvo:0 tunnel:0 vnet:0
                       )

    rpc rebuild                    (Rebuilds the debugsh CLI tree cache)
      input: {}
      output: {}
    rpc option                     (Set debugsh protocol specific options)
      input: optionName optionVal  (Eg: "timeout" "2")
      output: {}

```

### CLI

The debugsh CLI tree is automatically generated/cached in the REST server using the commands that are dynamically obtained from the backend. 

#### Debug Commands

* **debugsh show system internal (...|-h)**
* **debugsh debug system internal (...|-h)**
* **debugsh clear system internal (...|-h)**

* **debugsh show system internal debugsh backend cmdtree (...|-h)**

* **debugsh option timeout <1-180>** (Set the backend communication timeout)


For example:

```
sonic-cli# debugsh show system internal -h
Possible completions:
aclsvcd       ACL Services daemon
coppmgr       CoppMgr daemon
debugsh       Debug Shell command
fdbsync       Fdbsync Services daemon
intfmgr       intfmgr related commands
nbrmgrd       nbrmgrd related commands
orchagent     Orchagent related commands
sagmgr        sagmgr related commands
sai           SAI Debug commands
stpmgr        stpmgr related commands
syncd         Syncd related commands
teammgr       TeamMgr related commands
vxlanmgr      vxlanmgr related commands
sonic-cli#
```

```
sonic-cli# show system internal vxlanmgr global 

 VxlanHwCapability Enable 
Feature Capability: Vxlan(Enable) Vlan_Vni_Num(4096)

 ============== Pending Request Count===============
 tunnel_map:0 nvo:0 tunnel:0 vnet:0
sonic-cli#
```

```
sonic-cli# debugsh debug system internal logger aclsvcd level info 
sonic-cli#
```

```
sonic-cli# debugsh clear system internal vlan ifp
sonic-cli# 
```

```
sonic-cli# debugsh show system internal debugsh backend cmdtree vrrpmgrd
*
|   +---show
|   |   +---system
|   |   |   +---internal
|   |   |   |   +---logger
|   |   |   |   |   +---vrrpmgrd
|   |   |   |   +---debugsh
|   |   |   |   |   +---backend
|   |   |   |   |   |   +---cmdtree
|   |   |   |   |   |   |   +---vrrpmgrd
|   |   |   |   |   |   |   |   +---detail
|   +---debug
|   |   +---system
|   |   |   +---internal
|   |   |   |   +---logger
|   |   |   |   |   +---vrrpmgrd
|   |   |   |   |   |   +---level
|   |   |   |   |   |   |   +---debug
|   |   |   |   |   |   |   +---info
|   |   |   |   |   |   |   +---notice
|   |   |   |   |   |   |   +---warn
|   |   |   |   |   |   |   +---error
|   |   |   |   |   |   |   +---crit
|   |   |   |   |   |   |   +---emerg
sonic-cli#
```

```
sonic-cli# debugsh option timeout 2
sonic-cli#
```

#### IS-CLI Compliance
debugsh is a SONiC specific implementation, thus there is no need for IS-CLI compliance


### REST API Support
See [Data Models](#data-models)

# Flow Diagrams

```

User  sonic-cli#       REST Server           Redis          Backend
____  __________       ___________           _____          _______

 o   (1)
-+-  CLI
 |  ----> .        
/ \       .     (2)       
          .     RPC       
          .     exec      
          . ------------> .
          .               .         (3)
          .               .       Publish
          .               . -----------------> .
          .               .                    .
          .               . --+                .
          .               .   |  (4)           .
          .               .   |  Wait          .
          .               .   |for o/p         .
          .               ..<-+                .
          .               ..                   .
          .               ..                   .      (5)
          .               ..                   .    Sub. Msg.
          .               ..                   . ------------> .
          .               ..                                   .
          .               ..                          (6)      .
          .               ..                        Publish    .
          .               ..                   . <------------ .
          .               ..                   .     o/p 1     .
          .               ..       (7)         .               .
          .               ..     Sub. Msg.     .               .
          .               ...<---------------- .               .
          .               ...     o/p 1                        .
          .               ...                                  .
          .     (8)       ...                                  .
          .    Chunk      ...                                  .
          . <------------ ...                                  .
          .    o/p 1      ..                                   .
          .               ..                                   .
     (9)  .               ..                                   .
    <---- .               ..                                   .
    o/p 1 .               ..                                   .
          .               ..                                   .
          .               ..                          (10)     .
          .               ..                        Publish    .
          .               ..                   . <------------ .
          .               ..                   .     o/p 2     .
          .               ..       (11)        .               .
          .               ..     Sub. Msg.     .               .
          .               ...<---------------- .               .
          .               ...     o/p 2                        .
          .               ...                                  .
          .     (12)      ...                                  .
          .    Chunk      ...                                  .
          . <------------ ...                                  .
          .    o/p 2      ..                                   .
          .               ..                                   .
     (13) .               ..                                   .
    <---- .               ..                                   .
    o/p 2 .               ..                                   .
          .               ..                                   .


                                 ...

          .               ..                                   .
          .               ..                         (n-3)     .
          .               ..                        Publish    .
          .               ..                   . <------------ .
          .               ..                   .     END
          .               ..      (n-2)        .
          .               ..     Sub. Msg.     .
          .               ...<---------------- .
          .               ...     END
          .               ...
          .    (n-1)      ...
          .    Chunk      ...
          . <------------ ...
          .
          .
     (n)  .
    <---- .


```

Notes:

- The o/p returned to the sonic-cli could be sent either buffered or in chunks

# Error Handling
N/A

# Serviceability and Debug
N/A

# Warm Boot Support
N/A

# Scalability
N/A

# Limitations

- Only the debugsh backend commands are to be supported in sonic-cli.
- KLISH Auto-complete (i.e. tab autocompletion) will not work since these commands are being dynamically retrieved by the REST common app module transformer callback, and are unknown to the KLISH. This is in line with other emulations of bash command, like ping, which provide the "-h" option to obtain completion information.

# Unit Test

- REST Yang RPC (help, exec, rebuild, option)

- KLISH         (show, debug, clear, option )

# References

## Management Framework

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md

## KLISH

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md#3221-cli

## debugsh

https://docs.google.com/document/d/1kMpJ2k4CIBn-08TTKlYqxTBlDrmVNg5El7htcJ5sPxc

"Debugsh provides a mechanism to display backend data which is typically not available in the database. It can also be used to enable certain debug features during runtime on need basis like enabling some debug prints, debug checks etc."


