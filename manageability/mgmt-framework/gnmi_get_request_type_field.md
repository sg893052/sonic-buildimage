# Feature Name
GNMI Get Request Type Field
# High Level Design Document
#### Rev 0.6

# Scope
This document describes the high level design of supporting the 'type' field in a gNMI [GNMI](#GNMI) GetRequest Message in the management framework.

# Feature Overview

gNMI is a gRPC-based protocol for the modification and retrieval of configuration from a target device, as well as the control and generation of telemetry streams from a target device to a data collection system [GNMI](#GNMI)

The Get RPC provides an interface by which a client can request a set of paths to be serialized and transmitted to it by the target. The client sends a GetRequest message to the target, specifying the data that is to be retrieved. The fields of the GetRequest message are described in Section 3.3.1 of [GNMI](#GNMI).

https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md#331-the-getrequest-message

## Requirements
Support the 'type' field in the GetRequest message.

type - the type of data that is requested from the target. The valid values for type are described below.

The types of data currently defined are:

CONFIG - specified to be data that the target considers to be read/write. If the data schema is described in YANG, this corresponds to the "config true" set of leaves on the target.

STATE - specified to be the read-only data on the target. If the data schema is described in YANG, STATE data is the "config false" set of leaves on the target.

OPERATIONAL - specified to be the read-only data on the target that is related to software processes operating on the device, or external interactions of the device.

CONFIG, and STATE are already described in the [Query Parameter Framework](#query-parameter-framework) requirements

There is an expired draft that describes how OPERATIONAL data could be identified. Till a formal definition makes its way into the specification, we'll attempt to nail it down to mean any read-only data ("config false") that does not have a corresponding "config" path (i.e. "../../config/<data>" should not exist), excluding any data in "config" containers. Examples of this data are statistics, counters, and protocol negotiated values.

## Examples

Note: In the examples below some of the text has been edited for clarity. 

### gNMI Get RPC for a RADIUS server with ALL type data

Request all the data (including configuration, state, and operational data)

```text

gnmi_get -xpath '/openconfig-system:system/aaa/server-groups/server-group[name=RADIUS]/servers/server[address=10.10.10.10]' -logtostderr -target_addr localhost:8080
== getRequest:
prefix: <
>
path: <
  elem: <
    name: "openconfig-system:system"
  >
  elem: <
    name: "aaa"
  >
  elem: <
    name: "server-groups"
  >
  elem: <
    name: "server-group"
    key: <
      key: "name"
      value: "RADIUS"
    >
  >
  elem: <
    name: "servers"
  >
  elem: <
    name: "server"
    key: <
      key: "address"
      value: "10.10.10.10"
    >
  >
>
encoding: JSON_IETF

== getResponse:
notification: <
  timestamp: 1634340866464546744
  prefix: <
  >
  update: <
    path: <
      elem: <
        name: "openconfig-system:system"
      >
      elem: <
        name: "aaa"
      >
      elem: <
        name: "server-groups"
      >
      elem: <
        name: "server-group"
        key: <
          key: "name"
          value: "RADIUS"
        >
      >
      elem: <
        name: "servers"
      >
      elem: <
        name: "server"
        key: <
          key: "address"
          value: "10.10.10.10"
        >
      >
    >
    val: <
      json_ietf_val: "
{
    \"openconfig-system:server\": [
        {
            \"address\": \"10.10.10.10\",
            \"config\": {
                \"address\": \"10.10.10.10\",
                \"openconfig-system-ext:auth-type\": \"chap\",
                \"openconfig-system-ext:priority\": 1
            },
            \"radius\": {
                \"config\": {
                    \"openconfig-aaa-radius-ext:encrypted\": true,
                    \"retransmit-attempts\": 1,
                    \"secret-key\": \"\"
                },
                \"state\": {
                    \"counters\": {
                        \"access-accepts\": \"2\",
                        \"access-rejects\": \"1\",
                        \"openconfig-aaa-radius-ext:access-requests\": \"3\"
                    },
                    \"retransmit-attempts\": 1
                }
            },
            \"state\": {
                \"address\": \"10.10.10.10\",
                \"openconfig-system-ext:auth-type\": \"chap\",
                \"openconfig-system-ext:priority\": 1
            },
            \"tacacs\": {
                \"config\": {
                    \"openconfig-aaa-tacacs-ext:encrypted\": true,
                    \"secret-key\": \"\"
                }
            }
        }
    ]
}
"
    >
  >
>

```

### gNMI Get RPC for a RADIUS server with only CONFIG type data

Request only configuration data

```text

gnmi_get -xpath '/openconfig-system:system/aaa/server-groups/server-group[name=RADIUS]/servers/server[address=10.10.10.10]' --data_type CONFIG -logtostderr -target_addr localhost:8080
== getRequest:
prefix: <
>
path: <
  elem: <
    name: "openconfig-system:system"
  >
  elem: <
    name: "aaa"
  >
  elem: <
    name: "server-groups"
  >
  elem: <
    name: "server-group"
    key: <
      key: "name"
      value: "RADIUS"
    >
  >
  elem: <
    name: "servers"
  >
  elem: <
    name: "server"
    key: <
      key: "address"
      value: "10.10.10.10"
    >
  >
>
type: CONFIG
encoding: JSON_IETF

== getResponse:
notification: <
  timestamp: 1634340926214511357
  prefix: <
  >
  update: <
    path: <
      elem: <
        name: "openconfig-system:system"
      >
      elem: <
        name: "aaa"
      >
      elem: <
        name: "server-groups"
      >
      elem: <
        name: "server-group"
        key: <
          key: "name"
          value: "RADIUS"
        >
      >
      elem: <
        name: "servers"
      >
      elem: <
        name: "server"
        key: <
          key: "address"
          value: "10.10.10.10"
        >
      >
    >
    val: <
      json_ietf_val: "
{
    \"openconfig-system:server\": [
        {
            \"address\": \"10.10.10.10\",
            \"config\": {
                \"address\": \"10.10.10.10\",
                \"openconfig-system-ext:auth-type\": \"chap\",
                \"openconfig-system-ext:priority\": 1
            },
            \"radius\": {
                \"config\": {
                    \"openconfig-aaa-radius-ext:encrypted\": true,
                    \"retransmit-attempts\": 1,
                    \"secret-key\": \"\"
                }
            },
            \"tacacs\": {
                \"config\": {
                    \"openconfig-aaa-tacacs-ext:encrypted\": true,
                    \"secret-key\": \"\"
                }
            }
        }
    ]
}
"
    >
  >
>

```

### gNMI Get RPC for a RADIUS server with only STATE type data

Request only state data (which includes operational data)

```text

gnmi_get -xpath '/openconfig-system:system/aaa/server-groups/server-group[name=RADIUS]/servers/server[address=10.10.10.10]' --data_type STATE -logtostderr -target_addr localhost:8080
== getRequest:
prefix: <
>
path: <
  elem: <
    name: "openconfig-system:system"
  >
  elem: <
    name: "aaa"
  >
  elem: <
    name: "server-groups"
  >
  elem: <
    name: "server-group"
    key: <
      key: "name"
      value: "RADIUS"
    >
  >
  elem: <
    name: "servers"
  >
  elem: <
    name: "server"
    key: <
      key: "address"
      value: "10.10.10.10"
    >
  >
>
type: STATE
encoding: JSON_IETF

== getResponse:
notification: <
  timestamp: 1634340940535508475
  prefix: <
  >
  update: <
    path: <
      elem: <
        name: "openconfig-system:system"
      >
      elem: <
        name: "aaa"
      >
      elem: <
        name: "server-groups"
      >
      elem: <
        name: "server-group"
        key: <
          key: "name"
          value: "RADIUS"
        >
      >
      elem: <
        name: "servers"
      >
      elem: <
        name: "server"
        key: <
          key: "address"
          value: "10.10.10.10"
        >
      >
    >
    val: <
      json_ietf_val: "
{
    \"openconfig-system:server\": [
        {
            \"address\": \"10.10.10.10\",
            \"radius\": {
                \"state\": {
                    \"counters\": {
                        \"access-accepts\": \"2\",
                        \"access-rejects\": \"1\",
                        \"openconfig-aaa-radius-ext:access-requests\": \"3\"
                    },
                    \"retransmit-attempts\": 1
                }
            },
            \"state\": {
                \"address\": \"10.10.10.10\",
                \"openconfig-system-ext:auth-type\": \"chap\",
                \"openconfig-system-ext:priority\": 1
            }
        }
    ]
}
"
    >
  >
>

```

### gNMI Get RPC for a RADIUS server with only OPERATIONAL type data

Request only the operational data (Eg: counters)

```text

gnmi_get -xpath '/openconfig-system:system/aaa/server-groups/server-group[name=RADIUS]/servers/server[address=10.10.10.10]' --data_type OPERATIONAL -logtostderr -target_addr localhost:8080
== getRequest:
prefix: <
>
path: <
  elem: <
    name: "openconfig-system:system"
  >
  elem: <
    name: "aaa"
  >
  elem: <
    name: "server-groups"
  >
  elem: <
    name: "server-group"
    key: <
      key: "name"
      value: "RADIUS"
    >
  >
  elem: <
    name: "servers"
  >
  elem: <
    name: "server"
    key: <
      key: "address"
      value: "10.10.10.10"
    >
  >
>
type: OPERATIONAL
encoding: JSON_IETF

== getResponse:
notification: <
  timestamp: 1634340952348637025
  prefix: <
  >
  update: <
    path: <
      elem: <
        name: "openconfig-system:system"
      >
      elem: <
        name: "aaa"
      >
      elem: <
        name: "server-groups"
      >
      elem: <
        name: "server-group"
        key: <
          key: "name"
          value: "RADIUS"
        >
      >
      elem: <
        name: "servers"
      >
      elem: <
        name: "server"
        key: <
          key: "address"
          value: "10.10.10.10"
        >
      >
    >
    val: <
      json_ietf_val: "
{
    \"openconfig-system:server\": [
        {
            \"address\": \"10.10.10.10\",
            \"radius\": {
                \"state\": {
                    \"counters\": {
                        \"access-accepts\": \"2\",
                        \"access-rejects\": \"1\",
                        \"openconfig-aaa-radius-ext:access-requests\": \"3\"
                    }
                }
            }
        }
    ]
}
"
    >
  >
>


```


# Limitations

- The 'type' field of GetRequest message is supported only for the [transl clients](#translib). The "OTHERS"/Non-DB clients, and DB clients do not support this field, and will continue to return the unsupported request type error, if said 'type' field is included.

- OPERATIONAL type is supported only for Openconfig Yang modeled data.

- Similar to Query Parameter Framework limitations, empty containers, or lists will not be present in returned data

# Unit Test

| **Test**                       | **Description**                             |
| ------------------------------ | ------------------------------------------- |
|                                |                                             |
| gNMI GetRequest RPC            | gNMI GetRequest with default(ALL) type data |
|                                |                                             |
| gNMI GetRequest RPC ALL        | gNMI GetRequest with ALL type data          |
|                                |                                             |
| gNMI GetRequest RPC CONFIG     | gNMI GetRequest with CONFIG type data       |
|                                |                                             |
| gNMI GetRequest RPC STATE      | gNMI GetRequest with STATE type data        |
|                                |                                             |
| gNMI GetRequest RPC OPERATIONAL| gNMI GetRequest with OPERATIONAL type data  |

# References

## GNMI

https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md

## Query Parameter Framework

https://github.com/project-arlo/SONiC/blob/master/doc/mgmt/SONiC_QueryParameterForGet_HLD.md

## Translib

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md#3226-Translib

