# The NTP Restart Avoidance Design
# High Level Design Document
### Rev 0.4

# Table of Contents

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|:---------------------------------:|
| 0.1 | 11/22/2020  | Arun Barboza       | Initial draft                     |
| 0.2 | 11/25/2020  | Arun Barboza       | Internal Review(trusted key)      |
| 0.3 | 12/02/2020  | Arun Barboza       | Appendix ntpconfd design          |
| 0.4 | 12/14/2020  | Arun Barboza       | Mgmt VRF handling                 |

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

The NBIs and DHCP directly generate the ntpd configuration files (ntp.conf, ntp.keys, /run/ntp.conf.dhcp). These files will be cached, and diffed to generate the runtime reconfiguration commands that are to sent to ntpd. A new ntp.conf file is generated for ntpd, in case it is restarted (or restarts).

ntp.keys file is privileged information, and should be handled with similar diligence as the ntpd daemon.

Per existing SONiC/Linux behavior, the DHCP ntp configuration file (/run/ntp.conf.dhcp) file is given higher precedence than the /etc/ntp.conf file. The DHCP ntp configuration file is generated from /etc/ntp.conf with the NTP servers configuration lines replaced with the ones received from DHCP.

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


# Appendix ntpconfd Design

## Constants

ntpconfd_logdir = '/var/log/ntpconfd/'
saveconfig_logdir = ntpconfd_logdir + 'saveconfig/'

ntpd_confdir = '/var/run/ntpconfd/etc'
ntpd_conffile = ntpd_confdir + 'ntp.conf'
ntpd_keyfile = ntpd_confdir + 'ntp.keys'
ntpd_mgmtvrffile = ntpd_confdir + 'mgmt.vrf'

## Service File

## Libraries

### Difflib

Given 2 files, obtain a list of subtractions, and additions.

Use an existing library?

### Call ntpq binary

Send some commands to ntpq, and get a reply after each.
If any command returns error (other than "Config Succeeded", then stop)
can subprocess be used?

```

admin@S:~$ ntpq
ntpq> keytype MD5
ntpq> keyid 1
ntpq> passwd brcm
ntpq> association
No association ID's returned
ntpq>
ntpq> config-from-file /tmp/sample.conf
Sending configuration file, one line at a time.
Line No: 1 Config Succeeded: interface listen Ethernet0
Line No: 2 Config Succeeded: interface listen Ethernet1
Done sending file
ntpq>

```

```

ntpq> :config keys /etc/ntp.keys
Config Succeeded
ntpq>

```

```

ntpq> saveconfig n2.conf
Configuration saved to 'n2.conf'
ntpq>

```

** Do not use ntpq -c option because it may cause eavesdroppers to view the controlkey passwd that is passed on the command line **

```
admin@S:~$ ntpq --help
ntpq - standard NTP query program - Ver. 4.2.8p10
Usage:  ntpq [ -<flag> [<val>] | --<name>[{=| }<val>] ]... [ host ...]
  Flg Arg Option-Name    Description
   -4 no  ipv4           Force IPv4 DNS name resolution
                                - prohibits the option 'ipv6'
   -6 no  ipv6           Force IPv6 DNS name resolution
                                - prohibits the option 'ipv4'
   -c Str command        run a command and exit
                                - may appear multiple times
...
```


### Random number

What is available to use as a key? Python version. (secrets, random)

Generation of controlkey

### Logging

logging library


## Command chart handling for diffs of ntp.conf

The commands under Add column are sent via ntpq when the line is added.
The commands under Subtracted column are sent via ntpq when the line is deleted.


| Token         |    Add        | Subtract      | Change Description          |
|:-------------:|:-------------:|:-------------:|:---------------------------:|
|               |               |               |                             |
|               |               |               |                             |
| server host \ | server host \ | unpeer host   |                             |
|  ...          |  ...          |               |                             |
|               |               |               |                             |
|               |               |               |                             |
| interface \   | interface \   | interface \   |                             |
|  listen iface | listen iface  |  ignore iface |                             |
|               |               |               |                             |
|               |               |               |                             |
| enable auth   | enable auth   | disable auth  |                             |
|               |               |               |                             |
|               |               |               |                             |
| keys file     | keys file     |  See (b)      | file perm (ntp:ntp 0400)    |
|               |               |               |                             |
|               |               |               |                             |
| trustedkey \  | See (c)       |  See (c)      |                             |
|  k1 [k2 ... ] |               |               |                             |
|               |               |               |                             |
|               |               |               |                             |


Notes:

(a) vrf does not occur in the ntp.conf file, but is handled with a restart of ntp-daemon service

(b) Restart ntp-daemon to clear keys from ntpd

(c) trustedkey is incremental. Thus only the added keys are sent in a trustedkey command to ntpq. controlkey keyid needs to be added to trustedkey


## Signal Handler

### SIGHUP

