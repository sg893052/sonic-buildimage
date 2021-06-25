
# Routing and Next Hop Table Enhancement
#### Rev 0.1

# Table of Contents
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [1. Introduction](#1-introduction)
  * [2. Requirements Overview](#2-requirement-overview)
      * [2.1 Functional Requirements](#21-functional-requirements)
      * [2.2 Configuration and Management Requirements](#22-configuration-and-management-requirements)
      * [2.3 Scalability Requirements](#23-scalability-requirements)
      * [2.4 Warm Boot Requirements](#24-warm-boot-requirements)
      * [2.4 Restrictions](#25-restrictions)
  * [3. Design](#3-design)
      * [3.1 Overview](#31-overview)
      * [3.2 DB Changes](#32-db-changes)
          * [3.2.1 APP DB](#321-app-db)
      * [3.3 Switch State Service Design](#33-switch-state-service-design)
          * [3.3.1 Orchestration Agent](#331-orchestration-agent)
      * [3.4 Syncd](#34-syncd)
      * [3.5 SAI](#35-sai)
      * [3.6 CLI](#36-cli)
          * [3.6.1 Show Commands](#361-show-commands)
  * [4. Warm Boot Support](#4-warm-boot-support)
      * [4.1 Warm Upgrade](#31-warm-upgrade)
  * [5. Unit Test](#5-unit-test)


# Revision
| Rev  | Date | Author | Change Description |
| :--: | :--: | :----: | ------------------ |
| 0.1  |      |        | Initial version    |


# About this Manual
This document provides general information about an enhancement to the internal APP_DB routing table to split next hop information out into its own table.

Throughout this document a next hop group may refer to either a single next hop or a group of several next hops.

# 1 Introduction
Currently the route table within frr/zebra, APP_DB contains all next hop information for a route embedded in that route's entry in the structure/table.

At high scale (particularly when handling millions of routes all routed over multiple next hops) this is inefficient both in terms of performance and occupancy.  A given route entry needs to be built up including all its next hop information, which gets sent from zebra to APP_DB.  Orchagent then needs to receive, parse and handle all the information in that route entry every time.

A more efficient system would involve managing the next hop groups in use by the route table separately, and simply have the route table specify a reference to which next hop group to use.  Since at scale many routes will use the same next hop groups, this requires much smaller occupancy per route, and so more efficient building, transmission and parsing of per-route information.

# 2 Requirement Overview
## 2.1 Functional Requirements

- Improve efficiency of storage of routing information in FRR, APP_DB.
- Reduce time to send route into to APP_DB and reprogram routes when next hop information changes.
- The feature should be supported for L3 underlay and overlay routes.

## 2.2 Configuration and Management Requirements
- The existing 'show ip route' and 'show ipv6 route' commands must be supported when using the new next hop group table.

## 2.3 Scalability Requirements
- Unchanged in requirements, but will improve scalability in terms of storage and programming time.

## 2.4 Warm Boot Requirements
- The new next hop group table must be compatible with existing warm boot requirements.
- The co-relation between the application/zebra generated nexthop group id and hardware entry needs to be maintained across warm boot. 

## 2.5 Restrictions
- 

# 3 Design
## 3.1 Overview
FRR version 7.5 already supports the generation of nexthop id and using this id in the route nexthop information. This needs to be extended to FPM so that it can send the nexthop object to APP_DB seperately and then use it in the route info being sent to APP_DB.

Corresponding changes are required in APP_DB and orchagent to handle the nexthop group table and to associate the nexthop group reference in the route table. 

## 3.2 DB Changes
### 3.2.1 APP DB
Currently the ROUTE_TABLE in APP_DB includes all next hop information for the route, as follows:
```
### ROUTE_TABLE
    ;Stores a list of routes
    ;Status: Mandatory
    key           = ROUTE_TABLE:prefix
    nexthop       = *prefix, ;IP addresses separated “,” (empty indicates no gateway)
    ifname        = *PORT_TABLE.key,   ; zero or more separated by “,” (zero indicates no interface)
    blackhole     = BIT ; Set to 1 if this route is a blackhole (or null0)
```

This design adds a new NEXT_HOP_GROUP_TABLE, to store next hop group information to be used by one or more routes. For EVPN VXLAN based overlay routes, the ifname field will corresponds to the local IRB Vlan interface and the vni_label will be present to identify the VNI.
```
### NEXT_HOP_GROUP_TABLE
    ;Stores a list of groups of one or more next hops
    ;Status: Mandatory
    key           = NEXT_HOP_GROUP_TABLE:string ; arbitrary string identifying the next hop group, as determined by the programming application.
    nexthop       = *prefix,           ; IP addresses separated “,” (empty indicates no gateway)
    ifname        = *PORT_TABLE.key,   ; zero or more separated by “,” (zero indicates no interface)
    vni_label     = VRF.vni            ; New Field. zero or more separated by ',' (empty value for non-vxlan next-hops)
    nexthop_group = NEXT_HOP_GROUP_TABLE:key, ; index within the NEXT_HOP_GROUP_TABLE seperated by "," used for recursice/ecmp routes. ( When this field is present, other fields will not be present)
```
Note that the identifier for a next hop group is entirely the decision of the programming application. Whether this is done randomly or algorithmically is up to the application - this design imposes no requirements on this.
Recursive/ecmp routes are represented by referencing a list of nexthop group ids in the nexthop_group field. When this field is used, other fields will not be present.

The ROUTE_TABLE is then extended to allow a reference to the next hop group to be specified, instead of the current nexthop and intf fields.
```
### ROUTE_TABLE
    ;Stores a list of routes
    ;Status: Mandatory
    key           = ROUTE_TABLE:prefix
    nexthop       = *prefix, ;IP addresses separated “,” (empty indicates no gateway)
    ifname        = *PORT_TABLE.key,   ; zero or more separated by “,” (zero indicates no interface)
    blackhole     = BIT ; Set to 1 if this route is a blackhole (or null0)
    nexthop_group = NEXT_HOP_GROUP_TABLE:key ; index within the NEXT_HOP_GROUP_TABLE, optionally used instead of nexthop and intf fields
```

## **3.3 FRR**

### 3.4.1 Zebra FPM

With FRR 7.5 the zebra component already creates the nexthop group id and objects and refers it in the route info. The Zebra FPM component will be modified to send the nexthop group object separately via the netlink message and a reference of the nexthop group id in the route object.

### 3.4.2 fpmsyncd

fpmsyncd will be modified to receive the nexthop group object from Zebra FPM and populate the NEXT_HOP_GROUP_TABLE in the APP_DB.  The route processing in fpmsyncd will be modified to process the nexthop group id reference in the route info if it is present. fpmsyncd will get the zebra allocated nexthop group id and will allocate and map it to fpm allocated nexthop group id when writing to APP_DB. During warm-reboot this will help to reconcile the newly received zebra alocated nexthop group id and remap it to the older fpm allocated nexthop group id based on the groups content. This will ensure that the nexthop group id programmed to APP_DB is maintained accross warm-reboot and hence reprogramming to hardware is avoided.

## 3.4 Switch State Service Design

### 3.4.1 Orchestration Agent

A new orchestration agent will be written to handle the new NEXT_HOP_GROUP_TABLE in APP_DB. For a new or updated entry in the NEXT_HOP_GROUP_TABLE, programming will depend on whether the group is configured with 1 or multiple next hops.

 - If the group has a single next hop, the next hop group orchagent will simply get the SAI identifier for that next hop from the neighbor orchagent, and use that as the SAI identifier for the group.
 - If the group has multiple next hops, the next hop group orchagent will add a next hop group to ASIC_DB and then add a next hop group member to ASIC_DB for every member of that group that is available to be used. Changes to the membership of the group will result in next hop group members being added to or removed from ASIC_DB. The next hop group orchagent will then maintain an association between the identifer of the group from APP_DB and the SAI identifier assigned to the next hop group.

Dummy code for this logic:
```
if (num_next_hops == 1)
	sai_id = sai ID of the next hop
else
	create next hop group
	add next hop group member for each next hop
	sai_id = sai ID of the next hop group
```

The handling of whether a next hop group member can be programmed or not, interfaces going up and down, etc. will match the same handling done by next hop groups managed by the route orchagent.

When the next hop group is deleted, the next hop group orchagent will remove the next hop group from the ASIC_DB if required, and remove the association between the group identifier from APP_DB and the SAI identifier from ASIC_DB.  This will only happen once the next hop group is no longer referenced by any routes - and so the next hop group will maintain a reference count within orchagent to ensure this. If orchagent restarts before all referencing routes are updated/deleted, the remaining routes will simply be unable to be programmed into ASIC_DB after restart. This will just be a transient state until the routes are updated in the ROUTE_TABLE.

The route orchagent will parse the new nexthop_group field, and call into the next hop group orchagent to find the corresponding SAI object ID to program as the next hop attribute.  If the next hop group does not exist, the route will remain on the list of routes to sync, and will be retried on future runs of the route orchagent.

If a next hop group cannot be programmed because the data plane limit has been reached, one next hop will be picked to be temporarily used for that group.  When a route is then programmed using that next hop group, it will be informed that a temporary form of the next hop group is in use, and so the route will remain on the pending list and will retry until the correct next hop group is programmed.  This mirrors the current behaviour in the route orchagent when the next hop group limit is reached. The APP_DB entries remain unchanged.

A few notes about this design:

- The next hop groups created by the next hop group orchagent will not interact with those created by the route orchagent's current mechanisms. This is because the route orchagent's groups are identified by the members of the group, and so changing those members makes it a different group, whereas the new next hop group orchagent's groups have an arbitrary index, with members able to change without it becoming a different group. Therefore even if a group in each category share the same members, they will be programmed separately into ASIC_DB, in case the members change. The expectation is that all routes will be programmed into APP_DB using the old next hop schema or the new one, and so the only occasion where next hop groups could overlap is if a next hop group is created for an ACL that overlaps with one created by the next hop group orchagent.
- A ROUTE_TABLE entry with both next hop information embedded and a reference to the NEXT_HOP_GROUP_TABLE will be ignored.
- Next hop groups created by the fine grained next hop group orchagent will be unchanged by this design.

## 3.5 SyncD
No change.


## 3.6 SAI
No changes are being made in SAI. The end result of what gets programmed over the SAI will be the same as currently gets programmed - the difference is the programming sequence to get there.

## 3.7 CLI

### 3.7.1 Show Commands

The output of 'show ip route' and 'show ipv6 route' will remain unchanged - the CLI code will resolve the next hop group ID referenced in the ROUTE_TABLE to display the next hops for the routes.

# 4 Warm Boot Support

Warm boot support must not be affected by this enhancement. Specifically, this means that (assuming the required routes don't change during warm boot), routes must be associated with the same next hop group after warm boot. The application programming APP_DB must therefore ensure that next hop groups get assigned the same identifier after reboot. To achieve this fpmsyncd will allocate a nexthop group id and map it with the zebra allocated nexthop group id. The fpm allocted id is programmed to the APP_DB. On warm-reboot the APP-DB is restored with the fpm allocated nexthop group id. Zebra will allocate new nexthop group id and pass it to fpnsyncd. fpmsyncd will lookup based on the nexthop group contents and will map the new zebra allocated nexthop id with the restored fpm allocated nexthop group id. This will make sure the nexthop group id in the APP_DB is not modified and hence the hardware is not reprogrammed.

Fast reboot is not affected by this change. The requirements on the BGP application in terms of graceful restart remain the same - the only affect this change has is to give an alternative approach for what the BGP application programs into APP_DB.

# 4.1 Warm Upgrade

Existing applications will not be affected by this enhancement, so no warm upgrade support is needed. If in future an existing application is changed to use this feature, then a further enhancement should be made to handle warm upgrade. Adding warm upgrade support now would be untestable, due to no application using that code, and so is outside the scope of this design.

# 5 Unit Test
## 5.1 Underlay routes TC:
- Verify route programming with single path route add/del/updates
- Verify route programming with multipath routes add/del/Path-add/Path-delete
- Verify route programming with routes from multiple source  BGP/static/connected add/remove/update source
- Verify route programming for recursive routes
- Verify route programming for blackhole/Null routes
- Vefiry route programming for routes in user Vrfs

## 5.2 Overlay routes TC:
- Verify route programming for EVPN VXLAN Overlay routes 
- Vefiry route programming for various triggers advertise/withdraw/updates for overlay routes

## 5.3 Scale TC:
- Vefiry route programming for max scale routes
- Verify route programming for max ecmp paths
- Vefiry route programming for more than max ecmp paths

## 5.4 warm-reboot:
- Verify route programming after system warm-reboot
- Vefiry route programming after docker warm-reboot
