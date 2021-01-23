# The NTP Restart Avoidance Design
# High Level Design Document
### Rev 0.1

# Table of Contents

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 11/22/2020  | Arun Barboza       | Initial draft                     |
| 0.2 | 11/25/2020  | Arun Barboza       | Internal Review(trusted key)      |

# About this Manual
This document provides general information about the design of NTP Restart avoidance in SONiC.

# Scope
This document only describes the high level design of avoiding restarts of the NTP daemon in SONiC for on-the-fly configuration changes. Other components of NTP configuration will not be described. For those other components, the reader is referred to the HLD for [NTP support in Management Framework](https://github.com/project-arlo/SONiC/pull/69)

# Introduction
NTP (Network Time Protocol) is used to synchronize the clocks between devices on IP networks. SONiC uses the Open Source NTP daemon (ntpd) originated from [ntp.org](http://www.ntp.org) and built from a [Debian source package](https://packages.debian.org/source/stretch/ntp).

Unlike many other Unix/Linux daemons (Eg: sshd, snmpd), ntpd does not handle the SIGHUP signal for reconfiguration. Currently, the NTP daemon ntpd is restarted on every NTP configuration change.

The implementatation design described here aims to avoid most of the restarts upon NTP configuration change requests originated from NBI (North Bound Interfaces), and DHCP (Dynamic Host Configuration Protocol).

# Overview

## Current NTP Configuration Flow

The following diagram describes a top level overview of the *current* SONiC NTP configuration infrastructure.

```

                      .                                                  |
                      .                                                  |
       mgmt-framework .                                                  |
         container    .                                                  |
       ************** .                                                  |
                      .                                                  |
 +-------+            .   +-------+  Start              SONiC Host       |
 | KLISH |            .   | Click |--Reconfig---+       **********       |
 +-------+            .   +-------+  Stop       |                        |
     |                .       | |               |                        |
     |                .       | |               |                        |
     |                .       | |               |                        |
     |                .       | show ntp        |                        |
     |                .       | |               |                        |
     V                .       | |               |                        |
 +------+ +------+    .       | |               V                        | Srv1
 | REST | | gNMI |    .       | |           ntp-config                   | ...
 +------+ +------+    .       | |       +---------------+                | SrvN
    | |     | |       .       | |       |               |       +==========>
    | |     | | show  .       | |      n|               |udp    |        |
    | +-----+-(--ntp-/-Get----(-+----> t|     ntpd      |123    |        |
    |         | ascn. .       |        p|               |<======+        |
    |         |       .       |        q|               |       |        |
    |translib |       .       |         |               |       +==========>
    |  xfmr   |       .       |         +---------------+                | Clnt1
    |DBLyr CVL|       .       |           ntp|ntp-config                 | ...
    +---------+       .       |            ^     ^     ^                 | ClntN
         |            .       |            |     |     |                 |
        Set           .       |            |     |     |                 |
         |            .       |            |     |     |                 |
 . . . . | . . . . . ..       |            |     |     |                 |
         |            .       |            |     |     |                 |
         | database   .       |         Start    |     |                 |
         | container  .       |        Reconfig  |     |                 |
         | *********  .       |          Stop    |     |                 |
         V            .       |            |  Start    |                 |
     +-------+        .       |            | Reconfig  |                 |
     | redis |<---------Set---+            |   Stop    |  Start          |
     +-------+        .                    |     +--+  +---------+       |
         |            .                    |        |      Stop  |       |
         |            .                    |        |            |       |
         |            .                    |        |            |       |
         |            .             +----------+ +----------+ +--------+ |
         +-----------------Get/---->| hostcfgd | | systemd  | |dhclient| |
                      . Subscribe   +----------+ +----------+ +--------+ |
                      .                                                  |
                      .                                                  |
                      .                                                  |

```



## Proposed NTP Configuration Flow

The following diagram describes a top level overview of the *proposed* SONiC NTP configuration infrastructure.

```
                      .                                                  |
                      .                                                  |
       mgmt-framework .                                                  |
         container    .                                                  |
       ************** .                                                  |
                      .                                                  |
 +-------+            .   +-------+                     SONiC Host       |
 | KLISH |            .   | Click |------+              **********       |
 +-------+            .   +-------+      |                               |
     |                .       | |      Start                             |
     |                .       | |     Reconfig                           |
     |                .       | show   Stop                              |
     |                .       | ntp      |                               |
     |                .       | |        |                               |
     |                .   +---(-+--------(---------------------+         |
     V                .   |   |          |                     |         |
 +------+ +------+    .   |   |          V                     V         | Srv1
 | REST | | gNMI |    .   |   |         ntp              n    ntpq       | ...
 +------+ +------+    .   |   |    +----------+          t  +------+     | SrvN
    | |     | |       .   |   |    |          |  Start   p  |      |   +===>
    | |     | | show  .   |   |    | ntpconfd | Reconfig - n|      |udp| |
    | +-----+-(--ntp/-Get-+   |    |          |--------->d t| ntpd |123| |
    |         | ascn. .       |    |          |  Stop    a p|      |<==+ |
    |         |       .       |    |          |          e q|      |   | |
    |translib |       .       |    |          |          m  |      |   +===>
    |  xfmr   |       .       |    +----------+          o  +------+     | Clnt1
    |DBLyr CVL|       .       |        ntp               n               | ...
    +---------+       .       |         ^                                | ClntN
         |            .       |         |                                |
        Set           .       |         |                                |
         |            .       |      +--+--+-----+                       |
 . . . . | . . . . . ..       |      |     |     |                       |
         |            .       |      |     |     |                       |
         | database   .       |   Start    |     |                       |
         | container  .       |  Reconfig  |     |                       |
         | *********  .       |    Stop    |     |                       |
         V            .       |      |  Start    |                       |
     +-------+        .       |      |   Stop    |                       |
     | redis |<---------Set---+      |     |     |  Start                |
     +-------+        .              |     +--+  +---------+             |
         |            .              |        |      Stop  |             |
         |            .              |        |            |             |
         |            .              |        |            |             |
         |            .       +----------+ +----------+ +--------+       |
         +------Get/--------->| hostcfgd | | systemd  | |dhclient|       |
             Subscribe.       +----------+ +----------+ +--------+       |
                      .                                                  |
                      .                                                  |
                      .                                                  |

```

# Design

Unlike many other Unix/Linux daemons (Eg: sshd, snmpd), ntpd does not handle the SIGHUP signal for reconfiguration. Per mailing lists, ntpd implementors disfavor adding signal handling for reconfiguration. Instead, they recommend [Runtime Reconfiguration](http://lists.ntp.org/pipermail/questions/2014-April/037821.html) using the ":config" directive. We propose to take this recommended approach.

# ntpconfd

An ntpd (re)configuration daemon will be implemented -- ntpconfd. This daemon will front-end all configuration, and state changes requested of NTP and configure the ntpd using mode 6 control packet.

The following tasks will be handled:
- (Re)Start and Stop the ntpd daemon.
- Reconfigure the ntpd daemon upon receiving SIGHUP.
- Save the backup configuration for the ntpd daemon.

The following tasks continue to be handled by ntpd directly.
- Core NTP functionality (Association Management, Clock discipline, ...)
- Status query (show ntp)

## ntpq

Currently, the NBIs use the **ntpq** program to query ntpd about association status. This usage will be extended to runtime reconfigure ntpd.

### controlkey

To prevent un-authorized users from reconfiguring ntpd, a controlkey will be added to the initial configuration of ntpd. This will be in addition to any keys configured by the SONiC device administrator for the NTP authentication feature. The total number of keys allowed, per the ntpd documentation, is 65535. Reserving 1 entry for controlkey is not expected to be an issue.

The ntpd daemon does not require controlkey to be used for status queries. It requires controlkey authenticated requests for configuration requests.

## Config File Diff

The NBIs and DHCP directly generate the ntpd configuration files (ntp.conf, ntp.keys). These files will be cached, and diffed to generate the runtime reconfiguration commands that are to sent to ntpd. A new ntp.conf file is generated for ntpd, in case it is restarted (or restarts).

ntp.keys file is privileged information, and should be handled with similar diligence as the ntpd daemon.

## Avoid Restart

The following runtime reconfigurations, when generated by a qualified program, will be handled without a restart of ntpd. Qualified programs are hostcfgd, and dhclient.

### server, unpeer

### listen, ignore

### disable auth, enable auth

### keys

Re-reads the keys file.

### trustedkey

If the only trustedkey changes are additive, i.e. additional keys are being trusted, then it may be possible to handle without a restart.


## Require Restart

Following reconfigurations will require a ntpd restart.

### trustedkey

ntpd does not support an inverse (untrustkey) for the trustedkey command.
However, if the only trustedkey changes are additive, i.e. additional keys are being trusted, then restart may not be required.

### vrf

Unrestricted changing of the vrf is not supported by the kernel.

### Non-qualified changes

Changes made by manually editing the file will be attempted as best effort. Thus, it is likely that some commands, which are otherwise handled without a restart when sent from a qualified program, may result in a restart of the ntpd.


## ntp-daemon service

The new service ntp-daemon service will be created which will help with starting and stopping the ntpd daemon. It will be used by the ntpconfd daemon only. systemd will **not** start this service at startup.


## Syslogs

syslogs will be generated by the ntpconfd daemon for the following events:
- ntpconfd Starting, Stopping.
- (Re)Starting, Stopping ntpd daemon.
- Reconfiguring ntpd daemon.
- Error conditions (Eg: detectable erroneous configurations)

## Debug

Suitable debug logs, and data will be captured for the following
- Original ntp.conf.
- New ntp.conf.
- Detected diff of ntp.conf.
- Runtime reconfiguration commands.

## Memory

Each dynamic configuration causes a new configuration tree to be stored in
ntpd. It may be necessary to restart ntpd if there are an excessive number of reconfigurations (~1000).


# Known Issues Limitations

- certain reconfigurations cause ntpd restarts.
- excessive reconfigurations cause ntpd restarts.
- non-qualified (Eg: manually editing the files) reconfigurations cause ntpd restarts.