### SIGTERM

### SIGUSR2

## Initialization

### Create ntpd Configuration File

```

if '/run/ntp.conf.dhcp' exists
  read the 'server' lines from '/run/ntp.conf.dhcp' as dhcp_ntp_servers
  read the '/etc/ntp.conf' file with (server|peer|pool) replaced \
    with 'server' lines replaced with dhcp_ntp_servers, as prev_conffile
else
  read the '/etc/ntp.conf' file as prev_conffile
read the '/etc/ntp.keys' file as prev_keyfile

read the ConfigDB MGMT_VRF_CONFIG["vrf_global"]["mgmtVrfEnabled"] as prev_vrf_enabled
read the ConfigDB NTP["global"]["vrf"] as prev_vrf_configured

if controlkey is being used
  controlkey_inuse = true

if keyid space is full
  keyidfull = true

if (not controlkey_inuse) and (not keyid_full)
  assign controlkey_keyid
  generate controlkey_key

write prev_keyfile with generated controlkey as ntpd_keyfile
write prev_conffile with generated controlkey as ntpd_conffile
if prev_vrf_enabled == "true" and prev_vrf_configured != "default"
  touch ntpd_mgmtvrffile
else
  rm ntpd_mgmtvrffile


```

### Start ntp-daemon service

```

service ntp-daemon start

```


## Main Loop

### Check And Handle SIGTERM

```

Stop ntp-daemon service
Exit

```

### Check And Handle SIGHUP

```

read the ConfigDB MGMT_VRF_CONFIG["vrf_global"]["mgmtVrfEnabled"] as vrf_enabled
read the ConfigDB NTP["global"]["vrf"] as vrf_configured

if '/run/ntp.conf.dhcp' exists
  read the 'server' lines from '/run/ntp.conf.dhcp' as dhcp_ntp_servers
  read the '/etc/ntp.conf' file with (server|peer|pool) replaced \
    with 'server' lines replaced with dhcp_ntp_servers, as new_conffile
else
  read the '/etc/ntp.conf' file as new_conffile
read the '/etc/ntp.keys' file as new_keyfile

if (vrf_enabled != prev_vrf_enabled) or (vrf_configured != prev_vrf_configured)
  write new_keyfile as ntpd_keyfile
  write new_conffile as ntpd_conffile
  if prev_vrf_enabled == "true" and prev_vrf_configured != "default"
    touch ntpd_mgmtvrffile
  else
    rm ntpd_mgmtvrffile
  prev_* = new_*
  ReStart ntp-daemon service (Stop, Sleep, Start)
  continue


if controlkey_inuse or keyidfull
  write new_keyfile as ntpd_keyfile
  write new_conffile as ntpd_conffile
  if prev_vrf_enabled == "true" and prev_vrf_configured != "default"
    touch ntpd_mgmtvrffile
  else
    rm ntpd_mgmtvrffile
  prev_* = new_*
  ReStart ntp-daemon service (Stop, Sleep, Start)
  continue

if controlkey is being used
  controlkey_inuse = true
  write new_keyfile as ntpd_keyfile
  write new_conffile as ntpd_conffile
  if prev_vrf_enabled == "true" and prev_vrf_configured != "default"
    touch ntpd_mgmtvrffile
  else
    rm ntpd_mgmtvrffile
  prev_* = new_*
  ReStart ntp-daemon service (Stop, Sleep, Start)
  continue


if keyid space is full
  keyidfull = true
  write new_keyfile as ntpd_keyfile
  write new_conffile as ntpd_conffile
  if prev_vrf_enabled == "true" and prev_vrf_configured != "default"
    touch ntpd_mgmtvrffile
  else
    rm ntpd_mgmtvrffile
  prev_* = new_*
  ReStart ntp-daemon service (Stop, Sleep, Start)
  continue;

assign new_controlkey_keyid
generate new_controlkey_key

write new_keyfile with generated controlkey as ntpd_keyfile
write new_conffile with generated controlkey as ntpd_conffile
if prev_vrf_enabled == "true" and prev_vrf_configured != "default"
  touch ntpd_mgmtvrffile
else
  rm ntpd_mgmtvrffile

Diff the Configuration (prev_ and new_ of keyfile, and conffile)

prev_* = new_*

if the Diff cannot be reconfigured
  ReStart ntp-daemon service (Stop, Sleep, Start)
  continue

Send the Diff via ntpq

if Failure to Send
  ReStart ntp-daemon service (Stop, Sleep, Start)
  continue

```

### Check And Handle SIGUSR2

This is optional.

```

Dump some statistics/debug to the debug log files

```

### Sleep Forever

```

if sleep was interrupted due to SIGHUP
  Throttle it if necessary

```


# Appendix Scripts Design

## ntp-daemon

service

script


## ntp

service

script

## syslog

debug log

log rotation

## techsupport dump file

debug logs

ntp status


