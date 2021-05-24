# Feature Name
YANG Patch Media Type
# High Level Design Document
#### Rev 0.2

# Table of Contents
  - [Revision](#revision)
  - [About This Manual](#about-this-manual)
  - [Scope](#scope)
  - [1 Feature Overview](#1-feature-overview)
  - [2 Requirements](#2-requirements)
  - [3 Design Overview](#3-design-overview)
    - [3.1 Basic Approach](#31-basic-approach)
     - [3.1.1 REST Server changes](#311-rest-server-changes)
     - [3.1.2 Translib changes](#312-translib-changes)
     - [3.1.3 DB Access Layer changes](#313-db-access-layer-changes)
     - [3.1.4 REST CLI Client changes](#314-rest-cli-client-changes)
  - [4 Functionality](#4-functionality)
    - [4.1 Target Resource](#41-target-resource)
    - [4.2 Yang Patch Request](#42-yang-patch-request)
    - [4.3 Yang Patch Status Response](#43-yang-patch-status-response)
    - [4.4 Edit operations](#44-edit-operations)
  - [5 Unit Test](#5-unit-test) 
    - [5.1 Example Module](#51-example-module) 
    - [5.2 Positive Test case](#52-positive-test-case) 
    - [5.3 Negative Test case](#53-negative-test-case) 
  - [6 Limitations](#6-limitations) 
  
# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/23/2021  |   Mohammed Faraaz  | Initial version                   |
| 0.2 | 05/23/2021  |   Mohammed Faraaz  | Added Design and UT sections      |

# About this Manual
This document provides general information about the YANG Patch Media Type feature implementation in SONiC mgmt-framework's RESTCONF server.

# Scope
This document describes the high level design of YANG Patch Media Type feature. Describing RESTCONF and other related information are beyond the scope of this document.

# 1 Feature Overview
A "YANG Patch" is an ordered list of edits that are applied to the target configuration datastore by the RESTCONF server.
The YANG Patch operation is invoked by the RESTCONF client by sending a PATCH method request with a representation using 
"application/yang-patch+json". The message-body representing the YANG Patch input parameters MUST be present.

# 2 Requirements

## 2.1 Functional Requirements

1. The RESTCONF server should implement the "YANG PATCH" feature as described in the [RFC8072](https://tools.ietf.org/html/rfc8072)
2. The RESTCONF server should allow multiple sub-resources to be edited within the same PATCH method.
3. The RESTCONF server should allow a more precise edit operation than the "plain patch" mechanism found in [RFC8040](https://tools.ietf.org/html/rfc8040). i.e. edits operations can be heterogenuous.
4. The RESTCONF server should support "create", "delete", "merge", "replace", and "remove" operations.
5. The RESTCONF server should process "edits" in the explicit order. i.e. The edits are processed in client-specified order.
6. The RESTCONF server should process errors precisely even when multiple errors occur in the same YANG Patch request.
7. The YANG Patch request should be an atomic operation. i.e. All the edits must be succeded, otherwise an error will be thrown.
8. The RESTCONF server should log the YANG Patch's "patch-id" in the audit-log, this is useful for debugging.
9. The RESTCONF server MUST return the "Accept-Patch" header field in an OPTIONS response, as specified in [RFC5789], which includes the media type for YANG Patch.  This is needed by a client to determine the message-encoding formats supported by the server (e.g., XML, JSON, or both).
   The following is an example of an "Accept-Patch" header:
   Accept-Patch: application/yang-patch+json

# 3 Design Overview

![YANG_PATCH_DESIGN](yang_patch_sequence_diagram.svg)

## 3.1 Basic Approach

## 3.1.1 REST Server changes

- RESTCONF Server's Request handler will be enhanced to accept the PATCH Request with media type ["application/yang-patch+json"](https://datatracker.ietf.org/doc/html/rfc8072#section-4.2.2).
- RESTCONF Server will enhance OPTIONS response to include “application/yang-patch+json” content type in the “Accept-Patch” header.
- RESTCONF Server will implement the [":yang-patch" RESTCONF Capability](https://datatracker.ietf.org/doc/html/rfc8072#section-4.3).
- RESTCONF Server's will implement below [YANG PATCH template](https://datatracker.ietf.org/doc/html/rfc8072#section-3) to accept the request and to send the response.

## 3.1.2 Translib changes

- Translib's Bulk API will be enhanced to process the Ordered edits. i.e. to preserve and process the edits in a order sent by a REST Client.
- Translib's Bulk API will be enhanced to process the request atomically.

## 3.1.3 DB Access Layer changes

- DB Access layer's GET APIs will be enhanced to read from the current transaction cache.

## 3.1.4 REST CLI Client changes

- Extend the cli_client APIs to support YANG PATCH.

# 4 Functionality

## 4.1 Target Resource

The YANG Patch operation uses the RESTCONF target resource URI to identify the resource that will be patched.  This must be a configuration data resource within the datastore resource, e.g., "{+restconf}/data/ietf-interfaces:interfaces", to edit sub-resources within a top-level configuration (data resource).

## 4.2 Yang Patch Request

A YANG Patch is identified by a unique "patch-id", and it may have an optional comment.  A patch is an ordered collection of edits.  
Each edit is identified by an "edit-id", and it has an edit operation ("create", "delete", "merge", "replace", or "remove") that is applied to the target resource.
Each edit can be applied to a sub-resource "target" within the target resource.
The YANG Patch operation is invoked by the RESTCONF client by sending a PATCH method request with a representation using the media type "application/yang-patch+json" with a message body in a below template

```text
     +---- yang-patch
           +---- patch-id    string
           +---- comment?    string
           +---- edit* [edit-id]
              +---- edit-id      string
              +---- operation    enumeration
              +---- target       target-resource-offset
              +---- point?       target-resource-offset
              +---- where?       enumeration
              +---- value?
```

Note that YANG Patch can only edit data resources.  The PATCH method cannot be used to replace the datastore resource.

## 4.3 Yang Patch Status Response

A message-body representing the YANG Patch Status is returned to the RESTCONF client to report the detailed status of the edit operation. 
Below is the Response body template which RESTCONF server uses

```text
     +---- yang-patch-status
           +---- patch-id       string
           +---- (global-status)?
           |  +--:(global-errors)
           |  |  +---- errors
           |  |     +---- error*
           |  |        +---- error-type       enumeration
           |  |        +---- error-tag        string
           |  |        +---- error-app-tag?   string
           |  |        +---- error-path?      instance-identifier
           |  |        +---- error-message?   string
           |  |        +---- error-info?
           |  +--:(ok)
           |     +---- ok?            empty
           +---- edit-status
              +---- edit* [edit-id]
                 +---- edit-id    string
                 +---- (edit-status-choice)?
                    +--:(ok)
                    |  +---- ok?        empty
                    +--:(errors)
                       +---- errors
                          +---- error*
                             +---- error-type       enumeration
                             +---- error-tag        string
                             +---- error-app-tag?   string
                             +---- error-path?      instance-identifier
                             +---- error-message?   string
                             +---- error-info?
```
## 4.4 Edit operations

Edit operations are described breifly in the [RFC8072](https://datatracker.ietf.org/doc/html/rfc8072#section-2.5)

Note: Move and Insert operations are not supported in SONiC.

# 5 Unit Test


## 5.1 Example module

The example YANG module used in this document is a subset of openconfig-acl module.

The YANG subtree diagram for the "openconfig-acl" module

```text
module: openconfig-acl
  +--rw acl
     +--rw acl-sets
     |  +--rw acl-set* [name type]
     |     +--rw name           -> ../config/name
     |     +--rw type           -> ../config/type
     |     +--rw config
     |     |  +--rw name?          string
     |     |  +--rw type?          identityref
     |     |  +--rw description?   string
     |     +--rw acl-entries
     |        +--rw acl-entry* [sequence-id]
     |           +--rw sequence-id    -> ../config/sequence-id
     |           +--rw config
     |           |  +--rw sequence-id?   uint32
     |           |  +--rw description?   string
     |           +--rw ipv4
     |           |  +--rw config
     |           |  |  +--rw source-address?        oc-inet:ipv4-prefix
     |           |  |  +--rw destination-address?   oc-inet:ipv4-prefix
     |           |  |  +--rw dscp?                  oc-inet:dscp
     |           |  |  +--rw protocol?              oc-acl-ext:ip-protocol-type-ext
     |           +--rw transport
     |           |  +--rw config
     |           |  |  +--rw source-port?                          oc-pkt-match-types:port-num-range
     |           |  |  +--rw destination-port?                     oc-pkt-match-types:port-num-range
     |           +--rw actions
     |              +--rw config
     |              |  +--rw forwarding-action    identityref

```

## 5.2 Positive Test case

```text
   The following example shows 4 edits are being applied to an existing ACL which is MyACL1.

   o  First Edit is creating RULE with sequence-id 1
   o  Second Edit is Replacing RULE with sequence-id 1 (This Replaces the rule created in edit-1)
   o  Third Edit is Updating a subtree(ipv4/config/source-address) RULE with sequence-id 1 (Note: this updates the rule created in edit-2)
   o  Fourth Edit is Deleting a RULE with sequence-id 1

   Request from the RESTCONF client:

      PATCH /restconf/data/openconfig-acl:acl/\
         acl-sets/acl-set=MyACL1,ACL_IPV4/acl-entries \
         HTTP/1.1
      Host: example.com
      Accept: application/yang-data+json
      Content-Type: application/yang-patch+json

      {
        "ietf-yang-patch:yang-patch" : {
          "patch-id" : "ACL Rules",
          "edit" : [
            {
              "edit-id" : "edit1",
              "operation" : "create",
              "target" : "/acl-entry=1",
              "value" : {
                "acl-entry": [
                    {
                        "sequence-id": 1,
                        "config": {
                            "sequence-id": 1,
                            "description": "Description for MyACL1 Rule Seq 1"
                        },
                        "ipv4": {
                            "config": {
                                "source-address": "13.1.1.1/32",
                                "destination-address": "23.1.1.1/32",
                                "dscp": 1,
                                "protocol": "IP_TCP"
                            }
                        },
                        "transport": {
                            "config": {
                                "source-port": 101,
                                "destination-port": 201
                            }
                        },
                        "actions": {
                            "config": {
                                "forwarding-action": "ACCEPT"
                            }
                        }
                    }
                ]
              }
            },
            {
              "edit-id" : "edit2",
              "operation" : "replace",
              "target" : "/acl-entry=1",
              "value" : {
                "acl-entry": [
                    {
                        "sequence-id": 1,
                        "config": {
                            "sequence-id": 1,
                            "description": "Description for Changed Rule"
                        },
                        "ipv4": {
                            "config": {
                                "source-address": "13.1.1.1/32",
                                "destination-address": "23.1.1.1/32",
                                "protocol": "IP_TCP"
                            }
                        },
                        "transport": {
                            "config": {
                                "source-port": 102,
                                "destination-port": 102
                            }
                        },
                        "actions": {
                            "config": {
                                "forwarding-action": "ACCEPT"
                            }
                        }
                    }
                ]
              }
            },
            {
              "edit-id" : "edit3",
              "operation" : "merge",
              "target" : "/acl-entry=1/ipv4/config/source-address",
              "value" : {
                 "source-address": "13.3.3.3/32",
                 "dscp": 1                 
              }
            },  
            {
              "edit-id" : "edit4",
              "operation" : "delete",
              "target" : "/acl-entry=1"
            }            
          ]
        }
      }

   Response from the RESTCONF server:

      HTTP/1.1 200 OK
      Date: Thu, 26 Jan 2017 20:56:30 GMT
      Server: sonic-restconf-server
      Last-Modified: Thu, 26 Jan 2017 20:56:30 GMT
      Content-Type: application/yang-data+json

      {
        "ietf-yang-patch:yang-patch-status" : {
          "patch-id" : "ACL Rules",
          "ok" : [null]
        }
      }            
```

## 5.3 Negative Test case

```text
   The following example shows 2 ACLs are being added.

   o  First Edit will fail, because ACL1 already exists
   o  Second Edit will not be attempted as first edit is failed.

   Request from the RESTCONF client:

      PATCH /restconf/data/openconfig-acl:acl/\
         acl-sets \
         HTTP/1.1
      Host: example.com
      Accept: application/yang-data+json
      Content-Type: application/yang-patch+json

      {
        "ietf-yang-patch:yang-patch" : {
          "patch-id" : "Add ACLs",
          "edit" : [
            {
              "edit-id" : "edit1",
              "operation" : "create",
              "target" : "/acl-set=MYACL1,ACL_IPV4",
              "value" : {
                "acl-set": [
                 {
                   "name": "MyACL1",
                   "type": "ACL_IPV4",
                   "config": {
                       "name": "MyACL1",
                       "type": "ACL_IPV4",
                       "description": "Description for MyACL1"
                   },
                 }
                ]
              }
            },
            {
              "edit-id" : "edit2",
              "operation" : "create",
              "target" : "/acl-set=MYACL2,ACL_IPV4",
              "value" : {
                "acl-set": [
                 {
                   "name": "MyACL2",
                   "type": "ACL_IPV4",
                   "config": {
                       "name": "MyACL2",
                       "type": "ACL_IPV4",
                       "description": "Description for MyACL2"
                   },
                 }
                ]
              }
            },            
          ]
        }
      }
      
      HTTP/1.1 409 Conflict
      Date: Thu, 26 Jan 2017 20:56:30 GMT
      Server: example-server
      Last-Modified: Thu, 26 Jan 2017 20:56:30 GMT
      Content-Type: application/yang-data+json

      {
        "ietf-yang-patch:yang-patch-status" : {
          "patch-id" : "Add ACLs",
          "edit-status" : {
            "edit" : [
              {
                "edit-id" : "edit1",
                "errors" : {
                  "error" : [
                    {
                      "error-type": "application",
                      "error-tag": "data-exists",
                      "error-path": "/openconfig-acl:acl/acl\
                         /acl-sets/[acl-set='MyACL1','ACL_IPV4']\,
                      "error-message":
                        "Data already exists; cannot be created"
                    }
                  ]
                }
              }
            ]
          }
        }
      }      
```

# 6 Limitations

1. Does not support “insert” and “move” operations because SONiC does not support order by user.
2. Does not support YANG patch for data store resource i.e. The target resource cannot be '/' (top-level).

