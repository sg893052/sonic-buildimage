
# Port Access Control in SONiC

High level design document version 0.1

# Table of Contents
- **[List of Tables](#list-of-tables)**
- **[Revision](#revision)**
- **[About this Manual](#about-this-manual)**
- **[Scope](#scope)**
- **[Definition / Abbreviation](#definition--abbreviation)**
	- [Table 1 Abbreviations](#table-1-abbreviations)
- **[1 Feature Overview](#1-feature-overview)**
	- [1.1 Port Access Control](#11-port-access-control)
		- [1.1.1 Dot1x](#111-dot1x)
		- [1.1.2 MAC Authentication Bypass](#112-mac-autentication-bypass)
	- [1.3 Requirements](#13-requirements)
		- [1.3.1 Functional Requirements](#131-functional-requirements)
		- [1.3.2 Configuration and Management Requirements](#132-configuration-and-management-requirements)
		- [1.3.3 Scalability Requirements](#133-scalability-requirements)
		- [1.3.4 Warm Boot Requirements](#134-warm-boot-requirements)
	- [1.4 Design Overview](#14-design-overview)
		- [1.4.1 Basic Approach](#141-basic-approach)
		- [1.4.2 Container](#142-container)
		- [1.4.3 SAI Support](#143-sai-support)
- **[2 Functionality](#2-functionality)**
	- [2.1 Target Deployment Use Cases](#21-target-deployment-use-cases)
	- [2.2 Functional Description](#22-functional-description)
- **[3 Design](#3-design)**
	- [3.1 Overview](#31-overview)
	- [3.2 DB Changes](#32-db-changes)
		- [3.2.1 Config DB](#321-config-db)
		- [3.2.2 App DB](#322-app-db)
		- [3.2.3 State DB](#323-state-db)
		- [3.2.4 ASIC DB](#324-asic-db)
		- [3.2.5 Counter DB](#325-counter-db)
	- [3.3 Switch State Service Design](#33-switch-state-service-design)
		- [3.3.1 Orchestration Agent](#331-orchestration-agent)
		- [3.3.2 PAC daemons](#332-pac-daemons)
			- [3.3.2.1 PAC Manager](#3321-pac-manager)
			- [3.3.2.2 Authentication Manager](#3322-authentication-manager)
		- [3.3.3 Other Process](#333-other-process)
	- [3.4 SyncD](#34-syncd)
	- [3.5 SAI](#35-sai)
	- [3.6 Manageability](#36-manageability)
		- [3.6.1 Data Models](#361-data-models)
		- [3.6.2 Configuration Commands](#362-configuration-commands)
		- [3.6.3 Show Commands](#363-show-commands)
- **[4 Flow Diagrams](#4-flow-diagrams)**
- **[5 Error Handling](#5-error-handling)**
- **[6 Serviceability and Debug](#6-serviceability-and-debug)**
- **[7 Warm Boot Support](#7-warm-boot-support)**
- **[8 Scalability](#8-scalability)**
	- [8.1 Software scalability](#81-software-scalability)
- **[9 Limitation](#9-limitation)**
- **[10 Upgrade / Downgrade considerations](#10-upgrade--downgrade-considerations)**
- **[11 Unit Test](#11-unit-test)**
- **[12 Appendix: Sample configuration](#12-appendix-sample-configuration)**
- **[13 Internal Design Information](#13-internal-design-information)**
	- [13.1 Future Design Enhancements](#131-future-design-enhancements)

# List of Tables
[Table 1 Abbreviations](#table-1-abbreviations)

# Revision
| Rev  | Date       | Author   | Change Description |
| ---- | ---------- | ---------| ------------------ |
| 0.1  | 02/03/2021 | Prabhu Sreenivasan, Amitabha Sen | Initial version |

# About this Manual
This document provides general information about the Port Access Control feature in SONiC.

# Scope
This document provides general information about the Port Access Control feature implementation in SONiC.

# Definition / Abbreviation

## Table 1 Abbreviations
| **Term** | **Meaning**          |
| -------- | -------------------- |
| CoPP     | Control Plane Policing |
| Dot1x    | IEEE 802.1 standard  |
| EAPOL    | Extensible Authentication Protocol over LAN  |
| MAB      | MAC Authentication Bypass|
| PAC      | Port Access Control  |
| RADIUS   | Remote Authentication Dial In User service |
| SONiC-CLI | Klish CLI used by management framework |

# 1 Feature Overview

## 1.1 Port Access Control
Port Access Control (PAC) feature provides validation of client and user credentials to prevent unauthorized access to a specified switch port.

Local Area Networks (LANs) are often deployed in environments that permit unauthorized devices to be physically attached to the LAN infrastructure, or permit unauthorized users to attempt to access the LAN through equipment already attached. In such environments, it may be desirable to restrict access to the services offered by the LAN to those users and devices that are permitted to use those services.  Port access control makes use of the physical characteristics of LAN infrastructures in order to provide a means of authenticating and authorizing devices attached to a LAN port that has point-to-point connection characteristics and of preventing access to that port in cases in which the authentication and authorization process fails. In this context, a port is a single point of attachment to the LAN, such as Ports of MAC bridges and associations between stations or access points in IEEE 802.11 Wireless LANs.

### 1.1.1 Dot1x 

IEEE 802.1X is an IEEE Standard for Port Access Control (PAC) that provides an authentication mechanism to devices wishing to attach to a LAN. The standard defines Extensible Authentication Protocol Over LAN (EAPOL). The 802.1X standard describes an architectural framework within with authentication and consequent actions take place. It also establishes the requirements for a protocol between the authenticator and the supplicant, as well as between the authenticator and the authentication server. 

### 1.1.2 MAC Authentication Bypass
Simple devices like camera or printers which do not support 802.1x autentication can make use of MAB feature where the device gets authenticated based on the device MAC address.

## 1.3 Requirements

### 1.3.1 Functional Requirements
The following are the requirements for Port Access Control feature:
1. 802.1X is supported on physical interfaces only.
2. The interfaces cannot be part of a port-channel.
3. 802.1X is not supported on service port.
4. One can configure 802.1X interfaces for bridges using the following features:
   Parking VLAN.
   Dynamic VLAN.
   MAB (MAC-based authentication bypass).
5. MAB, parking VLAN, and dynamic VLAN all require a bridge access port.
6. In traditional bridge mode, parking VLANs and dynamic VLANs both require the destination bridge to have a parking VLAN ID or dynamic VLAN ID tagged subinterface.
7. When you enable or disable 802.1X on ports, hostapd reloads; however, existing authorized sessions do not reset.
8. Changing the 802.1X interface, MAB, or parking VLAN settings do not reset existing authorized user ports. However, removing all 802.1X interfaces or changing any of the following RADIUS parameters restarts hostapd, which forces existing, authorized users to re-authenticate:
   - RADIUS server IP address, shared secret, authentication port or accounting port.
   - Parking VLAN ID.
   - MAB activation delay.
   - EAP re authentication period.
9. You can configure up to three RADIUS servers (in case of failover). However, do not use a SONiC switch as the RADIUS server.
10. You can configure 802.1X interfaces with dynamic ACLs in VLAN-aware bridge mode only.
Note: 802.1X on SONiC to be tested with only a few wpa_supplicant (Debian), Windows 10 and Windows 7 supplicants.
11. RADIUS authentication is supported with FreeRADIUS and Cisco ISE.
12. 802.1X supports simple login and password, PEAP/MSCHAPv2 (Win7) and EAP-TLS (Debian).
13. 802.1X supports RFC 5281 for EAP-TTLS, which provides more secure transport layer security.
14. Mako template-based configurations are not supported.
15. SONiC supports Multi Domain Authentication (MDA), where 802.1X is extended to allow authorization of multiple devices (a data and a voice device) on a single port and assign different VLANs to the devices based on authorization.
    - MDA is enabled by default; however, you need to assign a tagged VLAN for voice devices (see Configure 802.1X Interfaces for a VLAN-aware Bridge).
    - A maximum of four authorized devices (MAB + EAPOL) per port are supported.
    - The 802.1X-enabled port must be a trunk port to allow tagged voice traffic from a phone; you cannot enable 802.1X on an access port.
    - Only one untagged VLAN and one tagged VLAN is supported on the 802.1X enabled ports.
    - Multiple MAB (non voice) devices on a port are supported for VLAN-aware bridges only. Authorization of multiple MAB devices for different VLANs is not supported.



The following are the requirements for MAB feature:
1. A port can be configured to be either doing port-based authentication or doing Mac-based authentication. But only one mode at a given instance.
2. A port when enabled for Dot1x authentication needs to explicitly mention whether it is being enabled for port-based authentication mode or Mac-based authentication mode.
3. When a port is configured for Mac-based Dot1x Authentication, then if at least one client has actively authentication itself then port is placed in authorized state for other components.
4. When a port is configured for Mac-based Dot1x Authentication, then port is placed in un-authorized state if the last active authorized client ceases to exist.
5. When a port is operational in Mac-based Dot1x Authentication, then only the data traffic arriving on the port from authorized clients need to be forwarded.
6. When a port is operational in Mac-based Dot1x Authentication, accept EAPOL packets from all the clients (both authorized and unauthorized as well).
7. When a port is enabled for Guest vlan, then Mac-based dot1x authentication can not be enabled on that port.
8. When a port is enabled for vlan assignment, then Mac-based dot1x authentication could also be enabled on that port. As the vlan assignment is based on the user details of the client.
9. To enable Mac-based dot1x authentication on port it needs to be an Edge port. (Dot1s Configuration).
10. If Mac-based dot1x authentication is enabled on a port and if Mac-based VLANs or Subnet VLANs or Protocol VLANs are also enabled, then Mac-based dot1x authentication is disabled.
11. If Port-Mac Locking is enabled on the port and if Mac-based dot1x authentication is also enabled on the port. Then Port-Mac Locking needs to be disabled.
12. If a client already authentication on one port has roamed to another port on the switch, then the state (status, vlan etc) of the client on the prior port needs to be cleaned.
13. Timeout mechanism needs to be maintained per client so that inactive client.s status can be removed on the switch.
14. Multicast/Broadcast traffic arriving on the port that is enabled for Mac-based authentication would be forwarded accordingly. If the source Mac-address of the packet is from a authenticated user.
15. Even when the port is authorized, dot1x would still continue to send EAPOL for authentication as more users could join on the port.
16. Unauthenticated Vlan is per port configuration. By default unauthenticated vlan is zero (invalid and not operational).
17. In port-based dot1x, if the client fails authentication then port is placed in the unauthenticated vlan (if configured)
18. In mac-based dot1x, if the client fails authentication then client-mac is associated to the unauthenticated vlan (if configured).
19. Unauthenticated vlan deprecates the Guest vlan feature of supplicant mode, as it would be redundant. 

### 1.3.2 Configuration and Management Requirements

### 1.3.3 Scalability Requirements

### 1.3.4 Warm Boot Requirements
Port Access Control feature should work seamlessly across warmboot. Statistics must be preserved across warmboot.

## 1.4 Design Overview

### 1.4.1 Basic Approach

### 1.4.2 Container
A new container sonic-security is introduced to hold all the port security applications. Apart from sonic-security container, code changes are made to SWSS container.
 
### 1.4.3 SAI Overview


# 2 Functionality

## 2.1 Target Deployment Use Cases

When a client authenticates itself initially on the network, the Switch acts as the authenticator to the clients on the network and forwards the authentication request to the Radius server in the network. If the authentication succeeds then the port is placed in authorized state and the client is able to forward or receive traffic through the port.

In a standard dot1x scenario, all the subsequent clients in the network that are connected to the same port need not authenticate to utilize the port on the switch. When Mac-based dot1x authentication is enabled, all the subsequent clients in the network that are connected to the same port need to authenticate themselves to utilize the port on the switch.

If the vlan assignment is enabled in the Radius server then as part of the response message Radius server sends the vlan id the client is supposed to be in the 802.1x tunnel attributes. In the diagram above, the switch has placed three clients belonging to same port in the different vlans (vlan10 and vlan20) based on the user details of the clients. This implies that the client can connect from any port and can get assigned to the appropriate vlan that it is supposed to be in, this is configured in the radius server. This gives flexibility for the clients to move around the network with out much configuration need to be done by the administrator.


## 2.2 Functional Description

The Ports of an 802.1X Authenticator switch provide the means in which it can offer services to other systems reachable via the LAN. Port-based network access control allows the operation of a System.s Port(s) to be controlled in order to ensure that access to its services is only permitted by Systems that are authorized to do so.

Port Access Control provides a means of preventing unauthorized access by Supplicants to the services offered by a System. Control over the access to a switch and the LAN to which it is connected can be desirable in order to restrict access to publicly accessible bridge ports or to restrict access to departmental LANs.

Access control is achieved by enforcing authentication of Supplicants that are attached to an Authenticator.s controlled Ports. The result of the authentication process determines whether the Supplicant is authorized to access services on that controlled Port.

A PAE is able to adopt one of two distinct roles within an access control interaction:

1. authenticator: A Port that enforces authentication before allowing access to services available via that Port.

2. supplicant: A Port that attempts to access services offered by the Authenticator.

Additionally, there exists a third role:
3. authentication server: Performs the authentication function necessary to check the credentials of the Supplicant on behalf of the Authenticator.  

All three roles are required in order to complete an authentication exchange.  SONiC supports the Authenticator role only, in which the PAE is responsible for communicating with the Supplicant. The Authenticator PAE is also responsible for submitting the information received from the Supplicant to the Authentication Server in order for the credentials to be checked, which will determine the authorization state of the Port. The Authenticator PAE controls the authorized/unauthorized state of the controlled Port depending on the outcome of the authentication process.

Local vs. RADIUS Authentication
There are two methods that can be used for authenticating a user for a particular port on a switch. These methods are described in the following sections.

Local Authentication
Local authentication consists of matching a user ID/password combination from the Supplicant to a locally-stored user database. The user ID that is returned in response to an EAP-Request/Identity is matched against the database. If a match is found, an EAP-Request/MD5 challenge request is sent to the Supplicant. The Supplicant then takes the challenge, its password and the EAP ID and derives an MD5 response. The MD5 response is then send to the Authenticator which uses the locally stored password to derive the answer and compare it to the Supplicants response. A match results in a successful authentication of the port.

Note that the SONiC Authenticator supports only the EAP-MD5 authentication type for local authentication.


ADIUS Authentication

When RADIUS authentication is used, the Authenticator basically becomes a passthrough between. The Supplicant and the RADIUS server exchange EAP messages which are encapsulated in either EAPOL or RADIUS frames (depending on the direction of the frame) by the Authenticator switch. The Authenticator determines the authorization status of the port based on RADIUS Access-Accept or Access-Reject frames. The Authenticator switch also needs to send and process all appropriate RADIUS attributes. For more information on these attributes, see the section labelled "RADIUS Authentication Attributes" in this document.

Per IEEE 802.1X-2001, the SONiC Authenticator supports EAP types that support RFC2284 (i.e. MD5, TLS, PEAP). For certain authentication types, such as EAP-TLS, key information can also be sent from the Authentication Server to the Authenticator as a RADIUS attribute in a RADIUS Access-Accept packet (i.e.  the MS-MPPE-Send-Key and MS-MPPE-Recv-Key attributes). The SONiC Authenticator provides the state machines and outcalls to handle keys, but, key distribution has not been fully implemented. The mechanisms provided could be used to extend platforms that support key distribution. Note that re-keying of Supplicants authentication types could be accomplished by enabling reauthentication on the SONiC Authenticator.


Unidirectional and bidirectional control
The controlled directions can be configured by management to dictate the degree to which protocol exchanges take place between Supplicant and Authenticator. This affects whether the unauthorized controlled Port exerts control over communication in both directions (disabling both incoming and outgoing frames) or just in the incoming direction (disabling only the reception of incoming frames). The management setting of the controlled directions parameter can take on one of the following values:
1. Both: Control is exerted over both incoming and outgoing frames.
2. In: Control is only exerted over incoming traffic. Per 802.1X, if the Port is bridge Port, the operational status of controlled directions will be set to Both. However, unidirectional control is not supported in SONiC. Please see "Limitations and Restrictions" section.

# 3 Design

## 3.1 Overview

**Dot1x - multi host mode control flow**   
0. User enables authentication 
	ACL - "deny ip any any" installed
	PortMgr - L2 Learning disabled (hardware)
	Rules for EAP packets comes to hostapd
1. Client sends an EAP Packet (EAP Start).
2. Hostapd informs pacd that there is a new client (IPC)
3. pacd informs hostapd to authenticate new client
4. hostapd authenticates the client using RADIUS
5. hostapd communicates authorization parameters to pacd
	Auth Success/Failure
	Authorization parameters (VLAN, DACL, Redirect, Voice, etc.)
6. pacd communicates with aclmgrd via STATE_DB
	(a) remove existing ACLs (deny ip any any)
	(b) install new ACLs permitting traffic for the new client
7. pacd communicates with PortMgr
	PortMgr - L2 Learning enabled (hardware)
8. pacd communicates with vlanmgrd
	clean-up existing port vlan config - operationally
	update Port membership with client VLAN
	update port PVID to client VLAN
9. (10), (11) -- Future component interaction based on Auth parameters received (like Voice VLAN, Redirect, DACL, etc.)
12. pacd informs hostapd success/failure depending on results of 6-11
13. hostapd sends EAP_SUCCESS/EAP_FAILURE



## 3.2 DB Changes

### 3.2.1 Config DB


### 3.2.2 App DB

### 3.2.4 ASIC DB

None

### 3.2.5 Counter DB

## 3.3 Switch State Service Design

### 3.3.1 Orchestration Agent

#### 3.3.1.1 PAC Orchestration agent

### 3.3.2 PAC daemons

#### 3.3.2.1 PAC Manager

#### 3.3.2.2 Authentication Manager

### 3.3.3 Other Process

No change to other process.

## 3.4 SyncD
No changes to SyncD.

## 3.5 SAI
No change to SAI.

## 3.6 Manageability

### 3.6.1 Data Models

### 3.6.2 Configuration Commands

The following commands are used to configure PAC 


# 4 Flow Diagrams

# 5 Error Handling


# 6 Serviceability and Debug

# 7 Warm Boot Support
Configured actions and counters should continue to work across warm reboot.

# 8 Scalability

## 8.1 Software scalability

# 9 Limitation

# 10 Upgrade / Downgrade considerations

# 11 Unit Test


# 12 Appendix: Sample configuration


**Using Sonic-CLI**


# 13 Internal Design Information

Internal BRCM information to be removed before sharing with the community

## 13.1 Future Design Enhancements

