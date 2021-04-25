# SONiC Auto-Negotiation and Link Training

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
| 0.1 | 04/15/2021  | Dante (Kuo-Jung) Su | Initial version                    |

# About this Manual

This document provides functional requirements and high-level design for providing Auto-Negotiation and Link Training support in SONiC.

#  References

##  Table 1: References

|       **Document**                                       | **Location**   |
| -------------------------------------------------------- | -------------- |
| Auto-Negotiation (AN) Overview                           | https://www.ieee802.org/3/by/public/Mar15/booth_3by_01_0315.pdf |
| Auto-Negotiation - What is it and how it fits into 1TPCE | https://www.ieee802.org/3/1TPCESG/public/Lo_02_0514.pdf |
| O’Reilly Ethernet: The Definitive Guide, 2nd Edition     | https://www.oreilly.com/library/view/ethernet-the-definitive/9781449362980/ch05.html |
| PAM-4 Auto-Negotiation & Link Training White Paper       | https://xenanetworks.com/whitepaper/autoneg-link-training/ |

# Scope

This document describes the high level design of Auto-Negotiation and Link Training support in SONiC.

# Definition/Abbreviation

## Table 2: Abbreviations

| **Term**     | **Meaning**                                                        |
| ------------ | ------------------------------------------------------------------ |
| AN           | Auto-Negotiation protocol                                          |
| CDR          | Optical clock and data recovery circuit                            |
| CLI          | Command-Line Interface                                             |
| LT           | Link Training                                                      |
| NRZ          | Non-Return-to-Zero, also called Pulse Amplitude Modulation 2-level |
| PAM4         | Pulse Amplitude Modulation 4-level                                 |
| SAI          | Switch Abstraction Interface                                       |
| SONiC        | Software for Open Networking in the Cloud                          |
| SWSS         | SWitch State Service                                               |

# 1 Requirements Overview

Automatic configuration of Ethernet interfaces over twisted-pair links and one fiber 
optic media type is provided by the Auto-Negotiation protocol. Clause 28 for the twisted-pair
links, Clause 37 for the 1000BASE-X fiber optic link, and Clause 73 for backplane/copper links >= 1G.
The Auto-Negotiation system ensures that devices at each end of a link can automatically negotiate their
configuration to the highest set of common capabilities.

Link Training is another process that can take place when a device is connected to a high-speed Ethernet
port through a copper cable or backplane. In this case it is important that the characteristics of the 
transmitted signal are tuned to be optimally carried over the copper cable.

Without Link Training support, the SONiC platforms will have to provide a set of statically tuned
tx signal parameters, it's neither scale and nor available on all of the SONiC platforms, and hence,
it's nice to introduce the Link Training support into SONiC.

## 1.1 Functional Requirements

Link Training could be enabled with or without AN Clause 73, it's performed after the speed is negotiated,
and hence, AN does not guarantee a compatible mode of operation will result in a link being established or
maintained.

- The AN and LT should be configurable individually from the CLI.
- The AN should be disabled by default for the best backward compatibility.
- The LT should be enabled by default to eliminate the need of platform-specific statically tuned parameters.
- The LT should be disabled by default if media_settings.json is available in the target platform.
- When the AN is explicitly enabled via the CLI, it shall automatically update the advertised capabilities as per the transceiver attached.
- When the LT is explicitly enabled via the CLI, it shall automatically activated on PAM4 link and de-activated in the case of NRZ mode.
- When the LT is explicitly disabled via the CLI, it shall never be activated.
- The LT should be de-activated when CDR is not available on the attached transceiver.

## 1.2 Configuration and Management Requirements

Several CLI configuration commands support this feature. The commands are available in
interface config mode.

- A CLI show command lists the per-interface AN and LT configuration and status.
- A set of CLI config commands to configure the per-interface AN and LT configuration.

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
