# Feature Name
Query Parameter Framework
# High Level Design Document
#### Rev 0.1

# Scope
This document describes the high level design of the Query Parameter support in the mgmt-framework.

# Feature Overview
RESTCONF is a protocol based on HTTP for configuring data defined in YANG. Each
RESTCONF operation allows zero or more query parameters to be present in the requested URI [RFC8040](#RFC8040).

## Requirements
Support the following 2 query parameter support in the framework.

### The "content" Query Parameter
https://tools.ietf.org/html/rfc8040#section-4.8.1


### The "depth" Query Parameter
https://tools.ietf.org/html/rfc8040#section-4.8.2


# References

## RFC8040

https://tools.ietf.org/html/rfc8040

