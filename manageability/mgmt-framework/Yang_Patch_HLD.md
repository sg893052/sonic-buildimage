# Feature Name
YANG Patch Media Type
# High Level Requirements Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Feature Overview](#feature-overview)
  * [Requirements](#requirements)
  * [Limitations](#limitations) 
  * [Example](#example) 
  
# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/23/2021  |   Mohammed Faraaz  | Initial version                   |

# About this Manual
This document provides general information about the YANG Patch Media Type feature implementation in SONiC mgmt-framework's RESTCONF server.
# Scope
This document describes the high level design of YANG Patch Media Type feature. Describing RESTCONF and other related information are beyond the scope of this document.

# Feature Overview
A "YANG Patch" is an ordered list of edits that are applied to the target configuration datastore by the RESTCONF server.
The YANG Patch operation is invoked by the RESTCONF client by sending a PATCH method request with a representation using 
"application/yang-patch+json". The message-body representing the YANG Patch input parameters MUST be present.

## Requirements

### Functional Requirements

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

# Limitations

1. Does not support “insert” and “move” operations because SONiC does not support order by user.
2. Does not support YANG patch for data store resource i.e. The target resource cannot be '/' (top-level).

# Example

```
   The following example shows several songs being added to an existing
   album.

   o  Each of two edits contains one song.

   o  Both edits succeed, and new sub-resources are created.

   Request from the RESTCONF client:

      PATCH /restconf/data/example-jukebox:jukebox/\
         library/artist=Foo%20Fighters/album=Wasting%20Light \
         HTTP/1.1
      Host: example.com
      Accept: application/yang-data+json
      Content-Type: application/yang-patch+json

      {
        "ietf-yang-patch:yang-patch" : {
          "patch-id" : "add-songs-patch-2",
          "edit" : [
            {
              "edit-id" : "edit1",
              "operation" : "create",
              "target" : "/song=Rope",
              "value" : {
                "song" : [
                  {
                    "name" : "Rope",
                    "location" : "/media/rope.mp3",
                    "format" : "MP3",
                    "length" : 259
                  }
                ]
              }
            },
            {
              "edit-id" : "edit2",
              "operation" : "create",
              "target" : "/song=Dear%20Rosemary",
              "value" : {
                "song" : [
                  {
                    "name" : "Dear Rosemary",
                    "location" : "/media/dear_rosemary.mp3",
                    "format" : "MP3",
                    "length" : 269
                  }
                ]
              }
            }
          ]
        }
      }

   Response from the RESTCONF server:

      HTTP/1.1 200 OK
      Date: Thu, 26 Jan 2017 20:56:30 GMT
      Server: example-server
      Last-Modified: Thu, 26 Jan 2017 20:56:30 GMT
      Content-Type: application/yang-data+json

      {
        "ietf-yang-patch:yang-patch-status" : {
          "patch-id" : "add-songs-patch-2",
          "ok" : [null]
        }
      }            
```

