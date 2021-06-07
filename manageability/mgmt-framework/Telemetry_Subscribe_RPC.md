# Subscription RPC enhancements in Telemetry Service

# High Level Design Document

# Table of Contents

- [1 Feature Overview](#1-Feature-Overview)
  - [1.1 Target Deployment Use Cases](#11-Target-Deployment-Use-Cases)
  - [1.2 Requirements](#12-Requirements)
    - [1.2.1 Wildcard paths in Subscribe RPC](#121-wildcard-paths-in-subscribe-rpc)
    - [1.2.2 ON_CHANGE subscription](#122-on_change-subscription)
    - [1.2.3 SAMPLE subscription](#123-sample-subscription)
    - [1.2.4 TARGET_DEFINED subscription](#124-target_defined-subscription)
    - [1.2.5 Scalar encoding for telemetry updates](#125-scalar-encoding-for-telemetry-updates)
    - [1.2.6 Translib Subscription Infrastructure for DB data](#126-translib-subscription-infrastructure-for-db-data)
  - [1.3 Design Overview](#13-Design-Overview)
    - [1.3.1 Basic Approach](#131-Basic-Approach)
    - [1.3.2 Container](#132-Container)
    - [1.3.3 SAI Overview](#133-SAI-Overview)
- [2 Functionality](#2-Functionality)
  - [2.1 Subscribe API](#21-subscribe-api)
  - [2.2 Wildcard Paths](#22-wildcard-paths)
- [3 Design](#3-Design)
  - [3.1 Framework](#31-framework)
    - [3.1.1 Handling wildcard keys in the path](#311-handling-wildcard-keys-in-the-path)
    - [3.1.2 DB key to Path mapping](#312-db-key-to-path-mapping)
    - [3.1.3 Subscribe Session](#313-subscribe-session)
    - [3.1.4 Sending Subscribe Response Data](#314-sending-subscribe-response-data)
    - [3.1.5 ON_CHANGE Subscription for DB data](#315-on_change-subscription-for-db-data)
    - [3.1.6 ON_CHANGE Subscription for non-DB data](#316-on_change-subscription-for-non-db-data)
    - [3.1.7 SAMPLE Subscription for DB data](#317-sample-subscription-for-db-data)
    - [3.1.8 SAMPLE Subscription for non-DB data](#318-sample-subscription-for-non-db-data)
    - [3.1.9 TARGET_DEFINED Subscription](#319-target_defined-subscription)
    - [3.1.10 ONCE Subscription](#3110-once-subscription)
    - [3.1.11 POLL Subscription](#3111-poll-subscription)
    - [3.1.12 Wildcard for non-DB Paths](#3112-wildcard-for-non-db-paths)
  - [3.2 DB Changes](#32-DB-Changes)
  - [3.3 Switch State Service Design](#33-Switch-State-Service-Design)
  - [3.4 SyncD](#34-SyncD)
  - [3.5 SAI](#35-SAI)
  - [3.6 User Interface](#36-User-Interface)
    - [3.6.1 Data Models](#361-Data-Models)
    - [3.6.2 CLI](#362-CLI)
    - [3.6.3 REST API Support](#363-REST-API-Support)
    - [3.6.4 gNMI Support](#364-gNMI-Support)
  - [3.7 Warm Boot Support](#37-Warm-Boot-Support)
  - [3.8 Upgrade and Downgrade Considerations](#38-Upgrade-and-Downgrade-Considerations)
  - [3.9 Resource Needs](#39-Resource-Needs)
- [4 Flow Diagrams](#4-Flow-Diagrams)
- [5 Error Handling](#5-Error-Handling)
- [6 Serviceability and Debug](#6-Serviceability-and-Debug)
- [7 Scalability](#7-Scalability)
- [8 Platform](#8-Platform)
- [9 Limitations](#9-Limitations)
- [10 Unit Test](#10-Unit-Test)
  - [10.1 Test Cases](#101-test-cases)
  - [10.2 gNMI UT Framework for spytest](#102-gnmi-ut-framework-for-spytest)
  - [10.3 UT with gnmi_cli tool](#103-ut-with-gnmi_cli-tool)
  - [10.4 Supported YANG Paths](#104-supported-yang-paths)
- [11 Internal Design Information](#11-Internal-Design-Information)
  - [11.1 IS-CLI Compliance](#111-IS-CLI-Compliance)
  - [11.2 Broadcom Packaging](#112-Broadcom-SONiC-Packaging)
  - [11.3 Broadcom Silicon Considerations](#113-Broadcom-Silicon-Considerations)
  - [11.4 Design Alternatives](#114-Design-Alternatives)
  - [11.5 Broadcom Release Matrix](#115-Broadcom-Release-Matrix)

# List of Tables

[Table 1: Abbreviations](#table-1-Abbreviations)

[Broadcom Release Matrix](#115-broadcom-release-matrix)

# Revision

| Rev |     Date    |       Author       | Change Description                                     |
|-----|-------------|--------------------|--------------------------------------------------------|
| 0.1 | 12/23/2020  | Sachin Holla       | Initial version                                        |
| 0.2 | 04/23/2021  | Sachin Holla       | Updated requirements for Cyrus release                 |
| 0.3 | 05/19/2021  | Sachin Holla       | Move yang path listing to a separate document          |
| 0.4 | 05/24/2021  | Sachin Holla       | Include detailed design and fixes for few comments     |
| 0.5 | 06/07/2021  | Sachin Holla       | Mention app's role in wildcard path support            |

# About This Manual

This document describes the high level design for Telemetry service and Translib infrastructure
to support gNMI ON_CHANGE subscriptions and wildcard paths in SONiC Telemetry Service.
Community version of telemetry service provides subscriptions using REDIS database based paths
as explained [here](https://github.com/Azure/sonic-telemetry/blob/master/doc/grpc_telemetry.md).
This functionality will be retained as is, without any modifications.
All the enhancements discussed here are applicable to only YANG data model based paths.
These paths are supported using the **Translib** library of the
[SONiC Management Framework](https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Management%20Framework.md).

Scope of this document is limited to the infrastructure components only.
HLD for individual features will be documented separately by respective feature teams.

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term**       | **Meaning**                         |
|----------------|-------------------------------------|
| gNMI           | gRPC Network Management Interface   |
| gRPC           | gRPC Remote Procedure Call (it is a recursive acronym!!)   |
| YGOT           | [YANG Go Tools](https://github.com/openconfig/ygot); a Golang library to process YANG data |

# References

SONiC Management Framework overview<br>
https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Management%20Framework.md

gNMI Specification<br>
https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md

gNMI Protobuf Definition<br>
https://github.com/openconfig/gnmi/blob/master/proto/gnmi/gnmi.proto

# 1 Feature Overview

## 1.1 Target Deployment Use Cases

 *FILLME*

## 1.2 Requirements

### 1.2.1 Wildcard paths in Subscribe RPC

gNMI Server should support wild cards in **path key** for all subscription modes.
Example: `/openconfig-interfaces:interfaces/interface[name=*]/config`.
Path can contain any number of wildcard key values.

Following cases will not be supported:

- Wildcard in path element (like `/openconfig-interfaces:interfaces/*/config` or `/openconfig-interfaces:interfaces/.../config`).
- Wildcard paths pointing to non-DB data sources, like FRR socket.
- WIldcard paths in **Get** RPC.

Server should reject the RPC with INVALID_ARGUMENT status code if the request contains
unsupported wildcard paths.

**Note:** Wildcard key handling requires changes in application code.
Not all applications are supporting it in this release.
Supported applications and paths are listed in [Telemetry_Subscribe_Paths.md](https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_Paths.md).

### 1.2.2 ON_CHANGE subscription

gNMI server should support ON_CHANGE subscription for all YANG paths except following:

- Paths mapped to DB tables that can be updated frequently.
  Most of the COUNTERS_DB tables will fall in this category.
  Actual list of such paths will be decided & published by individual features.
- Paths mapped to non-DB data sources, like FRR socket.
- Any path whose descendent node does not support ON_CHANGE.

Server should reject the Subscribe request with INVALID_ARGUMENT status code if it receives
ON_CHANGE subscription request for unsupported paths.

**Note:** Not all applications are supporting ON_CHANGE subscription in this release.
Supported paths are listed in [Telemetry_Subscribe_Paths.md](https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_Paths.md).

### 1.2.3 SAMPLE subscription

Server should support SAMPLE subscription for every YANG path.
Minimum subscribe interval will be 20 seconds.
Individual features can override this to have larger minimum sample interval
for specific YANG paths depending on the functionality.

Server should send periodic updates for every set leaf in the subscribed path.
If the `suppress_redundant` flag is set in the subscribe request, server should
generate updates only for the changed leaf paths (since last notification message).
Special case - notification message should not be sent if there are no changes.

Server should include delete paths for the YANG nodes deleted during
the sample interval in the notification message.
Existing implementation was not notifying deleted paths.

**Note:** SAMPLE subscription for wildcard paths require application code changes.
Wildcard supported yang modules are listed in [Telemetry_Subscribe_Paths.md](https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_Paths.md).
SAMPLE subscription will be supported for all non-wildcard paths.

### 1.2.4 TARGET_DEFINED subscription

For TARGET_DEFINED subscription requests, the server should determine best type
of subscription mode based on the preference of the subscribed path and its
descendent nodes.
Server should split the subscription request into multiple requests if subscribed path
prefers ON_CHANGE but some of its descendent paths prefer SAMPLE subscription.
Following table summarizes the server behavior.

| Subscribe path<br>preference | Descendent path<br>preference | Result       |
|------------------------------|-------------------------------|--------------|
| ON_CHANGE | All ON_CHANGE  | ON_CHANGE                  |
| ON_CHANGE | Few SAMPLE     | SAMPLE for those "few" descendent paths;<br>ON_CHANGE for rest |
| SAMPLE    | Don't care     | SAMPLE                     |

By default, all paths that support ON_CHANGE are assumed to prefer ON_CHANGE.
Individual apps can override it to prefer SAMPLE for individual paths (i.e, supports ON_CHANGE but prefers SAMPLE).

Paths that do not support ON_CHANGE will always have preferred mode as SAMPLE.

### 1.2.5 Scalar encoding for telemetry updates

All telemetry updates should be encoded as gNMI scalar types.
Each update entry should be a {leaf path, scalar value} pair.
Scalar type encoding is explained in gNMI specification [section 2.2.3](https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md#223-node-values).

Existing implementation coalesces all leaf values into a big JSON blob.
gNMI allows this only if the client requests it through `allow_aggregation` flag
and the YANG node is marked as eligible for aggregation.
Today there is no such option available in Translib schema.
Hence telemetry update message should not aggregate the leaf values.
Also, gNMI does not recommend aggregation as it helps both server and client implementation.

### 1.2.6 Translib Subscription Infrastructure for DB data

Translib should provide an infrastructure to handle gNMI subscription requests.
Translib (and gNMI server) should handle all protocol requirements and DB data monitoring without apps getting involved.
Apps should be only required to provide yang path to DB mappings and subscription preferences
(whether ON_CHANGE supported, minimum SAMPLE interval, preferred mode for TARGET_DEFINED subscription).

## 1.3 Design Overview

### 1.3.1 Basic Approach

Roles of framework components and apps:

- gNMI server will manage subscribe RPC life cycle, timers, validations, response encoding.
- Translib will provide APIs to monitor and retrieve subscribed YANG nodes.
- App modules are only required to provide YANG path to DB entry mappings and vice-versa.
- App modules should implement app interface function `translateSubscribe` to provide YANG path to DB/non-DB mappings.
  Should handle wildcard keys in the yang paths.
  Mapped DB key can have a redis wildcard pattern.
  One YANG path can have multiple DB/non-DB mappings.
- App modules can also return subscription preferences in `translateSubscribe` response.
  Translib will assume default values if app did not specify the preferences.
  Preferences includes:
  - Whether ON_CHANGE  enabled
  - Preferred subscription mode during TARGET_DEFINED
  - Minimum SAMPLE interval.
- Transformer common_app will discover the mappings and preferences from the annotations.
  Subtree transformers should implement `Subscribe_xxx` callback function to specify the mappings and preferences.
  Table and key transformers should handle wildcard paths.
- App modules should implement `processSubscribe` app interface function to resolve YANG keys for a DB entry.
  This is required to get a specific YANG path from a wildcard path template.
- Transformer common_app will automatically support this for simple cases.
  Apps should implement a *path transformer* to handle special cases -- subtree transformer or when there is no one-to-one mapping of YANG key to DB key.

Translib APIs:

- `translib.IsSubscribeSupported` API returns subscription preferences for given paths.
gNMI server uses this information to handle TARGET_DEFINED subscription and to validate ON_CHANGE, SAMPLE request parameters.
- `translib.Stream` API will stream the current data for given paths to gNMI server using a queue.
  This API is used to service SAMPLE, ONCE and POLL subscriptions.
- `translib.Subscribe` API handles ON_CHANGE subscriptions.
  It starts monitoring the mapped DB key patterns using redis PSUBSCRIBE operations.
  Subsequent changes to the DB will be stream asynchronously using a queue.

ON_CHANGE handling:

- Redis notifies translib when any of the monitored DB entries are updated/deleted.
  This keyspace notification only indicates the DB key of affected entry;
  does not indicate which fields are modified.
- Translib may be interested in in only few fields depending on the subscribed YANG paths.
- Translib maintains a cache of monitored DB entries to workaround redis limitations.
- When redis keyspace notification is received, translib reads latest entry value from DB for that key and compares with the cache to identify modified fields.
  Cache is also updated with the new value in this process.
- Translib will ignore the redis notification if none of the subscribed fields are changed.
  This helps to suppress noises close to the source.
- If subscribed path had wildcard keys, translib resolves the specific path for the updated DB entry by calling app module's `processSubscribe` function.
- If subscribed fields were updated, translib constructs individual YANG leaf paths and retrives the values using app module's `translateGet` and `processGet` functions.
  Translib streams the {path, YGOT object} data to gNMI server through the queue.
- If subscribed fields or DB entry was deleted, translib constructs corresponding YANG paths and
  streams these delete paths to gNMI server through the queue.

### 1.3.2 Container

All the changes will be local to existing **telemetry** container.

### 1.3.3 SAI Overview

N/A

# 2 Functionality

## 2.1 Subscribe API

The [gNMI specification](https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md)
defines a **Subscribe** RPC, which allows clients to receive relating to the state of specific data instances.
A subscription consists of one or more paths, with a specified subscription mode.
The mode of each subscription determines the triggers for updates for data sent from the server.
Different modes of subscription available:

- **ONCE** -- server streams the current data to the client and closes the subscription.
    This is equivalent of **Get** RPC, but is more efficient since server need not coalesce
    individual data items into a single response.

- **POLL** -- server streams the data in response to a poll request from the client.
    The client would specify the paths it is interested in through the **Subscribe** request.
    It would later send a **Poll** message to trigger responses from the server.
    Poll subscription are used as an efficient alternative to **Get** RPC.

- **SAMPLE** -- server samples the data values periodically and streams the data to the client.
    Client would specify the paths and sample interval through the **Subscribe** request.
    Server would reject the request if it cannot support the requested sample interval.

- **ON_CHANGE** -- server sends data updates to the client when the value of subscribed path is changed.
    This mode is not suitable for frequently changing values like counters.
    Server can reject the ON_CHANGE subscribe request if it cannot support ON_CHANGE updates
    to the requested paths.
    Once accepted, server first sends current data for the subscribed paths to client (initial sync).
    Thereafter server sends only the delta updates to client when the values are changed on the server.

- **TARGET_DEFINED** -- server can pick either SAMPLE or ON_CHANGE modes for the subscribe path
    depending on its settings or preferences.

## 2.2 Wildcard Paths

Wildcards in the gNMI paths indicate all elements at a given subtree in the schema.
Wildcards can appear in path element name or key value.
Asterisk (*) is the single level wildcard and ellipses (...) is the multi-level wildcard character.

To select configured MTUs of all interfaces:<br>
`/openconfig-interfaces:interfaces/interface[name=*]/config/mtu`

To select both config/mtu and state/mtu of Ethernet0:<br>
`/openconfig-interfaces:interfaces/interface[name=Ethernet0]/*/mtu`

To select all state containers in oc-interface yang tree:<br>
`/openconfig-interfaces:interfaces/.../state`

To select all leafs and containers of Ethernet0:<br>
`/openconfig-interfaces:interfaces/interface[name=Ethernet0]/...`

Wildcard paths are allowed in Get and Subscribe requests only.
The response message should not include wildcards.
It should include the expanded paths.

**Note:** Paths with wildcard element names (last 3 examples) are not supported in this release.

# 3 Design

## 3.1 Framework

### 3.1.1 Handling wildcard keys in the path

Every YANG node that represnets DB data can be mapped a set of (DB index, table name, key components, fields) tuples.
Redis key pattern also supports glob like wildcards.
When the path has wildcard keys, the mapped redis key components will have wildcard patterns.
Translib can use these wildcard key patterns in redis KEYS, SCAN and PSUBSCRIBE calls.

Few examples with a simplified openconfig-interfaces YANG model:

```
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface*       ==> CONFIG_DB:PORT
        +--rw name
        +--rw config
        |  +--rw enabled
        +--ro state         ==> APPL_DB:PORT_TABLE
        |  +--ro enabled
        |  +--ro oper-status
```

| **YANG Path**                         | **DB mappings**                         |
|---------------------------------------|-----------------------------------------|
| /interfaces/interface[name=\*]         | {CONFIG_DB, PORT, \*, {admin_status=config/enabled}}<br> {APPL_DB, PORT_TABLE, \*, {admin_status=state/enabled, oper_status=state/oper-status}} |
| /interfaces/interface[name=\*]/config  | {CONFIG_DB, PORT, \*, {admin_status=enabled}} |
| /interfaces/interface[name=\*]/state/oper-status  | {APPL_DB, PORT_TABLE, \*, {oper_status=oper-status}} |
| /interfaces/interface[name=Ethernet0]/config      | {CONFIG_DB, PORT, Ethernet0, {admin_status=enabled}} |

There is also a possibility of leaf path getting mapped to multiple DB tables.
E.g, real life `/interfaces/interface[name=*]` will map to (CONFIG_DB, PORT, \*, {...}), (CONFIG_DB, PORTCHANNEL, \*, {...}) and so on.

YANG path to DB mapping should be provided by each app module through `translateSubscribe` response.
It should return array of `notificationAppInfo` objects containing following data:

- Path to mapped YANG node.
  Should be same as request path or one of its descendent paths.
  Can include wildcards.
- Mapped DB index, table name, key pattern components
- DB field name to YANG leaf subpath mappings
- Whether ON_CHANGE supported
- Preferred subscription mode for TARGET_DEFINED
- Minimum SAMPLE interval

Mapping should be returned even if the YANG node does not map to a DB entry.
The DB index should set to *unknown db* to indicate the non-DB mapping.

Transformer common_app can automatically derive these mappings from the annotations.
But key transformers, table transformers should handle wildcard keys in the input path.
Subtree transformers should implement `Subscribe_xxx` callback function to return the mapping data.

### 3.1.2 DB key to Path mapping

Notification messages should not include paths with specific keys.
Hence translib requires reverse mapping of DB key to YANG keys while processing DB entries.

Example: In the simplified openconfig-interfaces YANG example from previous section,
if the subscribed path is "/interfaces/interface[name=\*]/config" and the DB key
is "PORT|Ethernet0", the `processSubscribe` should return the expanded path as
"/interfaces/interface[name=**Ethernet0**]/config".

App modules should implement `processSubscribe` funcation to resolve specific path from the wild card path.
Arguments for `processSubscribe` includes:

- Path template - contains wildcard keys.
- DB key info - DB index, table name, key components
- DB entry value

It should return a copy of path template with all its wildcard keys resolved.
App module can query other DB entries if needed.

Transformer common_app will automatically handle these mappings from the annotations for simple cases.
Manual translation should be provided through a **path transformer** for subtree transformers and
when there is no one-to-one mapping of DB key components to YANG keys in the path.

### 3.1.3 Subscribe Session

ON_CHANGE and SAMPLE are long running subscriptions; they will be active till the gNMI client closes the RPC.
Translib APIs will need path to DB mappings many times at various stages.
This metadata should be cached to avoid invoking app module `translateSubscribe` multiple times in a session.
A session object will be introduced to maintain this cache and any other session scoped cache.

gNMI server can create a session using `translib.NewSubscribeSession` API.
It must close the session using `translib.CloseSubscribeSession` API before closing the RPC.

Session object will be an optional parameter for `translib.IsSubscribeSupported`, `translib.Subscribe` and `translib.Stream` APIs.
When session is provided, these APIs will try to resolve the metadata from the session cache.
If the metadata is not present already, they will invoke app module `translateSubscribe` to resolve the mappings and save it in the session cache for later use.
If session is not provided, `translateSubscribe` will be called every time.

### 3.1.4 Sending Subscribe Response Data

Subscribe RPC expects server to stream the data as {leaf path, value} pairs.
Unlike Get RPC, the server should not aggregate the objects in the notification message.
To achieve this gNMI server will share a **queue** with translib APIs to receive the data in an asynchronously.
Translib loads smaller YGOT objects and writes {path, YGOT struct} data to the queue.
gNMI server reads the queue in a separate goroutine, formats the YGOT structure into notification message and sends it back to the client.

### 3.1.5 ON_CHANGE Subscription for DB data

ON_CHANGE notifications are realized by monitoring changes to the DB through redis keyspace notifications.
Following sequence diagram indicates overall flow of ON_CHANGE processing.

![ON_CHANGE High Level Design](images/Subscribe_ONCHANGE.png)

gNMI server handles all protocol specific encoding and decoding of subscribe RPC.
RPC Will be closed with INVALID_ARGUMENTS error code if ON_CHANGE cannot be supported for any of the requested paths.
gNMI server uses `translib.IsSubscribeSupported` API to check whether ON_CHANGE subscription can be supported for paths.
It calls **translib.Subscribe** API to register for notifications and receive
asynchronous responses upon data change.
gNMI server maintains queues and locks to receive asynchronous responses from Translib.

The **translib.Subscribe** API manages redis subscriptions and notifications for the YANG path.
It uses App Module's `translateSubscribe` and `processSubscribe` functions to map YANG paths
to redis key and vice versa.
Translib subscribes for redis keyspace notifications (through PSUBSCRIBE operation)
for all DB keys returned by `translateSubscribe`.

gNMI Subscribe PRC requires server to stream current data values before it could
send incremental updates.
Translib performs redis SCAN operation of the key pattern returned by `translateSubscribe`
to iterate over existing DB keys.
For each DB key, App Module's `processSubscribe` function will be called to resolve the
wildcard keys in the path.
Get operation is performed for the expanded path and resulting YANG data is enqueued onto
the gNMI server's response queue.
This approach tends to result in Translib sending stream of smaller chunks of data.

Once the initial data sync is complete, the `translib.Subscribe` API ends.
Subsequent incremental updates are sent asynchronously.

Redis would invoke Translib's notification handler callback function when a DB entry
for the subscribed key pattern is modified.
Redis notifications do not include any information about which fields of the DB entry are modified.
To identify the modified fields, Translib maintains a cache of DB entries it is monitoring.
Notification handler would read the current DB entry and compare with the cached DB entry to
identify the updated and deleted DB entry fields.
Cache will also be updated in this process.
Translib will identify the modified YANG leaf paths using DB field to YANG leaf mapping
returned by `translateSubscribe`.
Redis notification is ignored if none of the monitored DB fields are changed.
This allows to cancel such noises close to the source and save precious CPU cycles.

If the subscribe path had wildcard keys, the wildcards need to be resolved in the modified YANG paths.
Translib uses App Module's `processSubscribe` function to achieve this.
The Translib obtains the modified YANG value through Get operation on each of the
modified YANG paths and enqueue them onto gNMI server's response queue.

The DB entry cache will be maintained in Translib's DB Access Layer.
This allows to automatically fill the initial values during initial data sync.
Also the App Modules can reuse cache data during subsequent Get calls as well.

Subscribe session is closed and all associated caches discarded when the gNMI client closes the RPC.
Translib will also unsubscribe from redis keyspace notifications.
These flows is not shown in the sequence diagram.

### 3.1.6 ON_CHANGE Subscription for non-DB data

There is no equivalent of redis keyspace notifications for non-DB data.
Hence ON_CHANGE will not be supported for such paths.
`translib.Subscribe` will return error if `translateSubscribe` returned any non-DB path mapping.

### 3.1.7 SAMPLE Subscription for DB data

SAMPLE subscription will be handled similar to ON_CHANGE's initial data sync.
But there will not be any redis subscriptions and DB cache.
New `translib.Stream` API will be introduced for SAMPLE handling.
Following sequence diagram describes the overall flow.

![SAMPLE High Level Design](images/Subscribe_SAMPLE.png)

gNMI server maintains the sample timer, response queue and locks.
It invokes the `translib.Stream` for every timer tick of every SAMPLE request path.

The `translib.Stream` will returns current data snapshot at every iteration.
To detect deleted resources, gNMI server will maintain previous iteration's snapshot in a {path, YGOT object} cache.
Current iteration's data is compared against this cache to identify deleted objects or attributes.
YGOT API `ygot.Diff` can be used to compare two YGOT objects.
Previous iteration's snapshot cache is replaced with current snapshot at the end of the iteration.

gNMI client can set `suppress_redundant` flag in the request to avoid receiving duplicate updates at every sample interval.
Server should not send an update for a leaf unless its value is modified since last update.
YGOT snapshot cache will also be used to resolve updated attributes.

YGOT based diff can be heavier and slower compared to DB entry based cache, like the one used for ON_CHANGE.
However such cache cannot be used for non-DB data.
SAMPLE subscription must be supported for every YANG path, irrespective of whether it maps to a DB entry or not.
YGOT snapshot cache approach is chosen to keep the implementation simple.
A mixed approach (DB entry cache in translib for DB data and YGOT cache in gNMI server for non-DB data) can be considered in future releases if we run into performance issues with YGOT cache.

### 3.1.8 SAMPLE Subscription for non-DB data

SAMPLE subscription will be supported for non-DB data as well.
If `translateSubscribe` returns non-DB path mapping, the `translib.Stream` API will use Get operation to retrieve whole data and send it back on on shot.
This can affect performance since Get operation ends up aggregating large number of child objects.

Also, Get will not support wildcard paths due to YGOT limitations.
Hence `translib.Stream` will return error for non-DB path with wildcard keys.

### 3.1.9 TARGET_DEFINED Subscription

TARGET_DEFINED subscription flow is similar to that of ON_CHANGE and SAMPLE.
Preferred subscription mode for the path is discovered from the metadata returned by `translib.IsSubscribeSupported` API.
ON_CHANGE or SAMPLE subscriptions are created based on these preferences.

- If the subscribe path and its descendent nodes prefer ON_CHANGE, then the request is treated as an ON_CHANGE subscription request.
- If the subscribe path prefers SAMPLE, then the request is treated as a SAMPLE subscription request.
- If the subscribe path prefers ON_CHANGE but few of its descendent nodes prefer SAMPLE,
  then the request is treated as multiple requests internally.
  Server starts SAMPLE subscription for the descendent nodes that prefer SAMPLE.
  An ON_CHANGE subscription will be created for rest of the nodes.

Few examples with a simplified openconfig-interfaces YANG model:

```text
module: openconfig-interfaces
  +--rw interfaces
     +--rw interface*
        +--rw name
        +--rw config        ==> CONFIG_DB:PORT
        |  +--rw enabled
        +--ro state         ==> APPL_DB:PORT_TABLE
        |  +--ro enabled
        |  +--ro oper-status
        |  +--ro counters   ==> COUNTERS_DB:COUNTERS (on_change not supported)
        |  |  +--ro in-octets
```

Here container /interfaces/interface/state/counters maps to COUNTERS_DB and does not support ON_CHANGE.
Other nodes support ON_CHANGE.
Below table lists the gNMI server's behavior for different combinations of subscription modes and paths.

| **Mode**       | **Subscribe Path**                       | **Result** |
|----------------|------------------------------------------|------------|
| TARGET_DEFINED | /interfaces/interface[name=\*]           | SAMPLE for /interfaces/interface[name=\*]/state/counters;<br>ON_CHANGE for other paths |
| TARGET_DEFINED | /interfaces/interface[name=\*]/config    | ON_CHANGE |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state     | SAMPLE for /interfaces/interface[name=\*]/state/counters;<br>ON_CHANGE for other paths |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state/enabled         | ON_CHANGE  |
| TARGET_DEFINED | /interfaces/interface[name=\*]/state/counters        | SAMPLE     |
| ON_CHANGE      | /interfaces/interface[name=\*]           | error (counters does not support ON_CHANGE) |
| ON_CHANGE      | /interfaces/interface[name=\*]/config    | ON_CHANGE |
| ON_CHANGE      | /interfaces/interface[name=\*]/state     | error (counters does not support ON_CHANGE) |
| ON_CHANGE      | /interfaces/interface[name=\*]/state/enabled         | ON_CHANGE  |
| SAMPLE         | /interfaces/interface[name=\*]           | SAMPLE    |
| SAMPLE         | /interfaces/interface[name=\*]/config    | SAMPLE    |
| SAMPLE         | /interfaces/interface[name=\*]/state     | SAMPLE    |

Following sequence diagram describes the overall flow.

![TARGET_DEFINED Subscription Design](images/Subscribe_STREAM.png)

Detailed flow of `translib.Subscribe` and `translib.Stream` APIs are not shown for the sake simplicity.
Please refer to previous sections for details.

### 3.1.10 ONCE Subscription

ONCE subscription also uses `translib.Stream` API, but does not create a session cache.
RPC is closed after sending telemetry updates for current data.
Following sequence diagram describes the overall flow.

![ONCE Subscription Design](images/Subscribe_ONCE.png)

### 3.1.11 POLL Subscription

POLL subscription is handled similar to ONCE subscription.
Every poll message will use `translib.Stream` API to notify current data similar to ONCE.
RPC is not closed after processing the poll message.

### 3.1.12 Wildcard for non-DB Paths

SAMPLE, POLL and ONCE subscriptions are supported for non-DB data only if the subscribe path
does not contain wildcards.
Translib retrieves current data is through the existing Get flow - using app module's `translateGet` and `processGet` functions.
Wildcard paths cannot be passed to these functions due to a YGOT limitation.
It requires major changes in YGOT code generator, standard YGOT libraries and almost all existing app module code.

An alterante approach is to extend app module's `processSubscribe` scope to return all specific paths of a wildcard path.
However this will not be practical for the app modules if non-DB data source does not provide APIs to retrive object keys (equivalent of redis KEYS or SCAN).
This is individual non-DB data source's implementation specific capability and there is standard "non-DB data access" layer today.
Hence the current design does not support wildcard paths for non-DB data.
Future releases can enhance this based on how non-DB data access evolves in management framework.

## 3.2 DB Changes

No DB schema change required.

## 3.3 Switch State Service Design

N/A

## 3.4 SyncD

N/A

## 3.5 SAI

N/A

## 3.6 User Interface

### 3.6.1 Data Models

No data model is added/modified by the infrastructure.

### 3.6.2 CLI

No CLIs are added/modified.

### 3.6.3 REST API Support

No REST APIs are added/modified.

### 3.6.4 gNMI Support

No new APIs are added/modified by the infrastructure.

## 3.7 Warm Boot Support

N/A

## 3.8 Upgrade and Downgrade Considerations

N/A

## 3.9 Resource Needs

N/A

# 4 Flow Diagrams

Flow diagrams are already described in section 3.1.
Please refer to the following:

- [Section 3.1.5](#315-on_change-subscription-for-db-data) for ON_CHANGE subscription
- [Section 3.1.7](#317-sample-subscription-for-db-data) for SAMPLE subscription
- [Section 3.1.9](#319-target_defined-subscription) for TARGET_DEFINED subscription
- [Section 3.1.10](#3110-once-subscription) for ONCE and POLL subscription

# 5 Error Handling

N/A

# 6 Serviceability and Debug

N/A

# 7 Scalability

SAMPLE, ON_CHANGE, TARGET_DEFINED are long running subscriptions and they maintain a data cache per session.
Cache size is not deterministic; it depends on number of clients and the subscribed paths.
E.g, If clients subscribe for specific leaf nodes (like `/interfaces/interface[name=*]/state/oper-status`), the cache would hold only that leaf related data (or DB entry in case of ON_CHANGE).
But if clients subscribe for top level YANG containers/lists (like `/interfaces/interface[name=*]`) the cache would hold information for the entire data tree.
This can drastically increase the memory consumption of telemetry service.
But the exact numbers depend on the implementation and we do not have a correct estimate at this point.
Resource monitoring parameters for telemetry service will have to be adjusted later - after more apps support subscription.

CPU consumption telemetry service can also increase if the DB entries monitored through ON_CHANGE subscriptions change frequently.
E,g. interface or ACL counters.
Applications should disable ON_CHANGE for such YANG nodes through transformer annotations or subtree transformer's subscribe callback functions.
Non-transformer based apps can directly indicate this preference in `translateSubscribe` response.

gNMI server can also throttle the subscription requests based on the current device state.
It can close the subscribe RPC with RESOURCE_EXHAUSTED status if it is oversubscribed.
Such throttling mechanism should also consider the platform capabilities.
This can be explored in future releases.

# 8 Platform

N/A
This is a platform independent feature.

# 9 Limitations

- ON_CHANGE not supported for YANG nodes mapped to non-DB data, like FRR socket.
- Subscribe not supported for wildcard paths pointing to non-DB data nodes.
- Subscribe not supported for paths with wildcard element names.
  Only wildcard keys are allowed.
- Wildcard paths not supported in gNMI Get RPC.

# 10 Unit Test

## 10.1 Test Cases

Following unit test cases require apps code changes to handle subscription.
IGMP Snooping, ACL and TAM YANG paths can be used to cover most of them.
Some cases may need test YANGs to simulate the condition.
Each test will cover value create/update/delete cases wherever possible.

ON_CHANGE test cases:

- ON_CHANGE subscription for top level container path
- ON_CHANGE subscription for list node with wildcard key
- ON_CHANGE subscription for list node with specific key
- ON_CHANGE subscription for list node with specific, but non-existing key
- ON_CHANGE subscription for nested list node with wildcard key
- ON_CHANGE subscription for nested list node with specific key
- ON_CHANGE subscription for top level leaf nodes (without any keys)
- ON_CHANGE subscription for container inside a list
- ON_CHANGE subscription for leaf node inside a list, with an initial value
- ON_CHANGE subscription for leaf node inside a list, value does not exits
- ON_CHANGE subscription for leaf-list node inside a list; with leaf-list mapped to a DB field
- ON_CHANGE subscription for leaf-list node inside a list; with leaf-list mapped to its own DB entry
- ON_CHANGE subscription for an unsupported path - for non-transformer app
- ON_CHANGE subscription for an unsupported path - marked through transformer annotation
- ON_CHANGE subscription for an unsupported path - marked through subtree transformer code
- ON_CHANGE subscription for a non-DB path
- ON_CHANGE subscription with unknown path
- ON_CHANGE subscription with `updates_only` set to true

SAMPLE subscription test cases:

- SAMPLE subscription for top level container path
- SAMPLE subscription for list node with wildcard key
- SAMPLE subscription for list node with specific key
- SAMPLE subscription for nested list node with wildcard key
- SAMPLE subscription for nested list node with specific key
- SAMPLE subscription for top level leaf nodes (without any keys)
- SAMPLE subscription for container inside a list
- SAMPLE subscription for leaf node inside a list
- SAMPLE subscription for leaf-list node inside a list; with leaf-list mapped to a DB field
- SAMPLE subscription for leaf-list node inside a list; with leaf-list mapped to its own DB entry
- SAMPLE subscription for a non-DB path, with wildcard keys
- SAMPLE subscription for a non-DB path, without wildcard keys
- SAMPLE subscription with unknown path
- SAMPLE subscription with sample_interval less than min interval supported by YANG
- SAMPLE subscription with `suppress_redundant` set to true
- SAMPLE subscription with `updates_only` set to true.
- Both ON_CHANGE and SAMPLE subscriptions in a single request.

TARGET_DEFINED subscription test cases:

- TARGET_DEFINED for a node that prefers ON_CHANGE
- TARGET_DEFINED for a node that prefers SAMPLE -- preference set through transformer annotation
- TARGET_DEFINED for a node that prefers SAMPLE -- preference set through subtree transformer
- TARGET_DEFINED for a node that prefers SAMPLE -- for non-transformer app
- TARGET_DEFINED for a node that prefers ON_CHANGE, but a child node prefers SAMPLE -- preference set through transformer annotation
- TARGET_DEFINED for a node that prefers ON_CHANGE, but a child node prefers SAMPLE -- preference set through subtree transformer
- TARGET_DEFINED for a node that prefers ON_CHANGE, but a child node prefers SAMPLE -- for non-transformer app
- TARGET_DEFINED for non-DB path
- TARGET_DEFINED for unknown path

ONCE subscription test cases:

- ONCE subscription for top level container path
- ONCE subscription for list node with wildcard key
- ONCE subscription for list node with specific key
- ONCE subscription for top level leaf nodes (without any keys)
- ONCE subscription for container inside a list
- ONCE subscription for leaf node inside a list
- ONCE subscription for leaf-list node inside a list; with leaf-list mapped to a DB field
- ONCE subscription for a non-DB path, with wildcard keys
- ONCE subscription for a non-DB path, without wildcard keys
- ONCE subscription with unknown path

## 10.2 gNMI UT Framework for spytest

TBD

## 10.3 UT with gnmi_cli tool

OpenConfig community provides [gnmi_cli](https://github.com/openconfig/gnmi/blob/master/cmd/gnmi_cli/gnmi_cli.go)
tool which can be used for quick testing gNMI subscriptions on the SONiC device.
This tool will be available in the **telemetry** container.
Alternatively, it can be obtained from the build server `sonic-buildimage/src/sonic-telemetry/build/bin/gnmi_cli`.
Few of the sample usages are listed below.

1\) ON_CHANGE subscription

```
gnmi_cli -insecure -logtostderr -target OC_YANG -address localhost:8080 \
    -query_type streaming -streaming_type ON_CHANGE \
    -query openconfig-interfaces:interfaces/interface[name=*]/config
```

2\) SAMPLE subscription, with sample interval of 30 seconds

```
gnmi_cli -insecure -logtostderr -target OC_YANG -address localhost:8080 \
    -query_type streaming -streaming_type SAMPLE -streaming_sample_interval 30 \
    -query openconfig-interfaces:interfaces/interface[name=*]/config
```

3\) TARGET_DEFINED subscription

```
gnmi_cli -insecure -logtostderr -target OC_YANG -address localhost:8080 \
    -query_type streaming -streaming_type TARGET_DEFINED \
    -query openconfig-interfaces:interfaces/interface[name=*]/config
```

4\) POLL subscription, with polling interval of 20 seconds

```
gnmi_cli -insecure -logtostderr -target OC_YANG -address localhost:8080 \
    -query_type polling -polling_interval 20s \
    -query openconfig-interfaces:interfaces/interface[name=*]/config
```

5\) ONCE subscription

```
gnmi_cli -insecure -logtostderr -target OC_YANG -address localhost:8080 \
    -query_type once \
    -query openconfig-interfaces:interfaces/interface[name=*]/config
```

6\) Subscribing to multiple paths (passing comma separated path list in -query option)

```
gnmi_cli -insecure -logtostderr -target OC_YANG -address localhost:8080 \
    -query_type streaming -streaming_type ON_CHANGE \
    -query openconfig-interfaces:interfaces/interface[name=*]/config,openconfig-acl:acl/acl-sets
```

## 10.4 Supported YANG Paths

Please refer to [Subscribe YANG Path](https://github.com/BRCM-SONIC/sonic_doc_private/blob/master/manageability/mgmt-framework/Telemetry_Subscribe_Paths.md) guide.

# 11 Internal Design Information

## 11.1 IS-CLI Compliance

N/A

## 11.2 Broadcom SONiC Packaging

This feature will be available in all packages.

## 11.3 Broadcom Silicon Considerations

N/A

## 11.4 Design Alternatives

N/A

## 11.5 Broadcom Release Matrix

| Release | Change(s)    |
|---------|--------------|
| 3.2.0   | \* Subscribe RPC for paths with wildcard keys<br> \* ON_CHANGE subscription for eligible YANG paths |
| 4.0.0   | \* TARGET_DEFINED, SAMPLE, POLL and ONCE subscriptions<br> \* Scalar encoded telemetry updates |
