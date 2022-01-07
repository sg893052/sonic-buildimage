# Configuration Last Updated Timestamp
# High Level Design Document
#### Rev 0.2

# Scope
This document describes the high level design of supporting a way to retrieve the time of last update of the Configuration Database by the [Management Framework](#mf).

# Feature Overview

[Management framework](#mf) is a SONiC application which is responsible for providing various common North Bound Interfaces (NBIs) for the purposes of managing configuration and status on SONiC devices.

There is a need for external applications, like a remote controller, to detect the time of last update of the configuration on the SONiC device.

## Requirements

Provide an interface to return the last updated time of the Config DB by the Management Framework.

# Design

## ConfigDB Schema

No changes to the schema are anticipated. The Concurrent Access of the [DB Access Layer](#dbal) will be updated to note the timestamp of last update during any write transaction.
The Concurrent Access watch key for the entire ConfigDB, *CONFIG_DB_UPDATED*, will be used to note the timestamp of last update.

## OC Yang

OC Yang will be updated to add a config-time leaf to the system state container.
This is very similar to the current "boot-time" leaf, which gives the time of the last system restart.

```text

...
module: openconfig-system
  +--rw system
     +--rw config
...
     +--ro state
...
     |  +--ro config-time?                   oc-types:timeticks64
...


```

```text

    leaf config-time {
        type oc-types:timeticks64;
        units "nanoseconds";
        description
          "This timestamp indicates the time of the last configuration change
           on the system. The value is the timestamp in nanoseconds relative
          to the Unix Epoch (Jan 1, 1970 00:00:00 UTC).";
    }  

```

Both RESTCONF, and gNMI requests should be supported.

## KLISH

No KLISH changes are anticipated, since this is largely intended for remote programmatic applications (i.e. controllers).
However, this does not preclude updating the outputs of KLISH commands like *show version*, *show running-configuration*, or *show system* to display the time of last configuration change.

## RESTCONF

Following is an example RESTCONF request to obtain the last configuration modification time of a device.


```text

Request:

curl -X GET "https://5.5.5.5/restconf/data/openconfig-system:system/state"

Response:

{
   "openconfig-system:state" : {
      "hostname" : "sonic",
      "boot-time" : "1641294859000000000",
      "config-time" : "1640979392992330687",
      "current-datetime" : "2022-01-05T01:47:55Z+00:00"
   }
}

```

## gNMI

Following is an example gNMI request to obtain the last configuration modification time of a device.


```text

Request:

/usr/sbin/gnmi_get -xpath /openconfig-system:system/state -target_addr 127.0.0.1:8080 -username admin -password broadcom -pretty

Response:

== getRequest:
prefix: <
>
path: <
  elem: <
    name: "openconfig-system:system"
  >
  elem: <
    name: "state"
  >
>
encoding: JSON_IETF

== getResponse:
notification: <
  timestamp: 1641427078194365787
  prefix: <
  >
  update: <
    path: <
      elem: <
        name: "openconfig-system:system"
      >
      elem: <
        name: "state"
      >
    >
    val: <
      json_ietf_val: "{\"openconfig-system:state\":{\"boot-time\":\"1641294860000000000\",\"config-time\":\"1640979392992330687\",\"current-datetime\":\"2022-01-05T23:57:58Z+00:00\",\"hostname\":\"sonic\"}}"
    >
  >
>


```

# Limitations

- The config-time stamp will be updated only for changes from Management Framework. Controllers are expected to use the Management Framework.

- For the same reason as the previous bullet, there is no KLISH interface to retrieve the data

- The time is assumed to be monotonically increasing on the device, which is usually the case for correctly operating devices.

- Currently, the existing boot-time leaf is defined in the OC-Yang to be in units of nanoseconds; However, empirical observation of the response received to a RESTCONF Get request shows this to be actually returned in seconds. This is currently under investigation, and hope to multiply this by 10^9 as a fix.

- If a device has not been configured after restart, config-time leaf will not be returned.


# Unit Test

| **Test**                       | **Description**                             |
| ------------------------------ | ------------------------------------------- |
|                                |                                             |
| TS after boot (RESTCONF)       | Config TimeStamp (RESTCONF) should be absent|
|                                |                                             |
| TS after boot (gNMI)           | Config TimeStamp (gNMI)     should be absent|
|                                |                                             |
| TS after write (RESTCONF)      | Config TimeStamp (RESTCONF) should be set   |
|                                |                                             |
| TS after write (gNMI)          | Config TimeStamp (gNMI)     should be set   |
|                                |                                             |
| TS after rewrite (RESTCONF)    | Config TimeStamp (RESTCONF) should change   |
|                                |                                             |
| TS after rewrite (gNMI)        | Config TimeStamp (gNMI)     should change   |
|                                |                                             |


# References

## MF

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md

## DBAL

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md#32265-db-access-layer

