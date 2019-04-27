# Examples/guidance for HLD functional requirements

## Overview

This assumes a 2-stage HLD review process: -

1. First Pass: Within ~1 week of feature start. Fills out section 1, including top-level requirements. Used as input to the sizing process (i.e. what was sized?), and for initial course correction.
2. Second Pass: Document complete, including all sections. Requirements are detailed enough for test case generation, and are immutably numbered. Used for internal and community specification.

If smaller features can get to the second pass within 1 to 2 weeks of starting, then the first pass can be skipped.

The below guidance uses BFD as an example feature. In each case, an example content is shown for the First and Second Pass.

## First Pass - Top-level requirements

- RFC 5880-based BFD solution
- BFD is used for BGP session maintenance.
- IPv4 and IPv4 encapsulation
- >=100 ms Asynchronous mode packet interval

## Second Pass - Detailed requirements

A structured numbering scheme is used using the following &quot;sections&quot;. Not all are required: -

1. Overview - Overview of the feature and its purpose and usage
2. Functionality - This is the main body of the detailed requirements, and contains most of the functionality statements. This section may be further sub-divided into sub-categories as make sense, each covering a different aspect of the functionality. Doing this allows requirements to be later inserted into their natural position without renumbering
3. Interfaces - which interfaces does the feature run on?
4. Configurability - what configuration options will the feature have?
5. Serviceability - Which serviceability features are provided to allow the feature to be debugged in development, QA, and the field?
6. Platforms - In general, all SONiC features should be available on all SONiC hardware platforms. However in some cases there may be some limitations, and these can be documented here.
7. Limitations - Any limitations relative to what might be expected of the feature?

Note that the following requirements topics are covered in other requirements sections in the HLD, so are not covered here: -

- UI's provided and how?
- Scalability and Performance
- Warm Boot

Some general guidance: -

- Think about the person writing test cases - does this give them enough to go on?
- Think about the reviewer - do the requirements describe enough about the feature for them to understand the proposed implementation, and without requiring them to go off and read all of the base standards.
- For features that are based upon a standard, the goal is not to repeat that standard here. Instead, refer to the standard and pick out major support statements, especially for optional features in the standard.

BFD Example

1.0 - BFD (Bidirectional Forwarding Detection) is an OAM protocol used to detect the health (or otherwise) of a forwarding path. It is used as a detection mechanism by other protocols, typically because it can detect failures faster than the protocol-native detection mechanisms.
1.1 - BFD is widely used in Routing environments, and is broadly supported in the OEM products.
1.2 - It is standardized through RFC 5880 and a set of related RFCs (RFC 5881 to RFC 5885)

2.0.0 - Overview
2.0.1 - Compliant with RFC 5880 (BFD) and RFC 5881 (BFD for IPv4 and IPv6 (Single Hop)) unless otherwise stated
2.1.0 - Protocol Functions
2.1.1 - Support Asynchronous mode
2.1.2 - Support Demand mode
2.1.3 - Support Echo function
2.2.0 - Security Functions
2.2.1 - Support MD5 Authentication (incl. meticulous)
2.2.2 - Support SHA1 Authentication (incl. meticulous)
2.3.0 - Encapsulation Functions
2.3.1 - IPv4 encapsulation
2.3.1 - IPv6 encapsulation
2.4.0 - Timer interval range
2.4.1 - Support \&gt;= 100ms Tx interval
2.5.0 - Client Protocols
2.5.1 - Operate with BGP. BGP uses BFD to inform on the state of the forwarding path to a neighbor
2.5.2 - When BGP Graceful Restart is enabled, helper mode is triggered when a BFD session with a neighbor goes down.

3.0 - Supported on loopback interfaces
3.1 - Supported on Port-based routing interfaces
3.2 - Supported on VLAN-based routing interfaces
3.3 - In the case of a VLAN-based interface, physical path selection is consistent with VLAN forwarding - no special measures are taken to steer to a given physical path
3.4 - In the case of a LAG on the physical path, BFD runs at the LAG-level and not at the member port level

4.0 - BFD is enabled on a per-interface basis
4.1 - BFD session parameters are managed per session
4.2 - Session Tx intervals are configurable
4.3 - Configure operational mode (Async, Demand, Echo)
4.4 - A session may be administratively set to down
4.5 - Detect Multiplier is Confgurable
4.6 - Associate a BFD session to a BGP neighbor relationship

5.0 - UI show command are provided to show session states
5.1 - Per-session statistics are provided on packet Tx, Rx and stats change events
5.2 - All session down events are logged

6.0 - BFD is supported on all SONiC platforms

7.0 - BFD is not supported on the out-of-band management port
7.1 - Multi-hop BFD is not required
7.2 - Password session Authentication is not supported

