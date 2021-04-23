# SONIC Packet Path Optimization using XDP framework

## High Level Design Document

### Rev 0.1

# Table of Contents

* [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definition-abbreviation)
  * [Requirements](#1-Requirements-Overview)
  * [Functionality](#2-Functionality)
  * [Design](#3-Design)
  * [Flow Diagrams](#4-Flow-Diagrams)
  * [Serviceability and Debug](#5-Serviceability-and-Debug)
  * [Warm Boot Support](#6-Warm-Boot-Support)
  * [Scalability](#7-Scalability)
  * [Unit Test](#8-Unit-Test)

# List of Tables

[Table 1: References](#table-1-references)<br>
[Table 2: Abbreviations](#table-2-abbreviations)

# Revision

| Rev |     Date    |       Author        | Change Description                 |
|:---:|:-----------:|:-------------------:|:-----------------------------------|
| 0.1 | 04/23/2021  | Kalimuthu           | Initial version                    |

# About this Manual

This document provides functional requirements and high-level design for optimize the Packet Path in SONIC using XDP framework.

#  References

##  Table 1: References

|       **Document**  |           **Location**                                           |
| --------------------| ---------------------------------------------------------------- |
| XDP                 | https://prototype-kernel.readthedocs.io/en/latest/networking/XDP/|
| eBPF                | https://prototype-kernel.readthedocs.io/en/latest/bpf/index.html |

# Scope

This document describes the high level design of Packet Path Optimization using XDP framework in SONiC.

# Definition/Abbreviation

## Table 2: Abbreviations

| **Term**     | **Meaning**                                                        |
| ------------ | ------------------------------------------------------------------ |
| XDP          | Express Data Path                                                  |
| eBPF         | extended Berkeley Packet Filter                                    |
| SAI          | Switch Abstraction Interface                                       |
| SONiC        | Software for Open Networking in the Cloud                          |
| SWSS         | SWitch State Service                                               |

# 1 Requirements Overview

Xdp provides a bare metal packet processing at the lowest level. The xdp programs are written 
as an ebpf program that will be attached to the driver hook. Using a user space driver the user
is allowed to directly read or make changes to network packet data and take decisions on how to
handle the packet at an earlier stage with the attached xdp program so that the kernel stack can
be eliminated from the data path hence avoiding overheads like converting the packets to skbs
context switch costs etc. Basically introduces a new programmable layer in the kernel stack 
intended to close the performance gap between kernel bypass solutions.

In many scenarios the packets don't have to travel through all the layers in order to reach the
decision on what to do about the packet. The introduction of the architecture is aimed at making
the decision at an earlier stage packet processing so that it can be done faster by eliminating
much of the overheads in the usual kernel stack.

Express data path is a new addition to the linux kernel which is intended to speed up the packet
processing or network data processing in linux kernel. This is enabled by using the ebpf virtual
machine infrastructure which is available in the kernel for many other purposes already. XDP make
use of a user space c(like) program which manages actions of a kernel space program also written
in c that would be attached to the driver hook. Making it possible to make decisions at early stages
of packet processing and hence eliminating many over heads involved in kernel network stack.

## 1.1 Functional Requirements

There are many networking protocols that are running in the SONiC system where some are very timing
sensitive protocols like BFD, LLDP, UDLD etc. These protocol packets are mainly processed in the
application space, but still some of the processing is done in the kernel space IP stack which is unnecessary.
The XDP framework helps to optimize the packet flow by filtering the packet in the kernel at very early
stage and takes the necessary action as to pass the packet to application in the userspace directly or 
drop it in the kernel space itself. 

- Should provide a user space interface that are generic to SONIC applications.
- Support for early Rx packet processing without allocating SKBUFFs and software queues.
- Framework should support packet parsing, table lookups, create/manages stateful filters and packet manipulations.
- Should support both native XDP and generic XDP.
- Framework should support RX processing with SMP support.
- Should provide a framework to enhance the performance of Tx/Rx packet flow, flow sampling and monitoring.
- Extendable to initiate the Tx packet from the kernel space.
- Framework should support XDP statistical support.
- Should support Forwarding and load balancing.
- Batching techniques such as in Generic Receive Offload.


## 1.2 Configuration and Management Requirements
 NA
# 2 Functionality

## 2.1 Target Deployment Use Cases

TBD

# 3 Design

## 3.1 Overview

TBD

## 3.2 Database

TBD

## 3.3 Data Model

TBD

# 4 Flow Diagrams

None

# 5 Serviceability and Debug

TBD

# 6 Warm Boot Support

TBD

# 7 Scalability

TBD

# 8 Unit Test

TBD

# 9 Internal Design Information

TBD
