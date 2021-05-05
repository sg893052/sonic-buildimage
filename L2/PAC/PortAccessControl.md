
# Port Access Control in SONiC

High level design document version 0.3

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
		- [3.2.3 ASIC DB](#324-asic-db)
		- [3.2.4 Counter DB](#325-counter-db)
		- [3.2.5 State DB](#323-state-db)
	- [3.3 Switch State Service Design](#33-switch-state-service-design)
		- [3.3.1 Orchestration Agent](#331-orchestration-agent)
		- [3.3.2 PAC daemons](#332-pac-daemons)
			- [3.3.2.1 PAC Manager](#3321-pac-manager)
			- [3.3.2.2 Authentication Manager](#3322-authentication-manager)
			- [3.3.2.3 mabd](#3323-mabd)
		- [3.3.3 Other Process](#333-other-process)
	- [3.4 SyncD](#34-syncd)
	- [3.5 SAI](#35-sai)
	- [3.6 Manageability](#36-manageability)
		- [3.6.1 Data Models](#361-data-models)
		- [3.6.2 Configuration Commands](#362-configuration-commands)
			- [3.6.2.1 authentication enable](#3621-authentication-enable)
			- [3.6.2.2 authentication critical recovery max-reauth](#3622-authentication-critical-recovery-max-reauth )
			- [3.6.2.3 authentication monitor](#3623-authentication-monitor)
			- [3.6.2.4 aaa authentication pac](#3624-aaa-authentication-pac)
			- [3.6.2.5 mab request format attribute 1](#3625-mab-request-format-attribute-1)
			- [3.6.2.6 dot1x system-auth-control](#3626-dot1x-system-auth-control)
			- [3.6.2.7 authentication event no-response action authorize vlan](#3627-authentication-event-no-response-action-authorize-vlan)
			- [3.6.2.8 authentication event fail action authorize vlan](#3628-authentication-event-fail-action-authorize-vlan)
			- [3.6.2.9 authentication event fail retry](#3629-authentication-event-fail-retry)
			- [3.6.2.10 authentication max-users](#36210-authentication-max-users)
			- [3.6.2.11 authentication periodic](#36211-authentication-periodic)
			- [3.6.2.12 authentication port-control](#36212-authentication-port-control)
			- [3.6.2.13 authentication host-mode](#36213-authentication-host-mode)
			- [3.6.2.14 authentication timer reauthentiate](#36214-authentication-timer-reauthentiate)
			- [3.6.2.15 authentication event server dead action](#36215-authentication-event-server-dead-action)
			- [3.6.2.16 authentication event server dead action authorize voice](#36216-authentication-event-server-dead-action-authorize-voice)
			- [3.6.2.17 authentication event server alive action reinitialize](#36217-authentication-event-server-alive-action-reinitialize)
			- [3.6.2.18 authentication open](#36218-authentication-open)
			- [3.6.2.19 authentication order](#36219-authentication-order)
			- [3.6.2.20 authentication priority](#36220-authentication-priority)
			- [3.6.2.21 mab](#36221-mab)
			- [3.6.2.22 dot1x timeout](#36222-dot1x-timeout)
		- [3.6.3 Show Commands](#363-show-commands)
			- [3.6.3.1 show authentication interface](#3631-show-authentication-interface)
			- [3.6.3.2 show authentication](#3632-show-authentication)
			- [3.6.3.3 show authentication clients](#3633-show-authentication-clients)
			- [3.6.3.4 show authentication authentication-history](#3634-show-authentication-authentication-history)
			- [3.6.3.5 show mab](#3635-show-mab)
			- [3.6.3.6 show dot1x](#3636-show-dot1x)
		- [3.6.4 Clear Commands](#364-clear-commands)
			- [3.6.4.1 clear authentication sessions](#3641-clear-authentication-sessions)
			- [3.6.4.2 clear dot1x statistics](#3642-clear-dot1x-statistics)
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

# About this Manual
This document describes the design details of the Port Access Control feature in SONiC. Port Access Control (PAC) feature provides validation of client and user credentials to prevent unauthorized access to a specific switch port.

# Scope
This document describes the high level design details about Port Access Control feature implementation in SONiC.


# Definition / Abbreviation

## Table 1 Abbreviations
| **Term** | **Meaning**          |
| -------- | -------------------- |
| CoPP     | Control Plane Policing |
| Dot1x    | IEEE 802.1 standard  |
| EAPOL    | Extensible Authentication Protocol over LAN  |
| MAB      | MAC Authentication Bypass|
| PAC      | Port Access Control  |
| PAE      | Port Access Entity |
| RADIUS   | Remote Authentication Dial In User service |
| SONiC-CLI | Klish CLI used by management framework |

# 1 Feature Overview

## 1.1 Port Access Control
Port Access Control (PAC) feature provides validation of client and user credentials to prevent unauthorized access to a specific switch port.

Local Area Networks (LANs) are often deployed in environments that permit unauthorized devices to be physically attached to the LAN infrastructure, or permit unauthorized users to attempt to access the LAN through equipment already attached. In such environments, it may be desirable to restrict access to the services offered by the LAN to those users and devices that are permitted to use those services.  Port access control makes use of the physical characteristics of LAN infrastructures in order to provide a means of authenticating and authorizing devices attached to a LAN port that has point-to-point connection characteristics and of preventing access to that port in cases in which the authentication and authorization process fails. In this context, a port is a single point of attachment to the LAN, such as Ports of MAC bridges and associations between stations or access points in IEEE 802.11 Wireless LANs.

### 1.1.1 Dot1x 

IEEE 802.1X is an IEEE Standard for Port Access Control (PAC) that provides an authentication mechanism to devices wishing to attach to a LAN. The standard defines Extensible Authentication Protocol Over LAN (EAPOL). The 802.1X standard describes an architectural framework within with authentication and consequent actions take place. It also establishes the requirements for a protocol between the authenticator and the supplicant, as well as between the authenticator and the authentication server. 

### 1.1.2 MAC Authentication Bypass
Simple devices like camera or printers which do not support 802.1x authentication can make use of MAB feature where the device gets authenticated based on the device MAC address.

## 1.3 Requirements

### 1.3.1 Functional Requirements

*PAC*   
The following are the requirements for Port Access Control feature:
1. PAC is supported on physical interfaces only.
2. The interfaces cannot be part of a port-channel / LAG.
3. PAC is not supported on service port.
4. User can configure PAC interfaces for switches using the following features:
   - 802.1x
   - MAB (MAC-based authentication bypass).
5. MAB, and dot1x need support for access port.
6. PAC supports the following Dynamic ACLs:
   - Named ACLs
   - Dynamic ACLs
   - Filter Id
7. SONiC supports Single-Host mode where only one data client can be authenticated on a port and is granted access to the port at a given time.
8. SONiC supports Multiple Hosts mode where only one data client can be authenticated on a port and after that access is granted to all clients connected to the port
9. SONiC supports Multi Domain Authentication (MDA), where 802.1X is extended to allow authorization of multiple devices (a data and a voice device) on a single port and assign different VLANs to the devices based on authorization.
    - MDA is enabled by default; however, you need to assign a tagged VLAN for voice devices (see Configure 802.1X Interfaces for a VLAN-aware Bridge).
    - A maximum of four authorized devices (MAB + EAPOL) per port are supported.
    - The 802.1X-enabled port must be a trunk port to allow tagged voice traffic from a phone; you cannot enable 802.1X on an access port.
    - Only one untagged VLAN and one tagged VLAN is supported on the 802.1X enabled ports.
    - Multiple MAB (non voice) devices on a port are supported for VLAN-aware bridges only. Authorization of multiple MAB devices for different VLANs is not supported.
10.  SONiC supports Multiple Authentication mode where one voice client and multiple data clients can be authenticated on a port and these clients are then granted access.
11.  SONiC supports the following specical VLANs:
     - Unauthenticated VLAN
     - Guest VLAN
     - Monitor VLAN
     - Open VLAN
     - Critical VLAN
     - Voice VLAN
12. The following PAC port modes are supported on SONiC: 
    - Auto
    - Force Authorized
    - Force Unauthorized

*802.1x*   
1. SONiC shall make use of opensource 802.1x implementation **hostapd** suppied with **wpa_supplicant** package.

*MAB*   
The following are the requirements for MAB feature:
1. MAB can be used to authenticate clients that do not support EAP.
2. A port can be configured to be either doing port-based authentication or doing Mac-based authentication. But only one mode at a given instance.
3. A port when enabled for Dot1x authentication needs to explicitly mention whether it is being enabled for port-based authentication mode or Mac-based authentication mode.
4. When a port is configured for Mac-based Dot1x Authentication, then if at least one client has actively authentication itself then port is placed in authorized state for other components.
5. When a port is configured for Mac-based Dot1x Authentication, then port is placed in un-authorized state if the last active authorized client ceases to exist.
6. When a port is operational in Mac-based Dot1x Authentication, then only the data traffic arriving on the port from authorized clients need to be forwarded.
7. When a port is operational in Mac-based Dot1x Authentication, accept EAPOL packets from all the clients (both authorized and unauthorized as well).
8. When a port is enabled for Guest vlan, then Mac-based dot1x authentication can not be enabled on that port.
9. When a port is enabled for vlan assignment, then Mac-based dot1x authentication could also be enabled on that port. As the vlan assignment is based on the user details of the client.
10. To enable Mac-based dot1x authentication on port it needs to be an Edge port. (Dot1s Configuration).
11. If Mac-based dot1x authentication is enabled on a port and if Mac-based VLANs or Subnet VLANs or Protocol VLANs are also enabled, then Mac-based dot1x authentication is disabled.
12. If Port-Mac Locking is enabled on the port and if Mac-based dot1x authentication is also enabled on the port. Then Port-Mac Locking needs to be disabled.
13. If a client already authentication on one port has roamed to another port on the switch, then the state (status, vlan etc) of the client on the prior port needs to be cleaned.
14. Timeout mechanism needs to be maintained per client so that inactive client's status can be removed on the switch.
15. Multicast/Broadcast traffic arriving on the port that is enabled for Mac-based authentication would be forwarded accordingly. If the source Mac-address of the packet is from a authenticated user.
16. Even when the port is authorized, dot1x would still continue to send EAPOL for authentication as more users could join on the port.
17. Unauthenticated Vlan is per port configuration. By default unauthenticated vlan is zero (invalid and not operational).
18. In port-based dot1x, if the client fails authentication then port is placed in the unauthenticated vlan (if configured)
19. In mac-based dot1x, if the client fails authentication then client-mac is associated to the unauthenticated vlan (if configured).
20. Unauthenticated vlan deprecates the Guest vlan feature of supplicant mode, as it would be redundant. 

*RADIUS*   
1. User can configure up to three RADIUS servers (in case of failover). However, do not use a SONiC switch as the RADIUS server.
2. RADIUS authentication is supported with FreeRADIUS and Cisco ISE.


### 1.3.2 Configuration and Management Requirements

### 1.3.3 Scalability Requirements

### 1.3.4 Warm Boot Requirements
Port Access Control feature should work seamlessly across warmboot. Statistics must be preserved across warmboot.

## 1.4 Design Overview

### 1.4.1 Basic Approach

### 1.4.2 Container
A new container sonic-security is introduced to hold all the port security applications. Apart from sonic-security container, code changes are made to SWSS, mgmt-frameowrk containers.
 
### 1.4.3 SAI Support
No changes to SAI spec for supporting PAC.

# 2 Functionality

## 2.1 Target Deployment Use Cases

When a client authenticates itself initially on the network, the Switch acts as the authenticator to the clients on the network and forwards the authentication request to the Radius server in the network. If the authentication succeeds then the client is placed in authorized state and the client is able to forward or receive traffic through the port.

If the vlan assignment is enabled in the Radius server, then as part of the response message, Radius server sends the vlan id the client is supposed to be in the 802.1x tunnel attributes. This implies that the client can connect from any port and can get assigned to the appropriate vlan that it is supposed to be in; this is configured in the radius server. This gives flexibility for the clients to move around the network with out much configuration need to be done by the administrator.


## 2.2 Functional Description

The Ports of an 802.1X Authenticator switch provide the means in which it can offer services to other systems reachable via the LAN. Port-based network access control allows the operation of a System's Port(s) to be controlled in order to ensure that access to its services is only permitted by Systems that are authorized to do so.   

Port Access Control provides a means of preventing unauthorized access by Supplicants to the services offered by a System. Control over the access to a switch and the LAN to which it is connected can be desirable in order to restrict access to publicly accessible bridge ports or to restrict access to departmental LANs.   

Access control is achieved by enforcing authentication of Supplicants that are attached to an Authenticator's controlled Ports. The result of the authentication process determines whether the Supplicant is authorized to access services on that controlled Port.   

A PAE is able to adopt one of two distinct roles within an access control interaction:   
1. authenticator: A Port that enforces authentication before allowing access to services available via that Port.
2. supplicant: A Port that attempts to access services offered by the Authenticator.   

Additionally, there exists a third role:   
3. authentication server: Performs the authentication function necessary to check the credentials of the Supplicant on behalf of the Authenticator.  

All three roles are required in order to complete an authentication exchange. SONiC supports the Authenticator role only, in which the PAE is responsible for communicating with the Supplicant. The Authenticator PAE is also responsible for submitting the information received from the Supplicant to the Authentication Server in order for the credentials to be checked, which will determine the authorization state of the Port. The Authenticator PAE controls the authorized/unauthorized state of the controlled Port depending on the outcome of the authentication process.

### 2.2.1 Local vs. RADIUS Authentication
There are two methods that can be used for authenticating a user for a particular port on a switch. These methods are described in the following sections.   

*Local Authentication*   
Local authentication consists of matching a user ID/password combination from the Supplicant to a locally-stored user database. The user ID that is returned in response to an EAP-Request/Identity is matched against the database. If a match is found, an EAP-Request/MD5 challenge request is sent to the Supplicant. The Supplicant then takes the challenge, its password and the EAP ID and derives an MD5 response. The MD5 response is then send to the Authenticator which uses the locally stored password to derive the answer and compare it to the Supplicants response. A match results in a successful authentication of the port.   

Note that the SONiC Authenticator supports only the EAP-MD5 authentication type for local authentication.   


*RADIUS Authentication*   

When RADIUS authentication is used, the Authenticator basically becomes a passthrough. The Supplicant and the RADIUS server exchange EAP messages which are encapsulated in either EAPOL or RADIUS frames (depending on the direction of the frame) by the Authenticator switch. The Authenticator determines the authorization status of the port based on RADIUS Access-Accept or Access-Reject frames. The Authenticator switch also needs to send and process all appropriate RADIUS attributes. For more information on these attributes, see the section labelled "RADIUS Authorization Attributes" in this document.

### 2.2.2 Unidirectional and bidirectional control
The controlled directions dictate the degree to which protocol exchanges take place between Supplicant and Authenticator. This affects whether the unauthorized controlled Port exerts control over communication in both directions (disabling both incoming and outgoing frames) or just in the incoming direction (disabling only the reception of incoming frames). The control direction are of two type:   
1. Both: Control is exerted over both incoming and outgoing frames.
2. In: Control is only exerted over incoming traffic.   
SONiC allows only unidirection(In) control. Please see "Limitations and Restrictions" section.


### 2.2.3 Downloadable ACL
PAC (Port access control) feature brings in DACL (downloadable ACL) support into SONiC. As a part of PAC, once a client on an access controlled port is authenticated, the external RADIUS server can send ACL attributes based on user profile configuration on the RADIUS server. These are called Downloadable ACL’s. IPv6 and IPv4 ACLs are supported for DACL. The downloadable ACL rules per client are sent in extended ACL syntax style. The switch applies the client specific DACL for the duration of the authenticated session.   

The switch does not display RADIUS specified DACL’s in the running configuration. The ACL however shows up in the user interface show commands. Essentially, the DACL configuration is temporary (applied for the duration of the authenticated client session) and not persistent. The downloadable ACLs sent by RADIUS are in extended ACL syntax style and are validated just like user created ACLs. The ACLs created by the applications are owned by the internal application and hence cannot be deleted by a user.   

Generally, any static ACLs (created by user) applied on the port are removed prior to applying the dynamic ACL on the port. Once the application created dynamic ACL is removed/deleted, the static ACLs is re-applied on the port. Essentially, static ACLs and dynamic ACLs are mutually exclusive. However in certain situations, the static ACLs and dynamic ACLs co-exist on the port. In such situations, the static ACLs have lower priority than the dynamic ACLs attached on the port. Amoung DACLs IPv6 ACL have higher prority over IPv4. In situations where the client IP address changes, the application created ACLs are automatically updated to accommodate the operational change like a changed client IP address.   


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
- The learning mode of a port (or bridge port) was configured and controlled completely at the orchestration layer. With PAC, the application layer (pacd) also manages the learning mode to
	1. Once PAC is enabled on a port, all incoming traffic on the port are blocked/dropped except certain protocol traffic.
	2. PAC turns off learning on the port essentially dropping all unknown source MAC packets. This achieves the requirement of blocking ingress traffic.
	3. Egress traffic on the port is not blocked.
	4. Once a client starts the authentication process, the client is no longer unknown (unknown source MAC). However, since authentication is yet to be completed,
traffic for the client must still get dropped. To achieve the same, PAC installs a static FDB entry with discard bits set, is installed to mark the client "known" so that the incoming traffic does not flood the CPU.
- For MAB, unknown source MAC packets are trapped to the CPU.
- Station movement is also handled i.e if a packet is received from another port on a MAC, VLAN pair for which PAC installed a static FDB entry, such packets also get trapped to the CPU.


# 3 Design

## 3.1 Overview

[Figure 1](#pac-config-flow) shows the high level design overview of PAC services in SONiC. PAC Services Daemon is composed of multiple sub-modules. The main module i.e. PAC daemon handles the authentication related commands and makes use of hostApd and mabd daemons to authenticate a client via dot1x and mab respectively. hostApd being a standard Linux application takes hostapd.conf as its config file. hostApdMgr takes care of listening to dot1x specific configuration and translating them to respective hostapd.conf file config entries. pacd daemon being the main module decides which authentication protocol needs to be used for a given port and also calls APIs to program the polices in hardware.

### 3.1.1 Configuration flow

![pac-config-flow](https://user-images.githubusercontent.com/45380242/115655812-a91f2080-a351-11eb-9207-26dafc103d8e.PNG)

**Figure 1: PAC service daemon and configuration flow**

1. Mgmt interfaces like CLI and REST writes the user provided configuration to CONFIG_DB.
2. The pacd, mabd and hostApdMgr gets notified about their respective configuration.
3. hostApd being a standard Linux application gets its configuration from hostapd.conf file. hostApdMgr makes use of Jinja2 templates to generates the hostapd.conf file based on the relevant CONFIG_DB tables.
4. Pacd gets to know about the list of ports that needs to be authenticated from PAC_PORT_CONFIG_TABLE on CONFIG_DB. The same table provides info on which ports supporrts DOT1X and which supports MAB and priority amoung the authentication methods. Based on the priority and authentication failure status, pacd decides on the list of ports to be authenticated via DOT1X and the list of ports that needs to be authenticated via MAB. It communicates the respective list of interfaces to hostApd and mabd via Unix domain socket messages.
5. hostApd listens to EAPOL PDUs on the provided interface list and proceeds to authenticate the client when it receives a PDU. mabd listens to DHCP and EAPOL PDUs on the provided interface list and proceeds to authenticate the client when it receives a PDU.



### 3.1.2 EAPOL receive flow


![EAPOL-receive-flow](https://user-images.githubusercontent.com/45380242/115655906-ceac2a00-a351-11eb-9095-9d53ae549ad7.PNG)


**Figure 2: EAPOL receive flow**

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


**Figure 3: MAB PDU receive flow**

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

*PAC*   
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
"auth_fail_vlan": 100
}
}


key = PAC_PORT_CONFIG_TABLE:port ; Physical port
;field = value
method_list         = "dot1x"/"mab" ; List of methods to be used for authentication
priority_list       = "dot1x"/"mab"; Relative priority of methods to be used for authentication
port_control_mode   = "auto"/"force_authorized"/"force_unauthorized"; Port control mode
                                                                  ; 'auto": authentication enforced on port
								  ; 'force_authorized": authentication not enforced on port
								  ; 'force_unauthorized": authentication not enforced on port but port is blocked for all traffic
								  
host_control_mode   = "multi-host"/"multi-domain"/"multi-auth"/"single-auth" ; Host control mode
                                                    ; "multi-host": One data client can be authenticated on the port. Rest of the clients tailgate once the first client is authenticated.
						    ; "multi-domain": One data client and one voice client can be authenticated on the port.
						    ; "multi-auth": Multiple data client and one voice client can be authenticated on the port.
						    ; "single-auth": One data client or one voice client can be authenticated on the port.

quiet_period = 1*5DIGIT ; The initial value of the timer that defines the period during which the Authenticator will not attempt to authenticate the Supplicant. Range is 0-65535 seconds.
reauth_period = 1*10DIGIT;The initial value of the timer that defines the period after which the Authenticator will reauthenticate the Supplicant. Range is 3600 - 4294967296 seconds.
reauth_enable = "true"/"false";Indicates whether Reauthentication is enabled on the port.
max_users_per_port=1*2DIGIT; Maximum number of clients that can be authenticated on the port. This is applicable only for "multi-auth" host mode.Range is 1 - 64 clients.
guest_vlan_id= 1*4DIGIT;The Guest VLAN Id for the port. Range is 1 - 4093
auth_fail_vlan_id=1*4DIGIT;The Authentication Fail VLAN Id for the port. Range is 1 - 4093
max_auth_attempts=1DIGIT;The maximum number of authentication retries in the event of authentication failure.Range is 1 - 5
```   

```   
"PAC_GLOBAL_CONFIG_TABLE": {
"authentication_enable": "true",
"monitor_mode_enable": "false"
}
    
;field=value
authentication_enable = "true"/"false";Indicates whether PAC is enabled in the system.
monitor_mode_enable   = "true"/"false";Indicates whether monitor mode is enabled in the system.

```   

*hostapd*   
```
" HOSTAPD_GLOBAL_CONFIG_TABLE ": {
"dot1x_system_auth_control": "enable"
}

;field = value 
dot1x_system_auth_control "true"/"false" ; Indicates whether dot1x is enabled in the system.
```

### 3.2.2 App DB

### 3.2.3 ASIC DB

None

### 3.2.4 Counter DB

```
"HOST_APD_STATS_TABLE": [{
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

*PAC*   
```   
" PAC_PORT_OPER_TABLE ": {
"ethernet1": {
"enabled_method_list": [
"802.1x",
"mab"
],
"enabled_priority_list": [
"802.1x",
"mab"
],
"num_clients_authenticated": 10
}
}

key = PAC_PORT_OPER_TABLE : port ; Physical port
;field = value
enabled_method_list       = "dot1x"/"mab" ; List of methods to be used for authentication
enabled_priority_list     = "dot1x"/"mab" ; Relative priority of methods to be used for authentication
num_clients_authenticated = 1*2DIGIT ; Number of clients authenticated on the port.


```

```
" PAC_AUTHENTICATED_CLIENT_OPER_TABLE ": {
"ethernet1": [{
"00:00:00:11:02:33": {
"current_id": 21,
"auth_status": "authorized",
"authenticated_method": "802.1X",
"server_state": "36 34 43 50 4d 53 65 73 73 69 6f 6e 49 44 3d 30 61 38 32 62 39 37 36 4c 49
50 5a 44 45 4d 32 74 64 35 55 39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d
6e 4a 33 31 42 6a 5a 34 51 49 3b 33 34 53 65 73 73 69 6f 6e 49 44 3d 43 69 73 63 6f 49 53 45
2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35 3b",
"server_state_len": 106,
"server_class": "43 41 43 53 3a 30 61 38 32 62 39 37 36 4c 49 50 5a 44 45 4d 32 74 64 35 55
39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d 6e 4a 33 31 42 6a 5a 34 51 49
3a 43 69 73 63 6f 49 53 45 2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35",
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
"server_state": "36 34 43 50 4d 53 65 73 73 69 6f 6e 49 44 3d 30 61 38 32 62 39 37 36 4c 49
50 5a 44 45 4d 32 74 64 35 55 39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d
6e 4a 33 31 42 6a 5a 34 51 49 3b 33 34 53 65 73 73 69 6f 6e 49 44 3d 43 69 73 63 6f 49 53 45
2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35 3b",
"server_state_len": 106,
"server_class": "43 41 43 53 3a 30 61 38 32 62 39 37 36 4c 49 50 5a 44 45 4d 32 74 64 35 55
39 44 31 4c 37 43 56 44 37 5a 48 56 44 4f 70 74 4a 47 6b 7a 4d 6e 4a 33 31 42 6a 5a 34 51 49
3a 43 69 73 63 6f 49 53 45 2f 33 33 32 35 38 39 38 35 33 2f 38 36 36 33 35",
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


```   
" PAC_CLIENT_HISTORY_TABLE ": {
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


key = PAC_CLIENT_HISTORY_TABLE : port ; Physical Port Clientmac; Client MAC address
;field = value
authentication_method = "802.1x"/'mab" ; Method used to authenticate the client
backend_auth_method = "radius" ; Backend authentication method used to authorize the client.
auth_status = "authorized"/"unauthorized" ; Authorization status of the client

```

*hostapd*   
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

#### 3.3.1.1 PAC Orchestration agent

### 3.3.2 PAC daemons

#### 3.3.2.1 PAC Manager

#### 3.3.2.2 Authentication Manager
Authentication Manager primarily manages the order of authentication methods during a failover scenario. Majority of authentication functionalities are managed by 802.1X. These include interaction with a AAA server, applying client authorization parameters to allow authenticated client traffic, etc. These are strictly speaking not specific to 802.1X and are applicable to any authenticated methods like MAB.   

Authentication Manager allows enforcing authentication on a port. Authentication Manager needs to be enabled for the same. This is the first step to enabling port based access control. Once authentication is enabled, the port is marked Unauthorized and traffic is blocked through it.

Authentication Manager enables configuring various Authentication Host modes. This is configured per port. These modes determine the number of clients and the type of clients that can be authenticated and authorized on the ports. Distinction is made between Data and Voice clients.

Authentication Manager also enables configuring the authentication methods to be used for authenticating clients on a port. By default the configured authentication methods are tried in order for that port. SONiC allows the below authentication methods to get configured for each port.  
- 802.1X
- MAB

In the event that a port is configured for 802.1X and MAB in this sequence, the port will first attempt to authenticate the user through 802.1X. If 802.1X authentication times out, the switch will attempt MAB. The automatic sequencing of authentication methods allows the network administrator to apply the same configuration to every access port without having to know in advance what kind of device (employee or guest, printer or PC, IEEE 802.1X capable or not, etc.) will be attached to it.   

Authentication Manager allows configuring priority for each authentication method on the port. The default priority of a method is equivalent to its position in the order of the default authentication list.   
 
After successful authentication, the authentication method returns the Authorization parameters for the client. Authentication Manager uses these parameters for configuring the switch for allowing traffic for authenticated clients.

**Authentication Manager port modes**

*Auto*   
This mode is used to enforce authentication on a port. The port is unauthorized and blocked for traffic unless a client is authenticated.

*Force-Authorized*    
This mode is used to disable authentication on a port. All client traffic is allowed.

*Force-Unauthorized*   
This mode is used to un-authorize a port and block any client traffic


**Authentication Manager port host modes** 

*Single-Host mode*   
In this mode only one data client can be authenticated on a port and the client is granted access to the port. Access is allowed only for this client and no one else. Only when this client logs off, can another client get authenticated and authorized on the port and granted port access.   

*Multiple Hosts mode*   
In this mode only one data client can be authenticated on a port. However once authentication succeeds, access is granted to all clients connected to the port. Typical use case is a wireless access point which is connected to an access controlled port of a NAS. Once the access point is authenticated by the NAS, the port is authorized for traffic from not just the access point but also from all the wireless clients connected to the access point. Once this client gets authenticated, the port is open for all clients connected to the port.   

*Multiple Domain Authentication mode*   
In this mode one data client and one voice client can be authenticated on a port and these clients are then granted access. Typical use case is an IP phone connected to a NAS port and a laptop connected to the hub port of the IP phone. Both the devices need to be authenticated to access the network services behind the NAS. The voice and data domains are segregated. The RADIUS server attribute “Cisco-AVPair = "device-traffic-class=voice"” is used to identify a voice client.   

*Multiple Authentication mode*   
In this mode one voice client and multiple data clients can be authenticated on a port and these clients are then granted access. Typical use case is a network of laptops and an IP phone connected to the NAS port via a hub.   

**Authentication Manager Authentication method fallback and priorities**

Authentication manager controls the order in which the authentication methods are executed. Authentication manager does not make any required configuration for the respective methods to authenticate successfully. User or Administrator needs to ensure that the correct and appropriate configuration is present in the system.
Using the Authentication manager, user can configure an authentication method fallback list, which is configured per port. If authentication using any of the method fails, then authentication of the client on the port is tried using the next or subsequent methods.   

The default priority of a method is equivalent to its position in the order of the default authentication list, which is configured per port. If authentication method priorities are not configured, then the relative priorities (highest first) are in the same order as that of per port based authentication list. By configuring the authentication priority, user can over-ride the default priority.   

If the client is already authenticated using methods such as MAB and 802.1X happens to have higher priority than the authenticated method, if a 802.1X frame is received, then the existing authenticated client will be removed and authentication process would begin for the client using 802.1X.However if 802.1X is configured at a lower priority than the authenticated method, then the client will not be removed and the 802.1X frames will be ignored.   

Authentication manager allows user to modify the default method priorities using configuration. This is supported by configuring the priority order list for the authentication methods using the command authentication priority.   

If administrator changes the priority of the methods, then all the users who are authenticated using a lower priority method will be forced to re-authenticate. If an authentication session is in progress and administrator changes the order of the authentication methods then the configuration will take effect for the next session onwards.   

**Authorization parameters** 

Upon successful authentication, the authentication methods inform Authentication Manager about the result. Authentication Manager then authorizes the port and configures it for allowing traffic from the client.  

Authentication Manager receives the client authorization parameters from the authentication method after successful authentication of a client. The following parameters are acted upon:  

- *VLAN Id*: This is the VLAN ID sent by a RADIUS server. Authentication Manager configures the port membership accordingly so that the client traffic is associated with the VLAN.   
- *Session Timeout*: This is the timeout attribute of the authenticated client session.
- *Session Termination Action*: Upon session timeout, the Session Termination Action determines the action on the client session. The following actions are defined:
   - *Default*: The client session is torn down and authentication needs to be restarted for the client.
   - *RADIUS*: Re-authentication is initiated for the client.
- *Filter-Id*: Specifies an ACL of Diffserv policy name. This is used to apply a Static ACL or DiffServ policy on the port for the client. IPv4 and IPv6 ACLs in the “IN” direction is supported. If the Differv policy or ACL is not present in the system, or if a Diffserv policy is already configured on the port, authentication for the client is rejected. These are subject to Monitor Mode configuration. Filter-Id is supported on all Authentication Manager host modes.
- *Downloadable ACL*: DACLs are supported on all host modes.
- *Redirect ACL*: This is used to apply an ACL that traps matching packets to the CPU for redirection. It is typically used to match on HTTP packets from a client.
- *Redirect URL*: This is used to specify a redirect URL and works in conjunction with the Redirect ACL.

#### 3.3.2.3 mabd
MAB is intended to provide 802.1x unaware clients controlled access to the network using the devices’ MAC address as an identifier. This requires that the known and allowable MAC address and corresponding access rights be pre-populated in the authentication server.  

Today, 802.1x has become the recommended port-based authentication method at the access layer in enterprise networks. However, there may be 802.1x unaware devices such as printers, fax-machines etc that would require access to the network without 802.1x authentication. MAB is a supplemental authentication mechanism to allow 802.1x unaware clients to authenticate to the network. SONiC supported authentication methods are as below:
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


### 3.3.3 Other Process

No change to other process.

## 3.4 SyncD
No changes to SyncD.

## 3.5 SAI
No change to SAI.

## 3.6 Manageability

### 3.6.1 Data Models
Since Openconfig models are not available, Openconfig dot1x and mab are proprietary yang model following openconfig style.

### 3.6.2 Configuration Commands

The following commands are used to configure PAC.  

#### 3.6.2.1 authentication enable
This command enables PAC feature globally. By default the value is disabled. If enabled only, the configuration on the interface would take effect.

| Mode | Global Config |
| ---- | ------ |
| Syntax | authentication enable |
| Default | disable |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.2 authentication critical recovery max-reauth
This command configures the number of supplicants that are re-authenticated per second. This configuration is for the entire system across all the supplicants on all ports. This is used to control the system and network load when the number of supplicants to be re-authenticated is large. These re-authentications can be triggered due to ‘reinitialize’ dead or alive server actions.

| Mode | Global Config |
| ---- | ------ |
| Syntax | authentication critical recovery max-reauth |
| Default | 10 |
| Change history | SONiC 4.0 - Introduced |


#### 3.6.2.3 authentication monitor

This command enables the Authentication monitor mode on the switch. The purpose of Monitor mode is to help troubleshoot port-based authentication configuration issues without disrupting network access for hosts connected to the switch. In Monitor mode, a host is granted network access to an authentication enforced port even if it fails the authentication process. The results of the process are logged for diagnostic purposes.

| Mode | Global Config |
| ---- | ------ |
| Syntax | authentication monitor |
| Default | disable |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.4 aaa authentication pac
This command configures the authentication method for port-based access to the switch. The additional methods of authentication are used only if the previous method returns an error, not if there is an authentication failure. The possible methods are as follows:
ias. Uses the internal authentication server users database for authentication. This method can be used in conjunction with any one of the existing methods like local, radius, etc.
local. Uses the local username database for authentication.
none. Uses no authentication.
radius. Uses the list of all RADIUS servers for authentication

| Mode | Global Config |
| ---- | ------ |
| Syntax | aaa authentication pac \{ radius \| local \| none \} |
| Default | radius |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.5 mab request format attribute 1 
This command sets configuration parameters that are used to format attribute1 for MAB requests to the RADIUS server. RADIUS attribute 1 is the username, which is often the client MAC address

| Mode | Global Config |
| ---- | ------ |
| Syntax | mab request format attribute 1 groupsize \{ 1 \| 2 \| 4 \| 12 \} separator \{ \- \| \: \| \. } \[ lowercase \| uppercase \] |
| Default | group size=2 |
| Default | separator is : |
| Default | uppercase |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.6 dot1x system-auth-control 
This command enables the dot1x authentication support on the switch. While disabled, the dot1x configuration is retained and can be changed, but is not activated.
| Mode | Global Config |
| ---- | ------ |
| Syntax | dot1x system-auth-control |
| Default | disable |
| Change history | SONiC 4.0 - Introduced |


#### 3.6.2.7 authentication event no-response action authorize vlan
This command configures VLAN as guest vlan on an interface or a range of interfaces. The range is 1 to the maximum VLAN ID supported by the platformor alive server actions. By default, the guest VLAN is 0, i.e. invalid and is not operational.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event no-response action authorize vlan \<vlan-id\> |
| Default | 0 |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.8 authentication event fail action authorize vlan
This command configures the unauthenticated VLAN associated with the specified interface or range of interfaces. This VLAN is used when the AAA server fails to recognize the client credentials and rejects the authentication attempt. The unauthenticated VLAN ID can be a valid VLAN ID from 1-Maximum supported VLAN ID (4093 for FASTPATH). By default, the unauthenticated VLAN is 0, i.e. invalid and not operational.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event fail action authorize vlan \<vlan-id\> |
| Default | 0 |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.9 authentication event fail retry
This command configures the  number of times authentication may be reattempted by the client before a port moves to the authentication fail VLAN.  The reattemps range is 1 to 5.
	
| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event fail retry \<max-attempts\> |
| Default | 3 |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.10 authentication max-users
This command sets the maximum number of clients supported on an interface or range of interfaces when multi-authentication host mode is enabled on the port. The maximum users supported per port is dependent on the product. The count value is in the range 1 - 48.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication max-users \<count\> |
| Default | 48 |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.11 authentication periodic 
This command enables periodic reauthentication of the supplicant for the specified interface or range of interfaces.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication periodic |
| Default | Disabled  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.12 authentication port-control
This command sets the authentication mode to use on the specified interface or range of interfaces. The configuration on the interface mode takes precedence over the global configuration of this parameter.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication host-mode authentication port-control \{ auto \| force-authorized \| force-unauthorized \} |
| Default | auto  |
| Change history | SONiC 4.0 - Introduced 

#### 3.6.2.13 authentication host-mode 
This command configures the host mode of a port. The configuration on the interface mode takes precedence over the global configuration of this parameter. 

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication host-mode \{ multi-auth \| multi-domain \| multi-host \| single-host \| multi-domain-multi-host \} |
| Default | multi-host  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.14 authentication timer reauthentiate 
This command is used to configure the period of time after which the Authenticator attempts to reauthenticate a supplicant on the port. This command also provides an option to specify re-authentication time out value from the server (ex. Radius). When ‘server’ option is selected, the server supplied Session time out and Session Termination-action are used by Authenticator to reauthenticate a supplicant on the port .  By default server option is enabled. The reauthenticate seconds value range is 1 to 65535.

For reauthentication to happen after the configured or server provided timeout, the command “authentication periodic” should have enabled periodic reauthentication.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication timer reauthenticate \{ \<seconds\> \| server \} |
| Default | server  |
| Change history | SONiC 4.0 - Introduced |


#### 3.6.2.15 authentication event server dead action 
This command configures the actions to take when all the authentication servers are dead. The command also configures the critical VLAN ID. If the VLAN ID is not specified, the port PVID is used as the critical VLAN ID.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event server dead action \[ \{ reinitialize \| authorize \} \] \[ vlan vlan-id \] \] |
| Default | Action: None  |
| Default | VLAN: Port PVID  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.16 authentication event server dead action authorize voice
This command enables authorization of voice devices on the critical voice VLAN when all the authentication servers are dead. The configured voice VLAN of the port, on which the voice device is connected, is used as the critical voice VLAN ID.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event server dead action authorize voice |
| Default | Action: None  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.17 authentication event server alive action reinitialize
This command configures the actions to take when one authentication server comes back alive after all were dead. The reinitialize action triggers the re-authentication of supplicants authenticated on the critical VLAN. 

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication event server alive action reinitialize |
| Default | Action: None  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.18 authentication open
This command configures Open Authentication mode on the port.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication open |
| Default | Disabled  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.19 authentication order
This command is used to set the order of authentication methods used on a port. The allowed methods to configure for SONiC are Dot1x and MAB. Ordering sets the order of methods that the switch attempts when trying to authenticate a new device connected to a port. If one method in the list is unsuccessful or timed out, the next method is atempted. Each method can only be entered once.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication order \{ dot1x \[ mab \] \| mab \[ dot1x \] \} |
| Default | None  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.20 authentication priority
This command is used to set the priority for the authentication methods used on a port. The allowed methods to configure for SONiC are Dot1x and MAB. Authentication priority decides if the client, who is already authenticated, to re-authenticate with the  higher-priority method when the same is received.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | authentication priority \{ dot1x \[ mab \] \| mab \[ dot1x \] \} |
| Default | None  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.21 mab
This command is used to enable MAC Authentication Bypass (MAB) on an interface. MAB is a supplemental authentication mechanism that allows 802.1X unaware clients – such as printers, fax machines, and some IP phones — to authenticate to the network using the client MAC address as an identifier. However MAB can also be used to authenticate 802.1x aware clients. This command also provides options to specify the type of authentication to be used, which can be either EAP-MD5 ,PAP,CHAP. If enabled, EAP-MD5 is used by default.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | mab \[ auth-type \{ pap \| eap-md5 \} \| chap \] |
| Default | Disabled  |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.2.22 dot1x timeout
This command sets the value, in seconds, of the timers used by the authenticator or supplicant state machines on an interface or range of interfaces. Depending on the token used and the value (in seconds) passed, various timeout configurable parameters are set. 

- quiet-period: The value, in seconds, of the timer used by the authenticator state machine on this port to define periods of time in which it will not attempt to acquire a supplicant. This is the period for which the authenticator state machine stays in the HELD state.
- tx-period: The value, in seconds, of the timer used by the authenticator state machine on this port to determine when to send an EAPOL EAP Request/Identity frame to the supplicant. 
- server-timeout: The value, in seconds, of the timer used by the authenticator state machine on this port to timeout the authentication server. 
- supp-timeout: The value, in seconds, of the timer used by the authenticator state machine on this port to timeout the supplicant. 
- auth-period: The value, in seconds, of the timer used by the supplicant state machine on this port to timeout an authenticator when waiting for a response to packets other than EAPOL-Start.
- start-period: The value, in seconds, of the timer used by the supplicant state machine on this port to determine the interval between two successive EAPOL-Start frames when they are being retransmitted.
- held-period: The value, in seconds, of the timer used by the supplicant state machine on this port to determine the length of time it will wait before trying to send the authentication credentials again after a failed attempt. This is the period for which the supplicant state machine stays in the HELD state.

| Mode | Interface Config |
| ---- | ------ |
| Syntax | dot1x timeout \{ quiet-period \| tx-period \| server-timeout \| supp-timeout \| auth-period \| start-period \| held-period \} |
| Default | quiet-period: 60 seconds  |
| Default | tx-period: 30 seconds  |
| Default | supp-timeout: 30 seconds  |
| Default | server-timeout: 30 seconds  |
| Default | auth-period: 30 seconds  |
| Default | start-period: 30 seconds |
| Default | held-period: 60 seconds |
| Change history | SONiC 4.0 - Introduced |


	
### 3.6.3 Show Commands

#### 3.6.3.1 show authentication interface
This command displays the authentication manager information for the interface 

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show authentication interface  \{ all \| \{ interface \<unit\/slot\/port\> \} \} |
| Change history | SONiC 4.0 - Introduced |

| Field   | Description |
| ------ | ------------------- |
| Authentication Manager Status | The admin status of Authentication on the switch. This is a global configuration. |
| Interface  | The interface for which authentication configuration information is being displayed. |
| Port Control Mode | The configured control mode for this port. Possible values are force-unauthorized | auto | unauthorized. |
| Host Mode | The authentication host mode configured on the interface. |
| Authentication Restart timer | The time, in seconds, after which reauthentication starts. |
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
| Authentication Violation Mode | The action to be taken when a security violation occurs on a port. |
| Authentication Server Dead action | The action to be undertaken for data clients when all RADIUS servers are found dead. |
| Authentication Server Dead action for Voice | The action to be undertaken for voice clients when all RADIUS servers are found dead. |
| Authentication Server Alive action | The action to be undertaken for data clients when a RADIUS server comes back alive after all were found dead. |
| Allowed protocols on unauthorized port | The action to drop or forward the particular protocol packet from and to unauthorized clients on the port |
| Open Authentication | Indicates if Open Authentication is enabled on the interface. |

Example:
```
show  authentication interface 1/0/1

Authentication Manager Status.................. Enabled

Interface...................................... 1/0/1
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
Authentication Violation Mode.................. Restrict
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
| Syntax | show authentication clients  \{ all \| \{ interface \<unit\/slot\/port\> \} \} |
| Change history | SONiC 4.0 - Introduced |


| Field   | Description |
| ------ | ------------------- |
| Interface  | The interface for which authentication configuration information is being displayed. |
| Mac Address | The MAC address of the client. |
| User Name | The user name associated with the client. |
| VLAN Assigned Reason | This can take one of the following values |
| Host Mode | The authentication host mode configured on the interface. The possible values are multi-auth, multi-domain, multi-host, single-host and multi-domain-multi-host. |
| Method | The method used to authenticate the client on the interface. The possible values are 802.1x. MAB, Captive Portal and None. |
| Control Mode | The configured control mode for this port. Possible values are force-unauthorized, auto and unauthorized. |
| Session Time | The amount of time the client session has been active. |
| Session Timeout | This value indicates the time for which the given session is valid. The time period in seconds is returned by the RADIUS server on authentication of the port. |
| Time left for Session Termination Action | This value indicates the time left for the session termination action to occur. This field is valid only when the “authentication periodic” is configured. |
| Session Termination Action | This value indicates the action to be taken once the session timeout expires. Possible values are Default and Radius-Request. If the value is Default, the session is terminated and client details are cleared. If the value is Radius-Request, then a reauthentication of the client is performed. |
| Filter ID | Identifies the Filter ID returned by the RADIUS server when the client was authenticated. This is a configured DiffServ policy name on the switch. |
| ACS ACL Name | Identifies the Downloadable ACL returned by the RADIUS server when the client was authenticated. The Downloadable ACL is the same as returned using CiscoSecure-Defined-ACL AVP.|
| DACL | Identifies the Downloadable Dynamic ACL returned by the RADIUS server when the client was authenticated. |
| Redirect-ACL | The Redirect ACL is a static ACL sent in the RADIUS attribute redirect-acl. It is used to redirect matching packets to the CPU for further action. |
| Redirect URL | The Redirect URL is a URL sent in the RADIUS attribute redirect-url. It is used by the Redirect component logic to redirect matching packets the redirect URL by using HTTP 302 response code. |
| Acct Session Id | The Accounting Session Id associated with the client session. |


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


(dhcp-10-130-86-142) (Interface 0/10)#show  authentication  clients all

Interface  MAC-Address         Method   Host Mode    Control Mode  VLAN Assigned Reason
---------  -----------------   -------  ------------ ------------  --------------------------
0/16       10:8D:B6:C6:00:00   802.1X   multi-host   auto     RADIUS Assigned VLAN (10)

(dhcp-10-130-86-200) #show authentication clients interface 1/0/2

Mac Address.................................... 58:05:94:1C:00:00
User Name...................................... testixia
VLAN Assigned Reason........................... Voice VLAN (100)
Host Mode ..................................... multi-auth
Method......................................... 802.1X
Control Mode................................... auto
Session time ... .............................. 10
Session timeout ............................... 100
Time left for Session Termination Action....... 90
Session Termination Action..................... Default
Filter-Id ..................................... None
ACS ACL Name................................... xACSACLx-IP-FP_ACL-5ee227a2
DACL........................................... None
Redirect ACL................................... IP-REDIRECT-IN-00000001#d
Redirect URL................................... http://rtpjira.rtp.broadcom.com:8080
Session Termination Action..................... Default
Acct SessionId:................................ testixia:200000003

```





#### 3.6.3.4 show authentication authentication-history 
This command displays the authentication manager authentication history log for an interface

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show authentication authentication-history  \{ interface \<unit\/slot\/port\> \} |
| Change history | SONiC 4.0 - Introduced |

Example:
```
show authentication authentication-history 1/0/2 


Timestamp             Interface  MAC-Address        Auth Status   Method
--------------------  ---------  -----------------  ------------  ------
May 07 2020 13:02:41  1/0/2      58:05:94:1C:00:00  Unauthorized  802.1X
May 07 2020 13:01:33  1/0/2      58:05:94:1C:00:00  Unauthorized  802.1X

```

#### 3.6.3.5 show mab
This command is used to show a summary of the global mab configuration and summary information of the mab configuration for all ports. This command also provides the detailed mab sessions for a specified port. 

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show mab  \{ \<cr\> \| \{ interface \<unit\/slot\/port\> \} \}  |
| Change history | SONiC 4.0 - Introduced |

Example:
```
dhcp-10-130-86-142) #show  mab 

MAB Request Fmt Attr1 Groupsize... 2
MAB Request Fmt Attr1 Separator... legacy(:)
MAB Request Fmt Attr1 Case........ uppercase

Interface    Admin Mode     Auth-type    ---------    -----------    ---------    ---------------------   -------  -------------
0/1          Disabled       N/A          0/2          Disabled       N/A          
0/3          Disabled       N/A          


(dhcp-10-130-86-142) #show  mab interface 0/10
Interface    Admin Mode     Auth-type    
0/10         Enabled        eap-md5    

```

#### 3.6.3.6 show dot1x
This command is used to show a summary of the global dot1x configuration, summary information of the dot1x configuration for a specified port or all ports, the detailed dot1x configuration for a specified port and the dot1x statistics for a specified port - depending on the tokens used.

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | show dot1x  |
| Change history | SONiC 4.0 - Introduced |

| Field   | Description |
| ------ | ------------------- |
| Administrative Mode | Indicates whether 802.1x is enabled or disabled. |
| EAPOL Flood Mode  | Indicates whether the EAPOL flood support is enabled on the switch. |
| Dot1x Software Version | The version of Dot1x implementation running on the switch. |
	
Example:
```
#show dot1x

Administrative Mode............... Enabled
EAPOL Flood Mode.................. Disabled
Software Version.................. 1

```

### 3.6.4 Clear Commands

#### 3.6.4.1 clear authentication sessions
This command  clears information for all Auth Manager sessions. All the authenticated clients are re-intialized and forced to authenticate again.

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | clear authentication session |
| Change history | SONiC 4.0 - Introduced |

#### 3.6.4.2 clear dot1x statistics
This command resets the 802.1X statistics for the specified port or for all ports.

| Mode   | Exec |
| ------ | ------------------- |
| Syntax | clear dot1x statistics |
| Change history | SONiC 4.0 - Introduced |

# 4 Flow Diagrams

# 5 Error Handling


# 6 Serviceability and Debug
show commands will help to see if the PAC is active on a port.
All processing errors will be captured in syslog.
Debug command output will be captured as part of tech support for offline analysis.

# 7 Warm Boot Support
Configured actions and counters should continue to work across warm reboot.

# 8 Scalability

## 8.1 Software scalability
The following is the support scale for Port Access Control. The following numbers are recommended numbers and are not enforced by the software. Its expected that the user will not configure beyond the recommended scale.

| Configuration / Resource   | Scale |
| ------ | ------------------- |
| ports supported by dot1x | 300 |
| ports supported by MAB | 300 |
| Clients per port | 48 |
| DACLs supported | system-limit |


# 9 Limitation

- Authentication Manager does not make any required configuration for the respective methods to authenticate successfully. The administrator needs to ensure that the correct and appropriate configuration is present in the system. For example, if the authentication order method includes the 802.1x port authentication method, 802.1X should be enabled for the authentication to succeed. Authentication manager will not enable/disable and make the configurations related to 802.1X. Administrator should make the necessary configurations.

- In the default configuration, all traffic that is not EAP over LAN (EAPoL) traffic (including DHCP) is dropped until 802.1X  and MAB times out. Therefore, the value of the timeout can significantly affect the DHCP client on the end host. Longer 802.1X timeouts may prevent DHCP from functioning correctly after the  802.1X timeout expires.

- To prevent DHCP clients from timing out, SONiC recommends testing the DHCP clients in respective network to discover how long they take to time out and setting the  802.1X timers accordingly.

- After configuration Save and reload, if the session Id in the PDU, that is sent by the client doesn’t match with the session Id expected by the 802.1X, then the received PDU is ignored and SONiC will re-try for 802.1X authentication. If the PDU is processed and the authentication fails or times out, then only the authentication moves to the next method.

- Authentication Manager cannot be enabled on LAG interfaces. Enabling Authentication Manager on ports which are member of LAGs or including an Unauthorized port into a LAG will result in unpredictable results.


# 10 Upgrade / Downgrade considerations

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
authentication enable
aaa authentication dot1x default radius

interface 1/0/1
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

Feature shall be supported on below mentioned platforms.   
*AS4630*
- 4 Core CPU
- 8 GB RAM
- 16G Disc
- TD3-X3

## 13.1 Future Design Enhancements

