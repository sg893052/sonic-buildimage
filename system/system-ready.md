# System Ready

System Ready

# High Level Design Document

#### Rev 0.1

# Table of Contents
 * [List of Tables](#list-of-tables)
 * [Revision](#revision)
 * [About This Manual](#about-this-manual)
 * [Scope](#scope)
 * [Definition/Abbreviation](#definitionabbreviation)
 * [Feature Overview](#FeatureOverview)
 * [Requirements](#Requirements)
 * [Design](#Design)
 * [Unit Test](#UnitTest)


# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |   Date  |    Author    | Change Description        |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 04/26/2021 |  Senthil Kumar Guruswamy    | Initial version          |
| 0.2 | 05/24/2021 |  Senthil Kumar Guruswamy    | Full HLD update          | 
| 0.3 | 07/02/2021 |  Senthil Kumar Guruswamy    | Update		            | 

# About this Manual

This document provides general information about System ready in SONiC.

# Scope

This document describes the high-level design of the System ready in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**             |
|--------------------------|-------------------------------------|
FEATURE                        Docker/Service
App                            Docker/Service

# 1 Feature Overview

SONiC is an open-source network operating system based on Debian Linux, integrated with various systemd services. 
At present, system ready is based on only core docker services(swss ,bgp,teamd,pmon,syncd,database,mgmt-framework) and the portinitdone status.

In this HLD, the system ready will be enhanced to consider the overall active systemd services consisting of
1. All docker services instead of just a few core docker services.
2. All host services.
3. The portready status along with portinit.

With the asynchronous architecture of SONiC, we will not be able to verify if the config has been applied all the way down to the HW. We will add provisions for applications to notify its closest UP status.
Hence the system ready will NOT indicate the complete system ready but indicates the closest up status.

The existing "show system status" click command will be deprecated and replaced with "show system status core". This behavior will be similar to what exists at present.
New click command "show system status all" will be introduced for knowing the overall system status which will be helpful in debugging and can be integrated to techsupport dump.

## 1.1 Requirements

### 1.1.1 Functional Requirements
System Readiness:
- Identify the host systemd services 
- Sysmonitor to check system status of all the service units from the above tables[FEATURE,HOST_SERVICE] & the Port ready status and then show and post 'FULL SYSTEM READY' status in the STATE_DB.
  In parallel, need to explore if monit can offload these activities from sysmonitor.
  Service status Details:
    - Check only the status of "enabled" services(host+docker).
    - Docker Services:
        - Read the closest UP status from each docker apps.
        - In addition to checking the dockers, check for all the processes(critical) inside the docker containers.
        - Also, Enforce all docker owners to implement supevisord proc_exit_listener to exit docker on its critical process exit.
        - Multiple instances of a process inside docker cannot be monitored from Host. However, will consider monitoring all redis-server instances of database docker.
    - List down the failed services and its status.

  Port Ready status Details:
    - As part of show system status, today we consider portInitDone message from SWSS. But this is only the initial port creation in DB, and no way reflects the actual port status. This is the portInitDone from PORTTABLE. However there is one more portInitDone updated by portsyncd in the state DB by portsyncd. This is updated when host interfaces are created in the kernel. We plan to use this one.
- New Click CLI
    - "show system status core" will replace the existing "show system status" CLI.
    - "show system status all" to be introduced to cover the overall system status.
    - This CLI to display the existing core system status as well as the overall system status with the failed services' systemctl status.
    - During the techsupport data collection, the new click CLI to be included for debugging.


### 1.1.2 Configuration and Management Requirements
- Click CLI to be introduced to show the overall system status.
    "show system status all"
- Exisiting CLI "show system status" will be replaced with "show system status core"

### 1.1.3 Scalability Requirements
- NA

### 1.1.4 Warm/fast/cold Boot Requirements
- "show system status all" and "show system status core" should work post warm-reboot.
 
# 2 Design
## 2.1 Overview
- Enhancement of System Ready to consider all active systemd services' running status along with its closest UP status and Port ready status.

## 2.2 Current Model
- The existing model considers only the core docker services' running status and the PortInitDone status from APPL_DB.

## 2.3 Feature Details
- System ready is based on the following components.
  - 1. SONiC Docker services
  - 2. SONiC Host services
  - 3. Port ready status
  - 4. Existing core system status.
  - 5. Sysmonitor


### 2.3.1 SONiC Docker services:
- Enhance the system ready by considering the running status of "all" the docker apps along with its "closest UP status".
- For this, a new FEATURE table with UP_STATUS key for each docker apps should be created/maintained in redis STATE_DB. 
- Please do not confuse with the already existing similar FEATURE table in CONFIG_DB which indicates if a docker app is enabled or disabled.
- As part of docker launch, each docker app should set the UP_STATUS to False and expect each docker app to set it to True as depicted below after it completes initialization.


#### 2.3.1.1 Categorization of docker apps in marking UP_STATUS: 
##### 2.3.1.1.1 Apps to notify its closest UP status:
       - Docker apps which rely on config, can mark UP_STATUS to True in STATE_DB  when they are ready to receive configs from CONFIG_DB and/or some extra dependencies are met.
         
       - sonic-db-cli STATE_DB HSET "FEATURE|<dockername>" UP_STATUS True

       - Schema in STATE_DB
          sonic-db-dump -n STATE_DB output

          "FEATURE|<dockername>": {
            "type": "hash",
            "value": {
              "UP_STATUS": "True"
            }
           },
        - Check example section for clarity.

       - For instance, 
         - swss docker app is considered to be UP only when its systemd service is in running state and when its dependencies are met by checking state-db entries populated by *mgrs, like intfmgr, vrfmgr, vxlanmgr.
         - At present, sysmonitor polls the status but now the ask for the respective docker apps is to update the STATE_DB when they think that it is the closest UP status of their app.

         - On similar lines, bgp also does wait for swss-service state-db entries and along with fpmsyncd running status.

         - Other apps like udld,stp etc once after waiting for PortInitDone status and interface db creation, they could mark their UP status.

         - Database app which is the first/base app to be up, may set the UP_STATUS to True once all the required number of redis-server instances are in running state.

       - Apps which notify the UP status, should have a new entry "check_up_status" set to True in /etc/sonic/init_cfg.json file as below.
         - Schema in /etc/sonic/init_cfg.json
            {
              "FEATURE": {
                 "<dockername>": {
                     ...
                     "state": "enabled",
                     "check_up_status": "True"
                 }
              }
            }

          - Check example section for clarity.
          - This json file will be fed to FEATURE table of CONFIG_DB during bootup.
          - This json file will be factory default and no config command will be provided for "check_up_status" entry to be updated in CONFIG_DB later.

          

##### 2.3.1.1.2 Apps which dont notify UP status:
       - A few docker apps may opt out of this, declaring only their systemd running status is sufficient to consider it to be UP.
       - This information will be maintained in init_cfg.json file's new entry "check_up_status" against each docker app.
       - App which does not notify UP status, should have the entry "check_up_status" set to False as below in init_cfg.json file.
       - We can treat it as False even if this entry is not present.
       - /etc/sonic/init_cfg.json
          {
             "FEATURE": {
                 "<dockername>": {
                     ...
                     "state": "enabled",
                     "check_up_status": "False"
                 },
             }
             ...
          }

#### 2.3.1.2 Monitoring Strategy
- The running status of docker apps will be checked only if they are "enabled" or "always_enabled" in FEATURE table of CONFIG_DB.

- Further, the UP_STATUS in STATE_DB will only be checked for those dockers which are in running state and also only when "check_up_status" value is set to True in CONFIG_DB.

- Also, as it is essential for all the ciritical processes in a docker to be running, supervisord's proc-exit-listener must be mandated except for the below exception.
  Exception: Dockers which has process restart implemented through supervisord's autorestart property is sufficient and need not exit docker on killing its process.
  Example, mgmt-framework docker has process restart implemented for its rest-server process.

#### 2.3.1.3 Example:
  - Apps Marking UP_STATUS in STATE_DB'S FEATURE table:
    - sonic-db-cli STATE_DB HSET "FEATURE|udld" "UP_STATUS" "True"
      1

    - sonic-db-cli STATE_DB HGET "FEATURE|udld" "UP_STATUS"
      True

    - sonic-db-dump -n STATE_DB -y

       "FEATURE|udld": {
         "type": "hash",
        "value": {
          "UP_STATUS": "True"
        }
       },


  - Adding new entry to init_cfg.json file:
     - Add the "check_up_status" key and value "False/True" against each docker app entry in init_cfg.json

       - /etc/sonic/init_cfg.json
          {
             "FEATURE": {
                 "udld": {
                     ...
                     "state": "enabled",
                     "check_up_status": "True"
                 },
                 "bgp": {
                     ...
                     "state": "always_enabled",
                     "check_up_status": "True"
                 },
                 "sflow": {
                     ...
                     "state": "enabled",
                     "check_up_status": "False"
                }
             }
             ...
          }

  
### 2.3.2 SONiC Host services:
- Host services that are in loaded and active state as explained in Section (2.3.5) will be monitored for its running status.


### 2.3.3 PortInitDone Status:
- Respective services should mark its UP_STATUS considering Port ready status
- Hence there is no separate logic check needed by system monitoring tool.


### 2.3.4 Core system status:
- Core system status's CLI will be deprecated and replaced with "show system status core" click CLI and output will remain same as in present.


### 2.3.5 Sysmonitor:
- Sysmonitor thread to montior all the Sonic Service(Docker+Host) states(present in /etc/systemd/system) and process their running status and Up status.
- Loaded, enabled/enable-runtime/static , active & running, active & exited state services are considered UP.
- Inactive and failed state services are considered DOWN.
- For all docker services, app ready status is read from state db feature table UP_STATUS post checking the check_up_status flag in config db.
- show system status all command will use socket communication with sysmonitor to get the current status of all the services.

## 2.4 KLISH/CLICK Commands
- Only Click commands are supported and not KLISH commands as KLISH will not work if Mgmt-framework service is down.
    - show system status core
    - show system status all
       Options: --brief/--detail
        - show system status all --brief
        - show system status all --detail

### 2.4.1 show system status core
- The output of this command will be same as the "show system status" command output.
- "show system status" command will be deprecated.

### 2.4.2 show system status all
- All Sonic Services will be monitored for its running and Up status.
- There is no separate logic to get Port status as it is covered as part of relevant services marking up status.
- Output format of the CLI :
    
    <"System is ready with all the services"/"System is not ready - one or more service have Failed">
    Service-Name        Service-Status       App-Ready-Status   Fail-Reason
     <service1>         OK                   OK                 -
     <service2>         Failed               Failed             start-limit-hit
     <service3>         OK                   Failed             -


    Example 1:
    root@sonic:/# show system status all
    System is ready with all the services

    Service-Name                   Service-Status       App-Ready-Status     Fail-Reason
    as7712-pddf-platform-monitor   OK                   OK                   -
    bgp                            OK                   OK                   -
    caclmgrd                       OK                   OK                   -
    config-setup                   OK                   OK                   -
    containerd                     OK                   OK                   -

    Example 2:
    root@sonic:/# show system status all
    System is not ready - one or more service have Failed

    Service-Name                   Service-Status       App-Ready-Status     Fail-Reason
    aaastatsd                      Failed               Failed               start-limit-hit
    as7712-pddf-platform-monitor   OK                   OK                   -
    bgp                            OK                   OK                   -
    caclmgrd                       OK                   OK                   -
    config-setup                   OK                   OK                   -


### 2.4.3 show system status all --brief
- The output of this command will just display the brief status of the entire sonic services.
- Output format of the CLI :
    
    <"System is ready with all the services"/"System is not ready - one or more service have Failed">

    Example 1:
    root@sonic:/# show system status all --brief
    System is not ready - one or more service have Failed

    root@sonic:/#



### 2.4.4 show system status all --detail
- This command would display systemctl status output of failed services along with "all" option output.

- Output format of the CLI :

    <"System is ready with all the services"/"System is not ready - one or more service have Failed">
    Service-Name        Service-Status       App-Ready-Status   Fail-Reason
     <service1>         OK                   OK                 -
     <service2>         Failed               Failed             start-limit-hit
     <service3>         OK                   Failed             -

    ● system-health1.service - SONiC system health monitor
      Loaded: loaded (/lib/systemd/system/system-health.service; enabled-runtime;
      Active: failed (Result: start-limit-hit) since Thu 2019-02-14 11:21:09 UTC;
      Main PID: 4764 (code=exited, status=0/SUCCESS)
    
    ● system-health2.service - SONiC system health monitor
      Loaded: loaded (/lib/systemd/system/system-health.service; enabled-runtime;
      Active: failed (Result: start-limit-hit) since Thu 2019-02-14 11:21:09 UTC;
      Main PID: 4765 (code=exited, status=0/SUCCESS)

    Example:
    root@sonic:/# show system status all --detail
    System is not ready - one or more service have Failed

    Service-Name                   Service-Status       App-Ready-Status     Fail-Reason
    as7712-pddf-platform-monitor   OK                   OK                   -
    bgp                            OK                   OK                   -
    caclmgrd                       OK                   OK                   -
    ...
    telemetry                      OK                   OK                   -
    tpcm                           OK                   OK                   -
    udld                           Failed               Failed               exit-code
    updategraph                    OK                   OK                   -
    vrrp                           OK                   OK                   -

    ● udld.service - UDLD container
       Loaded: loaded (/usr/lib/systemd/system/udld.service; enabled; vendor preset: enabled)
       Active: failed (Result: exit-code) since Fri 2021-07-02 11:53:09 UTC; 2h 8min ago
      Process: 1827 ExecStartPre=/usr/bin/udld.sh start (code=exited, status=255)
          CPU: 161ms



## 2.5 Syslogs
- Syslog to be generated for any Sonic systemd services that changes event (went down/up).
- This can be handled in sysmonitor.
- At present, a dedicated thread in sysmonitor runs to listen for any service state change events. 
  Upon receiving any state change events, the entire core system services are checked for its status.
- This needs to be enhanced to identify the particular event service that caused the change and raise a syslog for that service.
- Also, syslog is generated for "System is ready with all the services" and  "System is not ready - One or more services have Failed" scenario, only when there is a change between the two states.

Example 1:
root@sonic:/# show in-memory-logging | grep "System is not ready - one or more service have Failed"
Jul 02 11:53:51.020133 2021 sonic INFO system#monitor: System is not ready - one or more service have Failed

Example 2:
Jul 02 17:04:26.830540 2021 sonic INFO system#monitor: in-memory.service service state changed to [inactive/dead]
Jul 02 17:05:21.261365 2021 sonic INFO system#monitor: in-memory.service service state changed to [active/exited]

Example 3:
Jul 02 17:01:00.454978 2021 sonic INFO system#monitor: hostcfgd.service service state changed to [inactive/dead]
Jul 02 17:01:28.725101 2021 sonic INFO system#monitor: hostcfgd.service service state changed to [active/running]

Example 4:
Jul 02 16:45:47.557377 2021 sonic INFO system#monitor: sflow.service service state changed to [inactive/dead]
Jul 02 16:59:49.720197 2021 sonic INFO system#monitor: sflow.service service state changed to [active/running]


## 2.6 Developer Guidelines
Docker applications to notify its closest UP status.
Add entry to init_cfg json file for to indicate if a docker is setting up its UP status in STATE_DB 


# 3 Unit Test
1. Check show system status all 
2. Check show system status all --brief
3. Check show system status all --detail
4. Check show system status core
5. Make any of the docker apps down and check failed apps details are shown
6. Make any of the host apps down and check failed apps details are shown
7. Check all critical processes in each docker app are in running state.
8. Kill one of the critical processes of a docker and see if the docker is exiting.


