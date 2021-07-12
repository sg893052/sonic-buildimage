
# Port Access Control in SONiC

High level design document version 0.9

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
		- [1.1.2 MAC Authentication Bypass](#112-mac-authentication-bypass)
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
		- [3.2.3 ASIC DB](#323-asic-db)
		- [3.2.4 Counter DB](#324-counter-db)
		- [3.2.5 State DB](#325-state-db)
	- [3.3 Switch State Service Design](#33-switch-state-service-design)
		- [3.3.1 Orchestration Agent](#331-orchestration-agent)
		- [3.3.2 pacd](#332-pacd)
			- [3.3.2.1 Authentication Manager port modes](#3321-authentication-manager-port-modes)
			- [3.3.2.2 Authentication Manager port host modes](#3322-authentication-manager-port-host-modes)
			- [3.3.2.3 Authentication Manager Authentication method fallback and priorities](#3323-authentication-manager-authentication-method-fallback-and-priorities)
			- [3.3.2.4 Authorization parameters](#3324-authorization-parameters)
			- [3.3.2.5 Dynamic ACL attributes](#3325-dynamic-acl-attributes)
			- [3.3.2.6 ACL naming convention](#3326-acl-naming-convention)
			- [3.3.2.7 Authentication Manager special VLANs](#3327-authentication-manager-special-vlans)
			- [3.3.2.8 Critical VLAN processing](#3328-critical-vlan-processing)
			- [3.3.2.9 Monitor Mode](#3329-monitor-mode)
			- [3.3.2.10 Authentication History](#33210-authentication-history)
			- [3.3.2.11 Open Authentication](#33211-open-authentication)
		- [3.3.3 mabd](#333-mabd)
		- [3.3.4 hostapd](#334-hostapd)
		- [3.3.5 hostapd](#335-hostapdmgr)
		- [3.3.6 fpinfra Library](#336-fpinfra-library)
		- [3.3.7 Other Process](#337-other-process)
		- [3.3.8 Interaction between pacd hostapd and mabd](#338-interaction-between-pacd-hostapd-and-mabd)
	- [3.4 SyncD](#34-syncd)
	- [3.5 SAI](#35-sai)
	- [3.6 Manageability](#36-manageability)
		- [3.6.1 Data Models](#361-data-models)
		- [3.6.2 Configuration Commands](#362-configuration-commands)
			- [3.6.2.1 authentication critical recovery max-reauth](#3621-authentication-critical-recovery-max-reauth )
			- [3.6.2.2 authentication monitor](#3622-authentication-monitor)
			- [3.6.2.3 aaa authentication pac](#3623-aaa-authentication-pac)
			- [3.6.2.4 mab request format attribute 1](#3624-mab-request-format-attribute-1)
			- [3.6.2.5 dot1x system-auth-control](#3625-dot1x-system-auth-control)
			- [3.6.2.6 authentication event no-response action authorize vlan](#3626-authentication-event-no-response-action-authorize-vlan)
			- [3.6.2.7 authentication event fail action authorize vlan](#3627-authentication-event-fail-action-authorize-vlan)
			- [3.6.2.8 authentication event fail retry](#3628-authentication-event-fail-retry)
			- [3.6.2.9 authentication max-users](#3629-authentication-max-users)
			- [3.6.2.10 authentication periodic](#36210-authentication-periodic)
			- [3.6.2.11 authentication port-control](#36211-authentication-port-control)
			- [3.6.2.12 authentication host-mode](#36212-authentication-host-mode)
			- [3.6.2.13 authentication timer reauthentiate](#36213-authentication-timer-reauthentiate)
			- [3.6.2.14 authentication event server dead action](#36214-authentication-event-server-dead-action)
			- [3.6.2.15 authentication event server dead action authorize voice](#36215-authentication-event-server-dead-action-authorize-voice)
			- [3.6.2.16 authentication event server alive action reinitialize](#36216-authentication-event-server-alive-action-reinitialize)
			- [3.6.2.17 authentication open](#36217-authentication-open)
			- [3.6.2.18 authentication order](#36218-authentication-order)
			- [3.6.2.19 authentication priority](#36219-authentication-priority)
			- [3.6.2.20 mab](#36220-mab)
		- [3.6.3 Show Commands](#363-show-commands)
			- [3.6.3.1 show authentication interface](#3631-show-authentication-interface)
			- [3.6.3.2 show authentication](#3632-show-authentication)
			- [3.6.3.3 show authentication clients](#3633-show-authentication-clients)
			- [3.6.3.4 show authentication authentication-history](#3634-show-authentication-authentication-history)
			- [3.6.3.5 show mab](#3635-show-mab)
			- [3.6.3.6 show dot1x](#3636-show-dot1x)
		- [3.6.4 Clear Commands](#364-clear-commands)
			- [3.6.4.1 clear authentication sessions](#3641-clear-authentication-sessions)
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
| 0.2  | 04/05/2021 | Prabhu Sreenivasan, Amitabha Sen | DB schema update and Review comments |
| 0.3  | 04/27/2021 | Prabhu Sreenivasan, Amitabha Sen | Updated CLI commands |
| 0.4  | 05/07/2021 | Prabhu Sreenivasan, Amitabha Sen | Updated desgin section |
| 0.5  | 05/07/2021 | Prabhu Sreenivasan, Amitabha Sen | Updated requirements and functional description section |
| 0.6  | 05/07/2021 | Prabhu Sreenivasan, Amitabha Sen | Updated docker to macsec, added configuration, scalability and warmboot requirements |
| 0.7  | 05/26/2021 | Prabhu Sreenivasan, Amitabha Sen | Review comments |
| 0.8  | 06/03/2021 | Prabhu Sreenivasan, Amitabha Sen | Review comments |
| 0.9  | 07/12/2021 | Prabhu Sreenivasan, Amitabha Sen | Review comments, removed dot1x timeout and clear dot1x statistics commands, removed scale limit for max ports supporting mab, dot1x. modified show dot1x output. |

# About this Manual
This document describes the design details of the Port Access Control feature in SONiC. Port Access Control (PAC) feature provides validation of client and user credentials to prevent unauthorized access to a specific switch port.

# Scope
This document describes the high level design details about Port Access Control feature implementation in SONiC.


# Definition / Abbreviation

## Table 1 Abbreviations
| **Term** | **Meaning**          |
| -------- | -------------------- |
| Authenticator | An entity that enforces authentication on a port before allowing access to services available on that port |
| CoPP     | Control Plane Policing |
| Dot1x    | IEEE 802.1 standard  |
| EAPOL    | Extensible Authentication Protocol over LAN  |
| MAB      | Mac-based Authentication Bypass |
| PAC      | Port Access Control  |
| PAE      | Port Access Entity |
| RADIUS   | Remote Authentication Dial In User service |
| SONiC-CLI | Klish CLI used by management framework |
| Supplicant | A client that attempts to access services offered by the Authenticator |

# 1 Feature Overview

## 1.1 Port Access Control
Port Access Control (PAC) feature provides validation of client and user credentials to prevent unauthorized access to a specific switch port.   

Local Area Networks (LANs) are often deployed in environments that permit unauthorized devices to be physically attached to the LAN infrastructure, or permit unauthorized users to attempt to access the LAN through equipment already attached. In such environments, it may be desirable to restrict access to the services offered by the LAN to those users and devices (clients) that are permitted to use those services. PAC provides means for authenticating and authorizing devices attached to a LAN port that has point-to-point connection characteristics and of preventing access to that port in cases in which the authentication and authorization process fails.      

PAC uses authentication methods like 802.1x and MAB for client authentication. These methods in turn use RADIUS for client credential verification and receive the authorization attributes like VLANs, ACLs etc.. for the authenticated clients.

### 1.1.1 Dot1x 

IEEE 802.1X-2004 is an IEEE Standard for Port Access Control (PAC) that provides an authentication mechanism to devices wishing to attach to a LAN. The standard defines Extensible Authentication Protocol Over LAN (EAPOL). The 802.1X standard describes an architectural framework within which authentication and consequent actions take place. It also establishes the requirements for a protocol between the authenticator and the supplicant, as well as between the authenticator and the authentication server. 

### 1.1.2 MAC Authentication Bypass
An authenticator can make use of MAC Authentication Bypass (MAB) feature to authenticate simple devices like camera or printers which do not support 802.1x. MAB feature makes use of the device MAC address to authenticate the client.

## 1.3 Requirements

### 1.3.1 Functional Requirements

*PAC*   
The following are the requirements for Port Access Control feature:
1. PAC should be supported on physical interfaces only.
2. The interfaces should not be part of a port-channel / LAG.
3. PAC shall not be supported on Out-of-band port.
4. PAC enforces access control for clients on switch ports using the following authentication mechanisms:
   - 802.1x
   - MAB (MAC-based authentication bypass).
5. Both 802.1x and MAB can be enabled on a port together. Their relative order and priority shall be configurable.
6. PAC shall be supported on ports configured as switch port mode access. Its not supported on trunk ports.
7. PAC supports the following Dynamic ACLs:
   - Named ACLs : ACLs that are statically configured on the switch and are used to control authenticated client traffic.
   - Dynamic ACLs : Rules of these ACLs are defined in RADIUS profiles. These rules (excluding ACL name) are sent in the RADIUS Access Accept and then the ACL is applied to the authenticated client.
   - Filter Id : This is a standard RADIUS attribute that is sent in the RADIUS Access Accept message and is used to indicate a statically configured ACL to be used for the authenticated client.
   - Downloadable ACLs : These ACLs, along with name and their rules, are defined in a RADIUS server. They are downloaded using RADIUS messages and are applied to the authenticated clients. They are also called CISCO-secure-ACLs.
8. SONiC supports Single-Host mode where only one data client can be authenticated on a port and is granted access to the port at a given time.
9. SONiC supports Multiple Hosts mode where only one data client can be authenticated on a port and after that access is granted to all clients connected to the port
10. SONiC supports Multiple Domain Authentication mode where only one data and one voice client can be authenticated on a port.
11. SONiC supports Multiple Authentication mode where one voice client and multiple data clients can be authenticated on a port and these clients are then granted access.
12. SONiC shall suport the following VLANs to authorize clients which fail authentication:
     - Unauthenticated VLAN
     - Guest VLAN
     - Monitor VLAN
     - Open VLAN
     - Critical VLAN
13. SONiC shall suport Voice VLAN to authorize Voice clients.
14. The following PAC port modes are supported on SONiC: 
    - Auto : Authentication is enforced on the port. Traffic is only allowed for authenticated clients
    - Force Authorized : Authentication is not enforced on the port and all traffic is allowed.
    - Force Unauthorized : Authentication is not enforced on the port and all traffic is blocked.

*802.1x*   
1. SONiC shall support 802.1x authenticator functionality.

*MAB*   
1. MAB shall be used to authenticate clients that do not support 802.1x

*RADIUS*   
1. PAC shall have a RADIUS clinet functionality where user shall configure RADIUS server details.
2. RADIUS authentication shall be tested/qualified with the following Radius Servers:   
	- FreeRADIUS
	- ClearPass
	- Cisco ISE.


### 1.3.2 Configuration and Management Requirements
This feature shall support CLI and REST based configurations.

List of configuration shall include the following:
- enable PAC feature globally.
- configure the number of supplicants that are re-authenticated per second.
- enable the Authentication monitor mode on the switch.
- configure the authentication method for port-based access to the switch.
- configuration parameters that needed to format attribute1 for MAB requests to the RADIUS server.
- enable the dot1x authentication support on the switch.
- configure VLAN as guest vlan on an interface.
- configure the unauthenticated VLAN associated with the specified interface.
- configure the number of times authentication may be reattempted by the client before a port moves to the authentication fail VLAN.
- set the maximum number of clients supported on an interface when multi-authentication host mode is enabled on the port.
- enable periodic reauthentication of the supplicant for the specified interface.
- set the authentication mode to use on the specified interface.
- configure the host mode of a port.
- configure the period of time after which the Authenticator attempts to reauthenticate a supplicant on the port.
- configure the actions to take when all the authentication servers are dead.
- enable authorization of voice devices on the critical voice VLAN when all the authentication servers are dead.
- configure the actions to take when one authentication server comes back alive after all were dead.
- configure Open Authentication mode on the port.
- set the order of authentication methods used on a port.
- set the priority for the authentication methods used on a port.
- enable MAC Authentication Bypass (MAB) on an interface.

### 1.3.3 Scalability Requirements
1. 48 clients per port, with a maxmimum of 512 clients per switch
2. 30 ACL rules per client/host

### 1.3.4 Warm Boot Requirements
Port Access Control feature should work seamlessly across warmboot:
- Statistics must be preserved across warmboot.
- HW and SW entries for authenticated clients are preserved across warmboot.
- Authenticated client traffic is not disrupted.
- Clients in the process of authentication will need to re-initiate their authentication process.

## 1.4 Design Overview

### 1.4.1 Basic Approach

### 1.4.2 Container
Existing container macsec holds all the port security applications. Apart from macsec container, code changes are made to SWSS, mgmt-frameowrk containers.
 
### 1.4.3 SAI Support
No changes to SAI spec for supporting PAC.

# 2 Functionality

## 2.1 Target Deployment Use Cases

When a client authenticates itself initially on the network, the Switch acts as the authenticator to the clients on the network and forwards the authentication request to the Radius server in the network. If the authentication succeeds then the client is placed in authorized state and the client is able to forward or receive traffic through the port. RADIUS servers sends a list of Authorization attributes like VLAN, ACLs etc.. to be applied to the client traffic. This allows the flexibility of differential treatment to clients.   

![pac-deployment](https://user-images.githubusercontent.com/45380242/117295415-7157c300-ae91-11eb-99fb-6415ce79fe44.PNG)


**Figure 1 : PAC target deployment use cases**   

## 2.2 Functional Description

The Ports of an 802.1X Authenticator switch provide the means in which it can offer services to other systems reachable via the LAN. Port-based network access control allows the operation of a System's Port(s) to be controlled in order to ensure that access to its services is only permitted by Systems that are authorized to do so.   

Port Access Control provides a means of preventing unauthorized access by Supplicants to the services offered by a System. Control over the access to a switch and the LAN to which it is connected can be desirable in order to restrict access to publicly accessible bridge ports or to restrict access to departmental LANs.   

Access control is achieved by enforcing authentication of Supplicants that are attached to an Authenticator's controlled Ports. The result of the authentication process determines whether the Supplicant is authorized to access services on that controlled Port.   

A PAE is able to adopt one of two distinct roles within an access control interaction:   
1. authenticator: An entity that enforces authentication on a port before allowing access to services available on that port.
2. supplicant: A client that attempts to access services offered by the Authenticator.   

Additionally, there exists a third role:   
3. authentication server: Performs the authentication function necessary to check the credentials of the Supplicant on behalf of the Authenticator.  

All three roles are required in order to complete an authentication exchange. **SONiC supports the Authenticator role only, in which the PAE is responsible for communicating with the Supplicant.** The Authenticator PAE is also responsible for submitting the information received from the Supplicant to the Authentication Server in order for the credentials to be checked, which will determine the authorization state of the Port. The Authenticator PAE controls the authorized/unauthorized state of the controlled Port depending on the outcome of the authentication process.

### 2.2.1 RADIUS Authentication
The Authenticator is essentially a passthrough for the EAP method. The Supplicant and the RADIUS server exchange EAP messages which are encapsulated in either EAPOL or RADIUS frames (depending on the direction of the frame) by the Authenticator switch. The Authenticator determines the authorization status of the port based on RADIUS Access-Accept or Access-Reject frames. The Authenticator switch also needs to send and process all appropriate RADIUS attributes. For more information on these attributes, see the section [Authorization parameters](#3324-authorization-parameters).

### 2.2.2 Unidirectional and bidirectional control
The controlled directions (from the authenticator perspective) dictate the degree to which data traffic flow is controlled for an unauthenticated client. This affects whether the unauthorized controlled Port exerts control over communication in both directions (disabling both incoming and outgoing frames) or just in the incoming direction (disabling only the reception of incoming frames). The control directions are of two types:   
1. Both: Control is exerted over both incoming and outgoing frames.
2. In: Control is only exerted over incoming traffic.   

**SONiC allows only unidirection(In) control. Please see [Limitation](#9-limitation) section.**


### 2.2.3 Downloadable ACL
Once a client on an access controlled port is authenticated, the external RADIUS server can send ACL attributes based on user profile configuration on the RADIUS server. These are called Downloadable ACL’s. IPv6 and IPv4 ACLs are supported for DACL. The downloadable ACL rules per client are sent in extended ACL syntax style. The switch applies the client specific DACL for the duration of the authenticated session.   

The switch does not display RADIUS specified DACL’s in the running configuration. The ACL however shows up in the user interface show commands. The DACL configuration is only applied on the client-connected-port for the duration of the authenticated client session and is not persistent. The downloadable ACLs sent by RADIUS are in extended ACL syntax style and are validated just like user created ACLs. The DACLs on the switch are managed by the PAC application and hence cannot be deleted by the user.   

Generally, any static ACLs (created by user) applied on the port are removed prior to applying the dynamic ACL on the port. Once the application created dynamic ACL is removed/deleted, the static ACLs is re-applied on the port. Essentially, static ACLs and dynamic ACLs are mutually exclusive. However if Open Authentication is configured on the port, the static ACLs and dynamic ACLs co-exist on the port. In such situations, the static ACLs have lower priority than the dynamic ACLs attached on the port. In situations where the client IP address changes, PAC gets to know about it via dhcp-snooping binding tables and the application created ACLs are automatically updated to accommodate the operational change like a changed client IP address.   


### 2.2.4 Named ACLs
RADIUS server can also provide an attribute (filter name/filter id) to have PAC apply a pre-configured ACL on the switch to the client. These pre-configured ACLs are *named ACLs*. These ACLs are created by the user on the switch. Once RADIUS indicates a named ACL is to be applied for a client, PAC replicates the ACL rules, modifies the rules to incorporate the client IP and then provide them as dynamic ACL rules.

### 2.2.5 RADIUS supplied VLANs
PAC (port access control) brings in support for access control with the ability to control user profiles from a RADIUS server. Once a client is authenticated, the client authorization parameters from RADIUS can indicate VLAN association for client traffic. The VLAN associated to the client could be a pre-created VLAN on the switch (static VLAN). In the absence of the VLAN on the switch, the VLAN are created on the switch (dynamic VLANs).

Default VLAN on port
A port should not be part of user configured VLAN When PAC is enabled on the port. The port gets configured for a default VLAN (VLAN 1 - configurable) when PAC is enabled on the port. Once PAC is disabled on the port, the port is removed from the default VLAN.

### 2.2.6 FDB interaction
PAC interacts with FDB to modify the learning mode of a port and add static FDB entries. The FDB related interactions for PAC are outlined below:
- PAC adds a static FDB entry for every authenticated client on Multi-Auth, Single-Host and Multi-Domain host modes.   
- SONiC today allows addition of static FDB entries from CLI which are persistent across reloads. The same is enhanced so that PAC SONiC applications can add static FDB entries. 
- Note that FDB entries thus added are not persistent and are operational config only (entries added as a result of the client getting authenticated).   
- The entries get removed once the client logs off.   
- FDB entries added operationally follows a similar config sequence like user created FDB entries.   
- Prior to the introduction of PAC, the learning mode of a port (or bridge port) was configured and controlled completely at the orchestration layer. With PAC, the application layer (pacd) also manages the learning mode to
	1. Once PAC is enabled on a port, all incoming traffic on the port are blocked/dropped except certain protocol traffic.
	2. PAC turns off learning on the port essentially dropping all unknown source MAC packets. This achieves the requirement of blocking ingress traffic.
	3. Egress traffic on the port is not blocked.
	4. Once a client starts the authentication process, the client is no longer unknown (unknown source MAC). PAC installs a static FDB entry with discard bits set to mark the client "known" so that the incoming traffic does not flood the CPU.
	5. After a client is authenticated, this discard FDB entry is removed. Learning on the port is enabled(multi-host-mode) or retained disabled (rest of the host modes) for that port.
- For MAB, unknown source MAC packets are trapped to the CPU.
- Station movement is also handled i.e if a packet is received from another port on a MAC, VLAN pair for which PAC installed a static FDB entry, such packets also get trapped to the CPU.


# 3 Design

## 3.1 Overview

[Figure 2](#pac-config-flow) shows the high level design overview of PAC services in SONiC. PAC Services Daemon is composed of multiple sub-modules. The main module i.e. PAC daemon handles the authentication related commands and makes use of hostApd and mabd daemons to authenticate a client via dot1x and mab respectively. hostApd being a standard Linux application takes hostapd.conf as its config file. hostApdMgr takes care of listening to dot1x specific configuration and translating them to respective hostapd.conf file config entries. pacd daemon being the main module decides which authentication protocol needs to be used for a given port and also calls APIs to program the polices in hardware.

### 3.1.1 Configuration flow

![pac-config-flow](https://user-images.githubusercontent.com/45380242/115655812-a91f2080-a351-11eb-9207-26dafc103d8e.PNG)

**Figure 2: PAC service daemon and configuration flow**

1. Mgmt interfaces like CLI and REST writes the user provided configuration to CONFIG_DB.
2. The pacd, mabd and hostApdMgr gets notified about their respective configuration.
3. hostApd being a standard Linux application gets its configuration from hostapd.conf file. hostApdMgr makes use of Jinja2 templates to generates the hostapd.conf file based on the relevant CONFIG_DB tables.
4. Pacd gets to know about the list of ports that needs to be authenticated from PAC_PORT_CONFIG_TABLE on CONFIG_DB. The same table provides info on which ports supporrts DOT1X and which supports MAB and priority amoung the authentication methods. Based on the priority and authentication failure status, pacd decides on the list of ports to be authenticated via DOT1X and the list of ports that needs to be authenticated via MAB. It communicates the respective list of interfaces to hostApd and mabd via Unix domain socket messages.
5. hostApd listens to EAPOL PDUs on the provided interface list and proceeds to authenticate the client when it receives a PDU. mabd listens to DHCP and EAPOL PDUs on the provided interface list and proceeds to authenticate the client when it receives a PDU.



### 3.1.2 EAPOL receive flow


![EAPOL-receive-flow](https://user-images.githubusercontent.com/45380242/115655906-ceac2a00-a351-11eb-9095-9d53ae549ad7.PNG)


**Figure 3: EAPOL receive flow**

1. EAPOL packet is received by hardware on a front panel interface and trapped to CPU. The packet gets thru the KNET driver and Linux Network Stack and eventually gets delivered to hostApd socket listening on EtherType 0x888E on kernel interface associated with the given front panel interface.
2. In a multi-step process, hostApd runs the Dot1x state machine to Authenticate the client via RADIUS.
3. On successful authentication of a client, hostApd sends an Client Authenticated Unix domain socket message to pacd with all the authorization parameters like VLAN and DACL.
4. pacd proceeds to authorize the client by writing PAC_AUTHORIZE_TABLE on APPL_DB. RADIUS authorization parameters like dynamic VLAN, dynamic ACL are created by writing on their tables on STATE_DB.
5. Orchagent in SWSS docker gets notified about changes in APPL_DB and responds by translating the APPL_DB changes to respective sairedis calls.
6. Sairedis APIs write into ASIC_DB.
7. Syncd gets notified on changes to ASIC_DB and in turn calls respective SAI calls.
8. The SAI calls translate to respective SDK calls to program hardware.
9. EAP success message (EAPOL PDU) is sent to client.


### 3.1.3 MAB PDU receive flow

![mab-pdu-receive-flow](https://user-images.githubusercontent.com/45380242/115655929-da97ec00-a351-11eb-90d3-a602ee2b0e3e.PNG)


**Figure 4: MAB PDU receive flow**

1. DHCP packet is received by hardware on a front panel interface and trapped to CPU. The packet gets thru the KNET driver and Linux Network Stack and eventually gets delivered to pacd socket listening on the kernel interface associated with the given front panel interface.
2. Pacd sends an Client Authenticate Unix domain socket message along with the received PDU MAC.
3. mabd interacts with RADIUS server to authenticate the given client based on the MAC.
4. On successful authentication of a client, mabd sends an Client Authenticated Unix domain socket message to pacd with all the authorization parameters like VLAN and DACL.
5. pacd proceeds to authorize the client by writing PAC_AUTHORIZE_TABLE on APPL_DB. RADIUS authorization parameters like dynamic VLAN, dynamic ACL are created by writing on their tables on STATE_DB.
6. Orchagent in SWSS docker gets notified about changes in APPL_DB and responds by translating the APPL_DB changes to respective sairedis calls.
7. Sairedis APIs write into ASIC_DB.
8. Syncd gets notified on changes to ASIC_DB and in turn calls respective SAI calls.
9. The SAI calls translate to respective SDK calls to program hardware.
10. EAP success message (EAPOL PDU) is sent to client.



## 3.2 DB Changes

### 3.2.1 Config DB

**PAC_PORT_CONFIG_TABLE**   
```   
"PAC_PORT_CONFIG_TABLE": {
  "ethernet1": {
    "method_list": [
      "802.1x",
      "mab"
    ],
    "priority_list": [
      "802.1x",
      "mab"
    ],
    "port_control_mode": "auto",
    "host_control_mode": "multi_auth",
    "quiet_period": 10,
    "reauth_period": 60,
    "reauth_enable": "true",
    "max_users_per_port": 48,
    "max_reauth_attempts": 1,
    "guest_vlan": 10,
    "auth_fail_vlan": 100,
    "open_authentication_mode": "disabled",
    "dead_server_action": "reinitialize",
    "dead_server_alive_action": "reinitialize",
    "dead_server_critical_vlan": 200,
    "dead_server_action_voice": "authorize"
  }
}


key                       =      PAC_PORT_CONFIG_TABLE:port     ;Physical port
     
;field                    =      value
     
method_list               =      "dot1x"/"mab"                  ;List of methods to be used for authentication
     
priority_list             =      "dot1x"/"mab"                  ;Relative priority of methods to be used for authentication
     
port_control_mode         =      "auto"/"force_authorized"/     'auto": authentication enforced on port
                                "force_unauthorized" ;         'force_authorized": authentication not enforced on port
                                                                'force_unauthorized": authentication not enforced on port but port is blocked for all traffic
     
host_control_mode         =      "multi-host"/"multi-domain"/   ;"multi-host": One data client can be authenticated on the port. Rest of the
                                "multi-auth"/"single-auth"      clients tailgate once the first client is authenticated.
                                                                "multi-domain": One data client and one voice client can be authenticated on the port.
                                                                "multi-auth": Multiple data client and one voice client can be authenticated on the port.
                                                                "single-auth": One data client or one voice client can be authenticated on the port.
     
quiet_period              =      1*5DIGIT                       ;The initial value of the timer that defines the period during which the
                                                                 Authenticator will not attempt to authenticate the Supplicant.
                                                                 Range is 0-65535 seconds.
     
reauth_period             =      1*10DIGIT                      ;The initial value of the timer that defines the period after which the will
                                                                 reauthenticate the Supplicant. Range is 3600 - 4294967296 seconds.

reauth_enable             =     "true"/"false"                  ;Indicates whether Reauthentication is enabled on the port.
     
max_users_per_port        =     1*2DIGIT                        ;Maximum number of clients that can be authenticated on the port. This is applicable
                                                                 only for "multi-auth" host mode. Range is 1 - 64 clients.

guest_vlan_id             =     1*4DIGIT                        ;The Guest VLAN Id for the port.Range is 1 - 4093
     
auth_fail_vlan_id         =     1*4DIGIT                        ;The Authentication Fail VLAN Id for the port.Range is 1 - 4093
     
     
max_auth_attempts         =     1DIGIT                          ;The maximum number of authentication retries in the event of authentication
                                                                 failure. Range is 1 - 5

open_authentication_mode  =     "enable"/"disable"              ;Indicates whether Open Authentication mode is enabled on the port.


dead_server_action        =     "none"/"reinitialize"/"authorize" ;Indicates action to be taken for data clients when all configured RADIUS
                                                                   servers are marked Dead.

dead_server_alive_action  =“none”/"reinitialize"                ;Indicates action to be taken for date clients when one of the configured RADIUS
                                                                 servers is marked Alive after all were marked Dead.

dead_server_critical_vlan =     1*4DIGIT                        ;The Critical (data) VLAN Id for the port.Range is 1 - 4093

dead_server_action_voice  =     “none”/”authorize"              ;Indicates action to be taken for voice clients when all configured RADIUS servers are marked Dead.

```   

**PAC_GLOBAL_CONFIG_TABLE**   
```  
"PAC_GLOBAL_CONFIG_TABLE": {"authentication_enable": "true","monitor_mode_enable": "false"}


;field                  =     value

authentication_enable   =     "true"/"false"            ;Indicates whether PAC is enabled in the system.

monitor_mode_enable     =     "true"/"false"            ;Indicates whether monitor mode is enabled in the system.

```     
 
**HOSTAPD_GLOBAL_CONFIG_TABLE**   
```
"HOSTAPD_GLOBAL_CONFIG_TABLE": {
  "dot1x_system_auth_control": "enable"
}


;field = value 
dot1x_system_auth_control "true"/"false" ; Indicates whether dot1x is enabled in the system.
```

### 3.2.2 App DB

### 3.2.3 ASIC DB

None

### 3.2.4 Counter DB

**HOST_APD_STATS_TABLE**   
```
"HOST_APD_STATS_TABLE": [
  {
    "00:00:00:11:22:33": {
      "dot1xAuthEapolFramesRx": 311,
      "dot1xAuthEapolFramesTx": 380,
      "dot1xAuthEapolStartFramesRx": 71,
      "dot1xAuthEapolLogoffFramesRx": 15,
      "dot1xAuthEapolRespIdFramesRx": 67,
      "dot1xAuthEapolRespFramesRx": 212,
      "dot1xAuthEapolReqIdFramesTx": 250,
      "dot1xAuthEapolReqFramesTx": 250,
      "dot1xAuthInvalidEapolFramesRx": 250,
      "dot1xAuthEapLengthErrorFramesRx": 250,
      "dot1xAuthLastEapolFrameVersion": 2
    }
  },
  {
    "00:00:00:22:22:34": {
      "dot1xAuthEapolFramesRx": 311,
      "dot1xAuthEapolFramesTx": 380,
      "dot1xAuthEapolStartFramesRx": 71,
      "dot1xAuthEapolLogoffFramesRx": 15,
      "dot1xAuthEapolRespIdFramesRx": 67,
      "dot1xAuthEapolRespFramesRx": 212,
      "dot1xAuthEapolReqIdFramesTx": 250,
      "dot1xAuthEapolReqFramesTx": 250,
      "dot1xAuthInvalidEapolFramesRx": 250,
      "dot1xAuthEapLengthErrorFramesRx": 250,
      "dot1xAuthLastEapolFrameVersion": 2
    }
  }
]


key = HOST_APD_STATS_TABLE : client mac; Client MAC
;field = value
dot1xAuthEapolFramesRx          = 1*10DIGIT ; The number of valid EAPOL frames of any type that have been received by this Authenticator.
dot1xAuthEapolFramesTx          = 1*10DIGIT ; The number of EAPOL frames of any type that have been transmitted by this Authenticator.
dot1xAuthEapolStartFramesRx     = 1*10DIGIT ; The number of EAPOL Start frames that have been received by this Authenticator.
dot1xAuthEapolLogoffFramesRx    = 1*10DIGIT ; The number of EAPOL Logoff frames that have been received by this Authenticator.
dot1xAuthEapolRespIdFramesRx    = 1*10DIGIT ; The number of EAP Resp/Id frames that have been received by this Authenticator.
dot1xAuthEapolRes pFramesRx     = 1*10DIGIT ; The number of valid EAP Response frames (other than Resp/Id frames) that have been received by this Authenticator.
dot1xAuthEapolReqIdFramesTx     = 1*10DIGIT ; The number of EAP Req/Id frames that have been transmitted by this Authenticator.
dot1xAuthEapolReqFramesTx       = 1*10DIGIT ; The number of EAP Request frames (other than Rq/Id frames) that have been transmitted by this Authenticator.
dot1xAuthInvalidEapolFramesRx   = 1*10DIGIT ; The number of EAPOL frames that have been received by this Authenticator in which the frame type is not recognized.
dot1xAuthEapLengthErrorFramesRx = 1*10DIGIT ; The number of EAPOL frames that have been received by this Authenticator in which the Packet Body Length field is invalid.
dot1xAuthLastEapolFrameVersion  = 1*10DIGIT ; The protocol version number carried in the most recently received EAPOL frame.
```


### 3.2.5 State DB

**PAC_PORT_OPER_TABLE**   
```   
"PAC_PORT_OPER_TABLE": {
  "ethernet1": {
    "enabled_method_list": [
      "802.1x",
      "mab"
    ],
    "enabled_priority_list": [
      "802.1x",
      "mab"
    ],
    "num_clients_authenticated": 10,
    "open_authentication_mode": "enabled",
    "dead_server_action": "reinitialize",
    "dead_server_alive_action": "reinitialize",
    "dead_server_critical_vlan": 200,
    "dead_server_action_voice": "authorize"
  }
}


key                     =    PAC_PORT_OPER_TABLE:port               ;Physical port

;field                  =    value

enabled_method_list       =     "dot1x"/"mab"                       ;List of methods to be used for authentication
enabled_priority_list     =     "dot1x"/"mab"                       ;Relative priority of methods to be used for authentication
num_clients_authenticated =     1*2DIGIT                            ;Number of clients authenticated on the port.
       
open_authentication_mode  =     "enabled"/"disabled"                ;Indicates if open authentication mode is enabled on the port.

dead_server_action        =     "none"/"reinitialize"/"authorize"   ;Indicates action to be taken for data clients when all configured
                                                                     RADIUS servers are marked Dead.

dead_server_alive_action  =     “none”/"reinitialize"               ;Indicates action to be taken for date clients when one of the
                                                                     configured RADIUS servers is marked Alive after all were marked Dead.

dead_server_critical_vlan =      1*4DIGIT                           ;The Critical (data) VLAN Id for the port.Range is 1 - 4093

dead_server_action_voice  =      “none”/”authorize"                 ;Indicates action to be taken for voice clients when all configured
                                                                     RADIUS servers are marked Dead.


```    


**PAC_AUTHENTICATED_CLIENT_OPER_TABLE**   
```
"PAC_AUTHENTICATED_CLIENT_OPER_TABLE": {
  "ethernet1": [
    {
      "00:00:00:11:02:33": {
        "current_id": 21,
        "auth_status": "authorized",
        "authenticated_method": "802.1X",
        "server_state": "36 34 43 50 4d 53 65 73 73 69 6f 6e 49 44 3d 30 61 38 32 62 39 37 36 4c 49 50 5a 44 45 4d 32 74 64 35 55 39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d 6e 4a 33 31 42 6a 5a 34 51 49 3b 33 34 53 65 73 73 69 6f 6e 49 44 3d 43 69 73 63 6f 49 53 45 2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35 3b",
        "server_state_len": 106,
        "server_class": "43 41 43 53 3a 30 61 38 32 62 39 37 36 4c 49 50 5a 44 45 4d 32 74 64 35 55 39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d 6e 4a 33 31 42 6a 5a 34 51 49 3a 43 69 73 63 6f 49 53 45 2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35",
        "server_class_len": 83,
        "session_timeout": 60,
        "user_name": "sonic_user",
        "user_name_len": 9,
        "termination_action": 0,
        "vlan_id": 194,
        "vlan_type": "radius",
        "backend_auth_method": "radius",
        "session_time": 511
      }
    },
    {
      "00:00:00:21:00:30": {
        "current_id": 28,
        "auth_status": "authorized",
        "authenticated_method": "802.1X",
        "server_state": "36 34 43 50 4d 53 65 73 73 69 6f 6e 49 44 3d 30 61 38 32 62 39 37 36 4c 49 50 5a 44 45 4d 32 74 64 35 55 39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d 6e 4a 33 31 42 6a 5a 34 51 49 3b 33 34 53 65 73 73 69 6f 6e 49 44 3d 43 69 73 63 6f 49 53 45 2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35 3b",
        "server_state_len": 106,
        "server_class": "43 41 43 53 3a 30 61 38 32 62 39 37 36 4c 49 50 5a 44 45 4d 32 74 64 35 55 39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d 6e 4a 33 31 42 6a 5a 34 51 49 3a 43 69 73 63 6f 49 53 45 2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35",
        "server_class_len": 83,
        "session_timeout": 60,
        "user_name": "sonic_user1",
        "user_name_len": 9,
        "termination_action": 0,
        "vlan_id": 194,
        "vlan_type": "radius",
        "backend_auth_method": "radius",
        "session_time": 51
      }
    }
  ]
}


key = PAC_AUTHENTICATED_CLIENTS_OPER_TABLE: mac ; Client MAC address
;field               = value ;
current_id           = 1*3DIGIT ; EAP Packet Id
auth_status          = "authorized"/"unauthorized" ; Authorization status of the client.
authenticated_method = "802.1x"/'mab" ; Method used to authenticate the client
server_state         = 1*506VCHARS ; Server State used in RADIUS Challenge packets
server_state_len     = 1*3DIGIT ; Server State Length
server_class         = 1*506VCHARS ; Server Class used in RADIUS Access Accept packets
server_class_len     = 1*3DIGIT ; Server Class Length
session_timeout      = 1*10DIGIT ; Client session timeou
user_name            = 1*255VCHARS ; Client user name
user_name_len        = 1*3DIGIT ; Client user name length
termination_action   = 1DIGIT ; Client action on session timeout:
                            ;0: Terminate the client
                            ;1: Reauthenticate the client
vlan_id              = 1*4DIGIT ; VLAN associated with the authorized client
vlan_type            = "RADIUS"/"Default"/"Voice"/"Critical"/"Unauthenticated"/"Guest"/"Monitor"; Type of VLANs associated with anauthorized client.
                                 ; Default VLAN: The client has been authenticated on the port default VLAN and the authentication server is not RADIUS.
                                 ; RADIUS: RADIUS is used for authenticating the client.
                                 ; Voice VLAN: The client is identified as a Voice device.
                                 ; Critical VLAN: The client has been authenticated on the Critical VLAN.
                                 ; Unauthenticated VLAN: The client has been authenticated on the Unauthenticated VLAN.
                                 ; Guest VLAN: The client has been authenticated on the Guest VLAN.
                                 ; Monitor Mode: The client has been authenticated by Monitor mode.
backend_auth_method = "radius" ; Backend authentication method used to authorize the client.
session_time        = 1*10DIGIT ; Client session time.

```   

**PAC_CLIENT_HISTORY_TABLE**   
```   
"PAC_CLIENT_HISTORY_TABLE": {
"ethernet1": [{
"31-March-2021-13:46:02": {
"client_mac_addr": "00:00:00:11:22:33",
"authentication_method": "mab",
"backend_auth_method": "radius",
"auth_status": "authorized"
}
},
{
"31-March-2021-13:45:58": {
"client_mac_addr": "00:00:00:11:22:33",
"authentication_method": "802.1X",
"backend_auth_method": "radius",
"auth_status": "un-authorized"
}
}
]
}


key = PAC_CLIENT_HISTORY_TABLE : port : date ; Physical Port Clientmac; Client MAC address ; Date
;field = value
client_mac_addr       =  <mac-address> ; Client MAC address
authentication_method = "802.1x"/'mab" ; Method used to authenticate the client
backend_auth_method   = "radius" ; Backend authentication method used to authorize the client.
auth_status           = "authorized"/"unauthorized" ; Authorization status of the client

```   

**HOSTAPD_OPER_PORT_TABLE**   
```
"HOSTAPD_OPER_PORT_TABLE": [{
"ethernet1": {
"protocol_version": 2,
"quiet_period": 30,
"tx_period": 30,
"server_timeout": 45,
"max_reqs": 2,
"max_req_ids": 3
}
}]


key = HOSTAPD_OPER_PORT_TABLE : port ; Physical Port
;field            = value
protocol_version  = 1DIGIT ; Dot1x protocol version
quiet_period      = 1*5DIGIT ; The initial value of the timer that defines the period during which the Authenticator will not attempt to authenticate the Supplicant. Range is 0-65535 seconds.
tx_period         = 1*5DIGIT ; Time time interval between each EAP Request Ids sent to the supplicant.

server_timeout    = 1*5DIGIT ; RADIUS server timeout

max_reqs          = 1*5DIGIT ; Maximum number of EAP requests (except Req-Ids) to be sent to the supplicant before timing out.

max_req_id        = 1*5DIGIT ; Maximum number of EAP Req-Ids to be sent to the supplicant before timing out.
```   


**HOSTAPD_CLIENT_OPER_TABLE**   

```
"HOSTAPD_CLIENT_OPER_TABLE": {
"ethernet1": [{
"00:00:00:11:22:33": {
"user_name": "sonic_user"
}
},
{
"00:00:00:11:22:55": {
"user_name": "sonic_user1"
}
}
]
}


key = HOSTAPD_OPER_PORT_TABLE : port ; Physical Port: client mac; Client MAC
;field    = value
user_name = 1*255VCHARS ; Client user name
```



## 3.3 Switch State Service Design

### 3.3.1 Orchestration Agent

### 3.3.2 pacd
pacd process links with libfpinfra.so and libauthmgr.so for the infrastructure and authentication manager functionality respectvely. Below picture depicts the interal details of the pacd process.

![pacd](https://user-images.githubusercontent.com/45380242/117293455-05745b00-ae8f-11eb-9c36-f7986b0179cf.PNG)   
**Figure 5: pacd process internals**

**Authentication Manager**   
Authentication Manager is the major component of pacd process. Authentication Manager primarily manages the order of authentication methods during a failover scenario. Majority of authentication functionalities are managed by hostapd(802.1X). These include interaction with a AAA server, applying client authorization parameters to allow authenticated client traffic, etc. These are strictly speaking not specific to 802.1X and are applicable to any authenticated methods like MAB.   

Authentication Manager allows enforcing authentication on a port. Authentication Manager needs to be enabled for the same. This is the first step to enabling port based access control. Once authentication is enabled, the port is marked Unauthorized and traffic is blocked through it.

Authentication Manager enables configuring various Authentication Host modes. This is configured per port. These modes determine the number of clients and the type of clients that can be authenticated and authorized on the ports. Distinction is made between Data and Voice clients.

Authentication Manager also enables configuring the authentication methods to be used for authenticating clients on a port. By default the configured authentication methods are tried in order for that port. SONiC allows the below authentication methods to get configured for each port.  
- 802.1X
- MAB

In the event that a port is configured for 802.1X and MAB in this sequence, the port will first attempt to authenticate the user through 802.1X. If 802.1X authentication times out, the switch will attempt MAB. The automatic sequencing of authentication methods allows the network administrator to apply the same configuration to every access port without having to know in advance what kind of device (employee or guest, printer or PC, IEEE 802.1X capable or not, etc.) will be attached to it.   

Authentication Manager allows configuring priority for each authentication method on the port. The default priority of a method is equivalent to its position in the order of the default authentication list.   
 
After successful authentication, the authentication method returns the Authorization parameters for the client. Authentication Manager uses these parameters for configuring the switch for allowing traffic for authenticated clients.

#### 3.3.2.1 Authentication Manager port modes

*Auto*   
This mode is used to enforce authentication on a port. The port is unauthorized and blocked for traffic unless a client is authenticated.

*Force-Authorized*    
This mode is used to disable authentication on a port. All client traffic is allowed.

*Force-Unauthorized*   
This mode is used to un-authorize a port and block any client traffic


#### 3.3.2.2 Authentication Manager port host modes 

*Single-Host mode*   
In this mode only one data client can be authenticated on a port and the client is granted access to the port. Access is allowed only for this client and no one else. Only when this client logs off, can another client get authenticated and authorized on the port and granted port access.   

*Multiple Hosts mode*   
In this mode only one data client can be authenticated on a port. However once authentication succeeds, access is granted to all clients connected to the port. Typical use case is a wireless access point which is connected to an access controlled port of a NAS. Once the access point is authenticated by the NAS, the port is authorized for traffic from not just the access point but also from all the wireless clients connected to the access point. Once this client gets authenticated, the port is open for all clients connected to the port.   

*Multiple Domain Authentication mode*   
In this mode one data client and one voice client can be authenticated on a port and these clients are then granted access. Typical use case is an IP phone connected to a NAS port and a laptop connected to the hub port of the IP phone. Both the devices need to be authenticated to access the network services behind the NAS. The voice and data domains are segregated. The RADIUS server attribute “Cisco-AVPair = "device-traffic-class=voice"” is used to identify a voice client.   

*Multiple Authentication mode*   
In this mode one voice client and multiple data clients can be authenticated on a port and these clients are then granted access. Typical use case is a network of laptops and an IP phone connected to the NAS port via a hub.   

#### 3.3.2.3 Authentication Manager Authentication method fallback and priorities

Authentication manager controls the order in which the authentication methods are executed. Authentication manager does not make any required configuration for the respective methods to authenticate successfully. User or Administrator needs to ensure that the correct and appropriate configuration is present in the system.
Using the Authentication manager, user can configure an authentication method fallback list, which is configured per port. If authentication using any of the method fails, then authentication of the client on the port is tried using the next or subsequent methods.   

The default priority of a method is equivalent to its position in the order of the default authentication list, which is configured per port. If authentication method priorities are not configured, then the relative priorities (highest first) are in the same order as that of per port based authentication list. By configuring the authentication priority, user can over-ride the default priority.   

If the client is already authenticated using methods such as MAB and 802.1X happens to have higher priority than the authenticated method, if a 802.1X frame is received, then the existing authenticated client will be removed and authentication process would begin for the client using 802.1X.However if 802.1X is configured at a lower priority than the authenticated method, then the client will not be removed and the 802.1X frames will be ignored.   

Authentication manager allows user to modify the default method priorities using configuration. This is supported by configuring the priority order list for the authentication methods using the command authentication priority.   

If administrator changes the priority of the methods, then all the users who are authenticated using a lower priority method will be forced to re-authenticate. If an authentication session is in progress and administrator changes the order of the authentication methods then the configuration will take effect for the next session onwards.   

#### 3.3.2.4 Authorization parameters 

Upon successful authentication, the authentication methods inform Authentication Manager about the result. Authentication Manager then authorizes the port and configures it for allowing traffic from the client.  

Authentication Manager receives the client authorization parameters from the authentication method after successful authentication of a client. The following parameters are acted upon:  

- *VLAN Id*: This is the VLAN ID sent by a RADIUS server. Authentication Manager configures the port membership accordingly so that the client traffic is associated with the VLAN.   
- *Session Timeout*: This is the timeout attribute of the authenticated client session.
- *Session Termination Action*: Upon session timeout, the Session Termination Action determines the action on the client session. The following actions are defined:
   - *Default*: The client session is torn down and authentication needs to be restarted for the client.
   - *RADIUS*: Re-authentication is initiated for the client.
- *Filter-Id*: Specifies an ACL of Diffserv policy name. This is used to apply a Static ACL or DiffServ policy on the port for the client. IPv4 and IPv6 ACLs in the “IN” direction is supported. If the Differv policy or ACL is not present in the system, or if a Diffserv policy is already configured on the port, authentication for the client is rejected. These are subject to Monitor Mode configuration. Filter-Id is supported on all Authentication Manager host modes.
- *Downloadable ACL*: DACLs are supported on all host modes.


#### 3.3.2.5 Dynamic ACL attributes   

ACL attributes from the RADIUS server can be sent using below methods:  

*Individual Cisco AVPair attributes*:   
This attribute sends single or multiple Cisco AV-Pairs in the format “ip:inacl” or “ipv6:inacl” containing attribute value as IP ACL rules in Extended ACL format. It can also specify the ACL name which must be pre-configured on the switch. Only one ipv4 and one ipv6 ACL is allowed per client.   

Predefined ACL example:   
```
ip:inacl=99
ip:inacl=Named_ACL
ipv6:inacl=Named_IPv6_ACL
Dynamic ACL example:
ip:inacl[#number]={extended-access-control-list}
ipv6:inacl[#number]={ extended-access-control-list}
```

*Filter-Id Attribute (locally configured ACL)*:   
ACL’s sent using Filter-Id need to be pre-configured on the switch. On receiving Access-Accept packet with Filter-Id containing ACL name with direction, the specified ACL is applied on the port. This ACL can be of any type; ipv4, ipv6 or MAC.  Any existing static ACLs on the port are removed and the new ACL is applied prior to authorizing the port to 802.1X. When the 802.1X session terminates, the pre-existing ACLs is restored to the port. If both a Filter-ID and a Cisco AV-Pair (26) containing a DACL are present in the Access Accept, the Access Accept is treated as an Access-Reject.   

*CiscoSecure-Defined-ACL attribute-value pair*:   
This attribute uses Named ACL’s configured on the RADIUS Server. CiscoSecure-Defined-ACL attribute-value pair can be set with the RADIUS Cisco-AV-Pair vendor-specific attributes (VSAs). This pair specifies the name of the Downloadable ACL on the RADIUS server with the “ACL-Name” attribute.  On receiving CiscoSecure-Defined-ACL attribute, a new Access-Request, with UserName as “ACL-Name” is sent to RADIUS server, to which RADIUS responds with Access-Accept containing Rules in the ACL Set <ACL-Name> of CiscoSecure-Defined-ACL as individual Cisco AV Pair attributes. If there is no response to the 2nd Access-Request or if an Access-Reject is received, the client is Rejected.
Individual Cisco AVPs containing individual ACL rules can be sent in Access-Accept along with CiscoSecure-Defined-ACL AVP. All the rules/ACLs are applied on the port.
Also, the rules downloaded as part of the second Access-Request are stored (cached) in a DB on the DUT till there is at least one client (on any port) using (configured with) that ACL. The “ACL-Name” has to be compliant with ACL naming convention as defined by SONiC.   

For each client session, one ACL of each type (IPv4, IPv6) is supported. Prior to applying ACL sent from RADIUS server, if any ACL’s were configured, they are removed and DACL’s are applied to the port. In case of the last session termination on a port, the removed static ACL’s are restored to the port after deleting the DACL’s.

#### 3.3.2.6 ACL naming convention   

When an ACL is installed by Authentication Manager, the following the naming convention is used.


| ACL type | Source | ACL name |
| -------- | ------ | -------- |
| IPv4 | Filter Id / Cisco AV Pair StaticACL | IP-STATIC-IN-\<Client Session ID\> |
| IPv6 | Filter Id / Cisco AV Pair StaticACL | IPV6-STATIC-IN-\<Client Session ID\> |
| IPv4 | Cisco AV Pair DACL | IP-DACL-IN-\<Client Session ID\> |
| IPv6 | Cisco AV Pair DACL | IPV6-DACL-IN-\<Client Session ID\> |


The switch UI shall prevent the operator from creating a static ACL where the name matches any of the regular expressions that can be part of the above naming conventions.

#### 3.3.2.7 Authentication Manager special VLANs   

Authentication Manager keeps trying the next configured authentication method in case authentication method fails or times out. However if the last authentication fails, Authentication Manager authorizes the client to special VLANs like Unauthenticated VLAN, Guest VLAN, or Critical VLAN. If a client is authenticated in any of these VLANs and the VLAN is reconfigured or deleted from the system, all these clients are unauthorized.   

*Unauthenticated VLAN*   
This is a special VLAN which is used to authorize clients which fail authentication due to invalid credentials. This is used for 802.1X aware clients only.

*Guest VLAN*   
This is a special VLAN used to authorize 802.1X unaware clients. Refer [4] for details.

*Critical VLAN*   
Critical VLAN allows supplicants to authenticate on a Critical VLAN when all RADIUS servers are dead. The criteria to mark a server dead is documented in [8]. The dead-server (all RADIUS servers marked dead) actions are configured per port.

*Critical Voice VLAN*   
Critical Voice VLAN support allows a phone to continue port access on the Voice VLAN when all the RADIUS servers go dead. If the feature is not enabled, the phone will be disconnected.


#### 3.3.2.8 Critical VLAN processing   

When all the configured RADIUS servers are marked Dead, the existing clients are kept intact. This is true for dead action Reinitialize/Authorize/None. Critical VLAN processing begins when either a new client tries to authenticate or re-authentication kicks in for an authorized client.
 
*Critical VLAN dead action “Reinitialize”*   
Pre-condition: All the configured RADIUS servers are marked Dead 
 
When a new client attempts to authenticate or an existing client undergoes re-authentication, the new client or the client under re-authentication is authorized into Critical Data VLAN. All the other authorized clients are terminated, with the exception of clients in Unauthenticated VLAN, Guest VLAN, Voice VLAN, Critical Data VLAN, Critical Voice VLAN, clients authorized in Monitor mode and Open mode. Terminated clients are expected to re-initiate the authentication process and they would be authorized into Critical Data VLAN once they do so.
 
*Critical VLAN dead action “Authorize”*   
Pre-condition: All the configured RADIUS servers are marked Dead  
 
When a new client attempts to authenticate or an existing client undergoes re-authentication, the new client or the client under re-authentication is authorized into Critical Data VLAN. All the other authorized clients are kept undisturbed.

*Critical VLAN dead action “None”*   
Pre-condition: All the configured RADIUS servers are marked Dead.  
 
When a new client attempts to authenticate or an existing client undergoes re-authentication, the new client or the client under re-authentication is not authorized and is terminated. All the other authorized clients are kept undisturbed.

*Critical VLAN alive-server action “Reinitialize”*   
When the alive-server action (one server alive after all were dead) is configured to “reinitialize”, Authentication Manager triggers the re-authentication of supplicants authorized on the Critical VLAN.   

The number of supplicants that are re-authenticated per second is configurable. This configuration is for the entire system across all the supplicants on all ports. This is used to control the system and network load when the number of supplicants to be re-authenticated is large. These re-authentications are triggered due to alive server actions.

#### 3.3.2.9 Monitor Mode   
If Monitor mode is enabled, Authentication Manager places the client in Monitor mode as applicable.   


#### 3.3.2.10 Authentication History   
Authentication Manager maintains a database of authentication events. Events like “Authorized” or “Unauthorized” are recorded along with the timestamp, port number, client MAC address, and the authentication method used.   


#### 3.3.2.11 Open Authentication  
The Open Authentication capability allows Authentication Manager to allow client traffic event before it authenticates. This is typically used to allow certain devices to allow access to network resources prior to authenticating to obtain IP address and download configuration or firmware upgrades. Once the information is downloaded, the device will authenticate to the network.  

Open Authentication is configured per interface. The open authentication settings are ignored for force-authorized and force-unauthorized ports.   

Open Authentication is supported for all switchport modes (access, general and trunk). It is also supported in all Authentication Manager Host modes. The number of clients that get Open access before authentication is limited by the configured host mode on the port.   

When a packet is received from a client, it is allowed access in Open mode before the authentication sequence initiates. The exception to this is when the client sends an EAP packet. In this scenario the client is not placed in Open mode immediately. We should look for the first authentication method. If it is configured as 802.1x, authentication is attempted instead.   

A client authorized in Open mode is considered a Data client and is authorized on the data VLAN of the port. A client given access in Open mode has its VLAN assigned reason as Default VLAN. Periodic re-authentication is not supported for these clients.   

A client authentication will eventually trigger based on the available and configured authentication methods on the port and on the reception of packets from the client.   

If a client fails authentication, the authentication failure actions will continue to applicable (Critical VLAN, Unauthenticated VLAN, Guest VLAN, Monitor mode etc.). If however we do not have the required configuration to authorize the client in any of these mentioned methods, the client will have access in Open mode.   

If the client succeeds authentication, the authorization parameters from RADIUS will be applied as usual. However if a DACL or Filter-Id is received, the admin configured static ACLs on the port will not be removed. These will be applied on the port prior to the statically configured ACL. This is required for subsequent clients on the port that are authorized in Open mode. The static ACL would continue to apply on the traffic as intended.    


#### 3.3.3 mabd
mabd provides the Mac-based Authentication Bypass(MAB) functionality. MAB is intended to provide 802.1x unaware clients controlled access to the network using the devices’ MAC address as an identifier. This requires that the known and allowable MAC address and corresponding access rights be pre-populated in the authentication server.  

MAB is a supplemental authentication mechanism to allow 802.1x unaware clients to authenticate to the network. SONiC supported authentication methods for mab are as given below:
- CHAP
- EAP-MD5
- PAP

Mac-based Authentication Bypass (MAB) is configured per port. For MAB to be used for authentication it needs to be is configured as an authentication method in method list for the port by Authentication Manager, MAB authentication is done in the order in which the methods are configured. If first in the list, MAB occurs first. If second in the list after 802.1x, MAB will occur if 802.1X times out or fails. 802.1X timeout is determined by the following time period:   

Timeout  = (maxReAuthReqIdentity + 1) * txPeriod   

Authentication Manager initiates MAB authentication by conveying the MAC address of the client attempting to authenticate as the username. MAB then sends a request to the authentication server with the MAC address of the client in the user configured attribute 1 format (by default ‘hhXX:XXhh:hhXX:hhXX:hhXX:hhXX’) as the User-Name (Radius Attribute 1). This attribute is sent irrespective of the authentication type configured on that interface for MAB.   

The type of RADIUS attributes included in the Access-Request varies with the type of authentication type selected for MAB on a physical interface. An Access-Request MUST contain either an EAP-Message (Radius Attribute 79) or a User-Password (Radius Attribute 2) or a CHAP-Password (Radius Attribute 3).  An Access-Request MUST NOT contain all 3 or any 2 of the above listed attributes.  
If authentication type is configured as EAP-MD5 (also the default), the MD5 hash of the Mac address as the password in the EAP-Message (Radius Attribute 79) is sent to the authentication server.  
If authentication type is configured as PAP, MAC address of the client is sent as the password similar to the format of Attribute 1 as clear text as part of the User-Password (Radius Attribute 2).  
If authentication type is configured as CHAP, a randomly generated 16-octet challenge is sent as the CHAP-Challenge (Radius Attribute 60) along with the CHAP-Password (Radius Attribute 3). CHAP-ID is a unique number which is used to identify the session. MAC address of the client is taken and formatted using the configured Attribute 1 format and is used as ‘secret’ in deriving the CHAP-Password. CHAP-Password is calculated as MD5 (CHAP-ID | secret | CHAP-Challenge).

The authentication server checks its pre-populated database for the authorized Mac addresses and returns an ‘Access-Accept’ or an ‘Access-Reject’ depending on if the Mac address has been found in the database. This also makes it possible for the client to be placed in a RADIUS assigned VLAN or apply a specific Filter ID to the client traffic.

### 3.3.4 hostapd
Hostapd is an open source implementation of 802.1x standard and the Linux application is supplied with wpa_suplicant package. The wired driver module of hostapd is adapted to communicate with pacd via socket interface instead of directly controlling the interfaces. hostapd gets its configuration from the hostapd.conf file generated via jinja2 tempaltes by SONiC system.

### 3.3.5 hostapdmgr
hostapdmgr reads hostapd specific configuration from SONiC DBs and populates the hostapd.conf. It further notifies the hostapd to re-read the configuration file.

### 3.3.6 fpinfra Library
fpInfra provides OS layer and Network layer abstractions for authentication manager and mab modules. It is implemented as a shared library *libfpinfra.so* and can be included in C or C++ applications. Only one instance of the library can run in a program (Linux process).  The library needs to be initialized via the ``` int fpinfraInit(void) ``` API before making any fpinfra API calls. During fpinfra library initialization, it starts multiple threads (fpinfraTask, nimHpcTask, osapiTimerHandler) to achieve the functionality. The major modules of fpinfra are as given below:   
	
	- Network Interface Manager (NIM) 
	- osapi Layer (memory, string, network)
	- sysapi Layer
	- Semaphore
	- Message Queue
	- SLL
	- Timer
	- AVL
	- Buffer Pool
	- Task creation / deletion
	- osapiTimer
	- Configurator
	- resource constants

The fpinfra(nim) listens to netlink notifications from the kernel, translates them to respective NIM events and delivers them to the registered applications via callbacks.

### 3.3.7 Other Process

	
No change to other process.

### 3.3.8 Interaction between pacd hostapd and mabd

*hostapd(802.1X)*   

hostapd comes to know of an 802.1x client attempting authentication via an EAP exchange. It informs pacd of this client by conveying the client MAC. If the authentication method selected by pacd is 802.1X, pacd sends an event to hostapd for authenticating the user. 

hostapd informs pacd about the result of the authentication. hostapd also passes all the authorization parameters it receives from an AAA Server to the pacd. These are used for configuring the NAS to allow authenticated client traffic.

*mabd(MAB)*   

When user or client tries to authenticate and the method selected is MAB, the pacd sends an event to mabd for authenticating the user. The client’s MAC address is sent to mabd for the same. 

pacd learns client’s MAC address through an hardware rule to copy-to-CPU the packets from unknown source MAC addresses.

mabd informs pacd about the result of the authentication. mabd also passes all the authorization parameters it receives from an AAA Server to the pacd. These are used for configuring the NAS to allow authenticated client traffic.


## 3.4 SyncD
No changes to SyncD.

## 3.5 SAI
No change to SAI.

## 3.6 Manageability

### 3.6.1 Data Models
Since Openconfig models are not available, Openconfig dot1x and mab are proprietary yang model following openconfig style.

### 3.6.2 Configuration Commands

The following commands are used to configure PAC.  

#### 3.6.2.1 authentication critical recovery max-reauth
This command configures the number of supplicants that are re-authenticated per second. This configuration is for the entire system across all the supplicants on all ports. This is used to control the system and network load when the number of supplicants to be re-authenticated is large. These re-authentications can be triggered due to ‘reinitialize’ dead or alive server actions.

| Mode | Global Config |
| ---- | ------ |
| Syntax | [no] authentication critical recovery max-reauth |
| range | 1-50 |
| Default | 10 |
| Change history | SONiC 4.0 - Introduced |


#### 3.6.2.2 authentication monitor

This command enables the Authentication monitor mode on the switch. The purpose of Monitor mode is to help troubleshoot port-based authentication configuration issues without disrupting network access for hosts connected to the switch. In Monitor mode, a host is granted network access to an authentication enforced port even if it fails the authentication process. The results of the process are logged for diagnostic purposes.

| Mode | Global Config |
| ---- | ------ |
| Syntax | [no] authentication monitor |
| Default | disable |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.3 aaa authentication pac
This command configures the authentication method for port-based access to the switch. The additional methods of authentication are used only if the previous method returns an error, not if there is an authentication failure. The possible methods are as follows:   
- none: Uses no authentication.
- radius: Uses the list of all RADIUS servers for authentication

| Mode | Global Config |
| ---- | ------ |
| Syntax | aaa authentication pac \{ radius \| none \} |
| Default | radius |
| Syntax | no aaa authentication pac |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.4 mab request format attribute 1 
This command sets configuration parameters that are used to format attribute1 for MAB requests to the RADIUS server. RADIUS attribute 1 is the username, which is often the client MAC address

| Mode | Global Config |
| ---- | ------ |
| Syntax | mab request format attribute 1 groupsize \{ 1 \| 2 \| 4 \| 12 \} separator \{ \- \| \: \| \. } \[ lowercase \| uppercase \] |
| Default | group size=2 |
| Default | separator is : |
| Default | uppercase |
| Syntax | no mab request format attribute 1 |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.5 dot1x system-auth-control 
This command enables the dot1x authentication support on the switch. While disabled, the dot1x configuration is retained and can be changed, but is not activated.
| Mode | Global Config |
| ---- | ------ |
| Syntax | [no] dot1x system-auth-control |
| Default | disable |
| Change history | SONiC 4.0 - Introduced |


#### 3.6.2.6 authentication event no-response action authorize vlan
This command configures VLAN as guest vlan on an interface. The range is 1 to the maximum VLAN ID supported by the platformor alive server actions. By default, the guest VLAN is 0, i.e. invalid and is not operational.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event no-response action authorize vlan \<vlan-id\> |
| Default | 0 |
| Syntax | no authentication event no-response |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.7 authentication event fail action authorize vlan
This command configures the unauthenticated VLAN associated with the specified interface. This VLAN is used when the AAA server fails to recognize the client credentials and rejects the authentication attempt. The unauthenticated VLAN ID can be a valid VLAN ID from 1-Maximum supported VLAN ID (4093). By default, the unauthenticated VLAN is 0, i.e. invalid and not operational.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event fail action authorize vlan \<vlan-id\> |
| Default | 0 |
| Syntax | no authentication event fail action authorize vlan |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.8 authentication event fail retry
This command configures the  number of times authentication may be reattempted by the client before a port moves to the authentication fail VLAN.  The reattemps range is 1 to 5.
	
| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event fail retry \<max-attempts\> |
| range | 1-5 |
| Default | 3 |
| Syntax | no authentication event fail retry |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.9 authentication max-users
This command sets the maximum number of clients supported on an interface when multi-authentication host mode is enabled on the port. The maximum users supported per port is dependent on the product. The count value is in the range 1 - 48.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication max-users \<count\> |
| range | 1-48 |
| Default | 48 |
| Syntax | no authentication max-users |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.10 authentication periodic 
This command enables periodic reauthentication of the supplicant for the specified interface.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | [no] authentication periodic |
| Default | Disabled  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.11 authentication port-control
This command sets the authentication mode to use on the specified interface.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication port-control \{ auto \| force-authorized \| force-unauthorized \} |
| Default | auto  |
| Syntax | no authentication port-control |
| Change history | SONiC 4.0 - Introduced 

#### 3.6.2.12 authentication host-mode 
This command configures the host mode of a port. The configuration on the interface mode takes precedence over the global configuration of this parameter. 

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication host-mode \{ multi-auth \| multi-domain \| multi-host \| single-host \} |
| Default | multi-host  |
| Syntax | no authentication host-mode |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.13 authentication timer reauthentiate 
This command is used to configure the period of time after which the Authenticator attempts to reauthenticate a supplicant on the port. This command also provides an option to specify re-authentication time out value from the server (ex. Radius). When ‘server’ option is selected, the server supplied Session time out and Session Termination-action are used by Authenticator to reauthenticate a supplicant on the port .  By default server option is enabled. The reauthenticate seconds value range is 1 to 65535.

For reauthentication to happen after the configured or server provided timeout, the command “authentication periodic” should have enabled periodic reauthentication.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication timer reauthenticate \{ \<seconds\> \| server \} |
| Default | server  |
| Syntax | no authentication timer reauthenticate |
| range | 1-65535 |
| Change history | SONiC 4.0 - Introduced |


#### 3.6.2.14 authentication event server dead action 
This command configures the actions to take when all the authentication servers are dead. The command also configures the critical VLAN ID. If the VLAN ID is not specified, the port PVID is used as the critical VLAN ID.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event server dead action \[ \{ reinitialize \| authorize \} \] \[ vlan vlan-id \] \] |
| Default | Action: None  |
| Default | VLAN: Port PVID  |
| Syntax | no authentication event server dead action |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.15 authentication event server dead action authorize voice
This command enables authorization of voice devices on the critical voice VLAN when all the authentication servers are dead. The configured voice VLAN of the port, on which the voice device is connected, is used as the critical voice VLAN ID.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event server dead action authorize voice |
| Default | Action: None  |
| Syntax | no authentication event server dead action authorize |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.16 authentication event server alive action reinitialize
This command configures the actions to take when one authentication server comes back alive after all were dead. The reinitialize action triggers the re-authentication of supplicants authenticated on the critical VLAN. 

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event server alive action reinitialize |
| Default | Action: None  |
| Syntax | no authentication event server alive action |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.17 authentication open
This command configures Open Authentication mode on the port.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | [no] authentication open |
| Default | Disabled  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.18 authentication order
This command is used to set the order of authentication methods used on a port. The allowed methods to configure for SONiC are Dot1x and MAB. Ordering sets the order of methods that the switch attempts when trying to authenticate a new device connected to a port. If one method in the list is unsuccessful or timed out, the next method is atempted. Each method can only be entered once.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication order \{ dot1x \[ mab \] \| mab \[ dot1x \] \} |
| Default order | dot1x, mab  |
| Syntax | no authentication order |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.19 authentication priority
This command is used to set the priority for the authentication methods used on a port. The allowed methods to configure for SONiC are Dot1x and MAB. Authentication priority decides if the client, who is already authenticated, to re-authenticate with the  higher-priority method when the same is received.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication priority \{ dot1x \[ mab \] \| mab \[ dot1x \] \} |
| Default order | dot1x, mab  |
| Syntax | no authentication priority |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.20 mab
This command is used to enable MAC Authentication Bypass (MAB) on an interface. MAB is a supplemental authentication mechanism that allows 802.1X unaware clients – such as printers, fax machines, and some IP phones — to authenticate to the network using the client MAC address as an identifier. However MAB can also be used to authenticate 802.1x aware clients. This command also provides options to specify the type of authentication to be used, which can be either EAP-MD5 ,PAP,CHAP. If enabled, EAP-MD5 is used by default.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | [no] mab \[ auth-type \{ pap \| eap-md5 \} \| chap \] |
| Default | Disabled  |
| Change history | SONiC 4.0 - Introduced |

	
### 3.6.3 Show Commands

#### 3.6.3.1 show authentication interface
This command displays the authentication manager information for the interface 

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show authentication interface  \{ all \| \{ interface \<slot\/port\> \} \} |
| Change history | SONiC 4.0 - Introduced |

| Field   | Description |
| ------ | ------------------- |
| Authentication Manager Status | The admin status of Authentication on the switch. This is a global configuration. |
| Interface  | The interface for which authentication configuration information is being displayed. |
| Port Control Mode | The configured control mode for this port. Possible values are force-unauthorized | auto | unauthorized. |
| Host Mode | The authentication host mode configured on the interface. |
| Authentication Restart timer |  The is the quite period timer. The quite period is time in seconds before re-attempting authentication for a failed client. |
| Configured method order | The order of authentication methods used on the interface. |
| Enabled method order | The order of authentication methods used on the interface. |
| Configured method priority | The priority for the authentication methods used on the interface. |
| Enabled method priority | The priority for the authentication methods used on the interface. |
| Reauthentication Period | The period after which all clients on the interface will be reauthenticated. |
| Reauthentication Enabled | Indicates whether reauthentication is enabled on the interface. |
| Maximum Users | The maximum number of clients that can be authenticated on the interface if the interface is configured as multi-auth host mode. |
| Guest VLAN ID | The VLAN id to be used to authorize clients that time out or fail authentication due to invalid credentials. This is applicable only for 802.1x unaware clients. |
| Unauthenticated VLAN ID | The VLAN id to be used to authorize clients that that time out or fail authentication due to invalid credentials. This is applicable only for 802.1x clients. |
| Critical Vlan Id | The VLAN id to be used to authorize clients that that time out due to unreachable RADIUS servers. |
| Authentication Server Dead action | The action to be undertaken for data clients when all RADIUS servers are found dead. |
| Authentication Server Dead action for Voice | The action to be undertaken for voice clients when all RADIUS servers are found dead. |
| Authentication Server Alive action | The action to be undertaken for data clients when a RADIUS server comes back alive after all were found dead. |
| Allowed protocols on unauthorized port | The action to drop or forward the particular protocol packet from and to unauthorized clients on the port |
| Open Authentication | Indicates if Open Authentication is enabled on the interface. |

Example:
```
show  authentication interface 1/1

Authentication Manager Status.................. Enabled

Interface...................................... Eth1/1
Authentication Restart timer................... 300
Configured method order........................ mab undefined undefined
Enabled method order........................... mab undefined undefined
Configured method priority..................... dot1x mab captive-portal
Enabled method priority........................ dot1x mab undefined
Reauthentication Period (secs)................. 3600
Reauthentication Enabled....................... False
Maximum Users.................................. 48
Guest VLAN ID..... ............................ 0
Unauthenticated VLAN ID........................ 0
Critical Vlan Id............................... 0
Authentication Server Dead action.............. None
Authentication Server Dead action for Voice.... None
Authentication Server Alive action............. None
Allowed protocols on unauthorized port......... dhcp
Open Authentication............................ Disabled

```
#### 3.6.3.2 show authentication 
This command displays the authentication manager global information and the number of authenticated clients.

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show authentication |
| Change history | SONiC 4.0 - Introduced |

| Field   | Description |
| ------ | ------------------- |
| Authentication Manager Status | The admin status of Authentication on the switch. This is a global configuration. |
| Dynamic VLAN Creation Mode | Indicates whether the switch can dynamically create a RADIUS-assigned VLAN if it does not currently exist on the switch. |
| VLAN Assignment Mode | Indicates if RADIUS assigned VLAN can be used for the Authentication Manager client or not. |
| Authentication Monitor Mode | The admin status of Monitor mode on the switch. This is a global configuration. |
| Critical Recovery Max ReAuth | Indicates the number of supplicants that are re-authenticated per second. |
| Number of Authenticated clients | The total number of clients authenticated on the switch except the ones in Monitor Mode |
| Number of clients in Monitor Mode | The number clients authorized by Monitor mode on the switch.|

Example:
```
 #show authentication 

Authentication Manager Status.................. Disabled
Dynamic Vlan Creation Mode..................... Disabled
VLAN Assignment Mode........................... Disabled
Authentication Monitor Mode.................... Disabled
Critical Recovery Max ReAuth................... 10

Number of Authenticated clients................ 2
Number of clients in Monitor mode.............. 0

```

#### 3.6.3.3 show authentication clients
This command displays the details of the dot1x configuration for a specified port.

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show authentication clients  \{ all \| \{ interface \<slot\/port\> \} \} |
| Change history | SONiC 4.0 - Introduced |


| Field   | Description |
| ------ | ------------------- |
| Interface  | The interface for which authentication configuration information is being displayed. |
| Mac Address | The MAC address of the client. |
| User Name | The user name associated with the client. |
| VLAN Assigned Reason | This can take one of the following values |
| Host Mode | The authentication host mode configured on the interface. The possible values are multi-auth, multi-domain, multi-host and single-host. |
| Method | The method used to authenticate the client on the interface. The possible values are 802.1x. MAB, Captive Portal and None. |
| Control Mode | The configured control mode for this port. Possible values are force-unauthorized, auto and unauthorized. |
| Session Time | The amount of time the client session has been active. |
| Session Timeout | This value indicates the time for which the given session is valid. The time period in seconds is returned by the RADIUS server on authentication of the port. |
| Time left for Session Termination Action | This value indicates the time left for the session termination action to occur. This field is valid only when the “authentication periodic” is configured. |
| Session Termination Action | This value indicates the action to be taken once the session timeout expires. Possible values are Default and Radius-Request. If the value is Default, the session is terminated and client details are cleared. If the value is Radius-Request, then a reauthentication of the client is performed. |
| Filter ID | Identifies the Filter ID returned by the RADIUS server when the client was authenticated. This is a configured DiffServ policy name on the switch. |
| ACS ACL Name | Identifies the Downloadable ACL returned by the RADIUS server when the client was authenticated. The Downloadable ACL is the same as returned using CiscoSecure-Defined-ACL AVP.|
| DACL | Identifies the Downloadable Dynamic ACL returned by the RADIUS server when the client was authenticated. |


VLAN Assigned Reason can take one of the following values:
| VLAN Assigned Reason   | Description |
| ------ | ------------------- |
| Default VLAN | The client has been authenticated on the port default VLAN and the authentication server is not RADIUS. |
| RADIUS | RADIUS is used for authenticating the client. |
| Voice VLAN | The client is identified as a Voice device. |
| Critical VLAN | The client has been authenticated on the Critical VLAN |
| Unauthenticated VLAN | The client has been authenticated on the Unauthenticated VLAN. |
| Guest VLAN | The client has been authenticated on the Guest VLAN. |
| Monitor Mode | The client has been authenticated by Monitor mode. |


Example:
```
(dhcp-10-130-86-200) #show authentication clients all


(dhcp-10-130-86-142) (Interface 1/16)#show  authentication  clients all

Interface  MAC-Address         Method   Host Mode    Control Mode  VLAN Assigned Reason
---------  -----------------   -------  ------------ ------------  --------------------------
Eth1/16    10:8D:B6:C6:00:00   802.1X   multi-host   auto     RADIUS Assigned VLAN (10)

	
	
(dhcp-10-130-86-200) #show authentication clients interface 1/2

Mac Address.................................... 58:05:94:1C:00:00
User Name...................................... testixia
VLAN Assigned Reason........................... Voice VLAN (100)
Host Mode ..................................... Multi-auth
Method......................................... 802.1X
Control Mode................................... Auto
Session time ... .............................. 10
Session timeout ............................... 100
Time left for Session Termination Action....... 90
Session Termination Action..................... Default
Filter-Id ..................................... None
ACS ACL Name................................... xACSACLx-IP-FP_ACL-5ee227a2
DACL........................................... None
Session Termination Action..................... Default
Acct SessionId:................................ testixia:200000003

```





#### 3.6.3.4 show authentication authentication-history 
This command displays the authentication manager authentication history log for an interface

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show authentication authentication-history \{ interface \<slot\/port\> \} |
| Change history | SONiC 4.0 - Introduced |

Example:
```
show authentication authentication-history interface 1/2 


Timestamp             Interface  MAC-Address        Auth Status   Method
--------------------  ---------  -----------------  ------------  ------
May 07 2020 13:02:41  Eth1/2     58:05:94:1C:00:00  Unauthorized  802.1X
May 07 2020 13:01:33  Eth1/2     58:05:94:1C:00:00  Unauthorized  802.1X

```

#### 3.6.3.5 show mab
This command is used to show a summary of the global mab configuration and summary information of the mab configuration for all ports. This command also provides the detailed mab sessions for a specified port. 

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show mab  \{ \<cr\> \| \{ interface \<slot\/port\> \} \}  |
| Change history | SONiC 4.0 - Introduced |

Example:
```
dhcp-10-130-86-142) #show  mab 

MAB Request Fmt Attr1 Groupsize... 2
MAB Request Fmt Attr1 Separator... legacy(:)
MAB Request Fmt Attr1 Case........ uppercase

Interface    Admin Mode     Auth-type    
---------    -----------    ---------
Eth1/1       Disabled       N/A          
Eth1/2       Disabled       N/A          
Eth1/3       Disabled       N/A          


(dhcp-10-130-86-142) #show  mab interface 1/10
Interface    Admin Mode     Auth-type    
---------    -----------    ---------
Eth1/10      Enabled        eap-md5    

```

#### 3.6.3.6 show dot1x
This command is used to show a summary of the global dot1x configuration.

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show dot1x  |
| Change history | SONiC 4.0 - Introduced |

| Field   | Description |
| ------ | ------------------- |
| Administrative Mode | Indicates whether 802.1x is enabled or disabled. |

	
Example:
```
#show dot1x

Administrative Mode............... Enabled

```

### 3.6.4 Clear Commands

#### 3.6.4.1 clear authentication sessions
This command  clears information for all Auth Manager sessions. All the authenticated clients are re-intialized and forced to authenticate again.

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | clear authentication session |
| Change history | SONiC 4.0 - Introduced |


# 4 Flow Diagrams
The flow diagrams Figure 2: PAC service daemon and configuration flow, Figure 3: EAPOL receive flow, Figure 4: MAB PDU receive flow; indicates the sequence of events involved in processing of PAC configuration and EAPOL/MAB packets.

# 5 Error Handling
N/A

# 6 Serviceability and Debug
show commands will help to see if the PAC is active on a port.   
All processing errors will be captured in syslog.    
Debug command output will be captured as part of tech support for offline analysis.   

# 7 Warm Boot Support
- Configured actions and counters continue to work across warm reboot.
- Already authenticated sessions continues to work.
- Authentication is restarted for the Clients being authenticated at the time of warmboot.
- Statistics are preserved across warmboot.
- HW and SW entries for authenticated clients are preserved across warmboot.
- Authenticated client traffic is not disrupted.
- Clients in the process of authentication will need to re-initiate their authentication process.

# 8 Scalability

## 8.1 Software scalability
The following is the support scale for Port Access Control. The following numbers are recommended numbers and are not enforced by the software. Its expected that the user will not configure beyond the recommended scale.

| Configuration / Resource   | Scale |
| ------ | ------------------- |
| authentication history entries | 1024 |
| authentication history entries per interface | 20 |
| clients that can be authorized on a port configured in Multi-Auth host mode | 48 |
| Number of clients supported by the switch | 512 |


# 9 Limitation

- SONiC PAC allows only unidirectional (In) control where the incoming traffic is blocked while the port is not authenticated. PAC does not have any control on traffic egressing out of the port.

- Authentication Manager does not automatically apply any additional configuration for the respective authentication methods to authenticate successfully. The administrator needs to ensure that the correct and appropriate configuration is present in the system. For example, if the authentication order method includes the 802.1x port authentication method, 802.1X should be enabled for the authentication to succeed. Authentication manager will not enable/disable and make the configurations related to 802.1X. Administrator should make the necessary configurations.

- Authentication Manager cannot be enabled on LAG interfaces. Enabling Authentication Manager on ports which are member of LAGs or including an Unauthorized port into a LAG will result in unpredictable results.


# 10 Upgrade / Downgrade considerations

Compatibility across software versions are not guaranteed. Upgrade/downgrade across non-compatible versions needs to be treated as a service affecting upgrade. Upgrade downgrade to a compatible software version can be done in conjunction with a warm boot to avoid disruption to already authenticated client sessions. Software compatibility related information can be found in the release notes.   

# 11 Unit Test

*fpinfra*   

fpinfra is a shared library that provides C APIs. Its unit tested using a C/C++ based test utility that exercises these APIs in sequence. The unit test is build up on the google test (gtest) infrastructure. The test infra relies on the automake "make check" and gets executed during the compliation of sonic-secutity docker. The test code is spread into the following file/modules.
1. Semaphore API tests.
2. Task API tests
3. Linked list API tests.
4. Buffer managment API tests.
5. Tree API tests.
6. OS API tests.
7. NIM task and API tests.

# 12 Appendix: Sample configuration


**Using Sonic-CLI**
```
configure
aaa authentication dot1x default radius

interface 1/1
authentication order dot1x mab
authentication priority dot1x mab
authentication host-mode multi-auth
authentication event fail action authorize vlan 30
authentication event no-response action authorize vlan 30
mab
exit

dot1x system-auth-control
exit
```

# 13 Internal Design Information

Internal BRCM information to be removed before sharing with the community.

Feature shall be qualified on the below mentioned platforms.   
*AS4630*
- 4 Core CPU
- 8 GB RAM
- 16G Disc
- TD3-X3

## 13.1 Future Design Enhancements

