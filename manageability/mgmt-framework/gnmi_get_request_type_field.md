# Feature Name
GNMI Get Request Type Field
# High Level Design Document
#### Rev 0.1

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

CONFIG, and STATE are already described in the [Query Parameter Framework](#query-parameter-framework) requirements (https://github.com/BRCM-SONIC/sonic_doc_private/blob/9d40eeb6874060108de14a8492657f717e084f10/manageability/mgmt-framework/query_parameter_framework.md#the-content-query-parameter)

There is an expired draft that describes how OPERATIONAL data could be identified. Till a formal definition makes its way into the specification, we'll attempt to nail it down to mean any read-only data ("config false") that does not have a corresponding "config" path (i.e. "../../config/<data>" should not exist), excluding any data in "config" containers. Examples of this data are statistics, counters, and protocol negotiated values.

# References

## GNMI

https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md

## Query Parameter Framework

https://github.com/BRCM-SONIC/sonic_doc_private/blob/9d40eeb6874060108de14a8492657f717e084f10/manageability/mgmt-framework/query_parameter_framework.md

