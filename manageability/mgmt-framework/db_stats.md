# Feature Name
Management Framework DB Layer Statistics
# High Level Design Document
#### Rev 0.1

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 02/18/2022  |   Arun Barboza     | Initial version                   |
| 0.2 | 03/14/2022  |   Sachin Holla     | Telemetry server stats            |

# Scope
This document describes the high level design of obtaining the [Management Framework](#management-framework) DB layer statistics.

# Feature Overview

The [DB Access Layer](#dbal) is a nested go package of the [Translib](#translib) that is used to access table (and maps) data stored in [redis](#redis). Translib is a library used within the dockers implementing [GNMI](#GNMI) and RESTConf interfaces  of SONiC. A need arose to instrument the DB Access Layer for visibility into its usage by the Management Framework, transformer, and other users for debugging or optimization.

The configuration for per-connection read cache (i.e. a cache of DB data that is read from the redis database for just the life of the DB Layer connection (NewDB through DeleteDB)), is also described in the schema.

# Requirements


The instrumentation should allow a user of the API to obtain:
- number of times important APIs in the DB Access Layer have been called.
- time spent in those APIs, if configured to do so.
- table/map level granularity, if configured to do so.
- important cache usage statistics.

The instrumentation is primarily intended for internal engineering usage. 

It is initially intended for obtaining Get, or Read side usage statistics.

To start, or stop, collecting time, or table/map level instrumentation, a restart should be avoided.

It should be easy to obtain, or clear the collected statistics via a programmatic interface.

# Design

Counter statistics of the APIs are always collected.
If configured, time statistics, and table/map level statistics are collected.

## DB Statistics Schema

A new table "TRANSLIB_DB" is introduced in ConfigDB. It is not expected to be used in production, and is only intended for internal engineer or developer usage.

    key         = TRANSLIB_DB|default   ; DB Layer of Translib configurations.

    ;field      = value

    time_stats  = True|False            ; Collect time statistics, if True.
                                        ; (Total Time, Peak Time).
                                        ; Default: False

    table_stats = True|False            ; Collect table level stats, if True.
                                        ; (Total Time, Peak Time).
                                        ; Default: False

    map_stats   = True|False            ; Collect map level stats, if True.
                                        ; (Total Time, Peak Time).
                                        ; Default: False

    per_connection_cache = True|False   ; Per-connection cache is enabled

    global_cache         = True|False   ; Not implemented

    tables_cache       = table(,table)* ; List of tables to cache. 
                                        ; Empty/Absent list means cache all,
                                        ; unless @no_tables_cache is set to
                                        ; "all"

    no_tables_cache    = table(,table)* ; List of tables not to cache.

    maps_cache         = map(,map)*     ; List of maps to cache. 
                                        ; Empty/Absent list means cache all,
                                        ; unless @no_maps_cache is set to "all"

    no_maps_cache      = map(,map)*     ; List of tables not to cache.


## Collection and Viewing Procedure

- Set the fields in the CONFIG_DB as per the [DB Statistics Schema](#db-statistics-schema)
- Send SIGUSR2 (or the re-configure signal) to the rest_server to reconfigure
- Reconfiguration, with a detectable change, clears the stats.
- Perform the operations which need to have their DB usage instrumented.
- Statistics from REST server are viewable using the GET method on https://ipaddress/debug/stats.
- Statistics are cleared using the DELETE method.
- Statistics from Telemetry server can be managed using `GetStats` and `ClearStats` gRPC APIs from the "Debug" gNOI service

# Examples

## Collecting Time Stats

In CONFIG_DB: TRANSLIB_DB|default time_stats=True

Note: Some of the units below may be in micro, or nano seconds. But cut-and-paste may have invalidated the encodings

```text

()@: ~ $ curl -k https://127.0.0.1:9090/debug/stats
API TYPE     TIMER TYPE   NUM HITS   TOTAL TIME   PEAK
============ ============ ========== ============ ============
REST APIs    Server       170        2.985605323s 513.400078ms
             Handler      170        2.945663649s 513.30643ms
             Auth         0          0s           0s
             Translib     169        2.825377411s 513.067391ms
             CVL          0          0s           0s
             DB           244        0s           0s
Service APIs Server       1          137.518��s    137.518��s

DB APIs Globals: New: 1530 Delete: 1522 PeakNew: 17 ZeroGetHits 1351

 Databases:
 2:
 Table Name:AllTables
           GetEntry GetKeys GetKeysPattern GetMap GetMapAll Total
           -------- ------- -------------- ------ --------- -----
 Hits      2        1       1              0      0         3
 CacheHits 0        0       0              0      0
 Time      0s       0s      0s             0s     0s 0s
 Peak      0s       0s      0s             0s     0s 0s

 4:
 Table Name:AllTables
           GetEntry GetKeys GetKeysPattern GetMap GetMapAll Total
           -------- ------- -------------- ------ --------- -----
 Hits      80       160     160            0      0         240
 CacheHits 15       5       5              0      0
 Time      0s       0s      0s             0s     0s 0s
 Peak      0s       0s      0s             0s     0s 0s

 6:
 Table Name:AllTables
           GetEntry GetKeys GetKeysPattern GetMap GetMapAll Total
           -------- ------- -------------- ------ --------- -----
 Hits      0        1       1              0      0         1
 CacheHits 0        0       0              0      0
 Time      0s       0s      0s             0s     0s 0s
 Peak      0s       0s      0s             0s     0s 0s

()@: ~ $

```

## Collecting Table and Time Stats

In CONFIG_DB: TRANSLIB_DB|default time_stats=True, and table_stats=True

Note: the cache hits have been simulated.

```text

()@: ~ $ curl -k https://127.0.0.1:9090/debug/stats
API TYPE     TIMER TYPE   NUM HITS   TOTAL TIME   PEAK
============ ============ ========== ============ ============
REST APIs    Server       9          221.326895ms 60.235536ms
             Handler      9          220.141982ms 60.094728ms
             Auth         0          0s           0s
             Translib     9          217.96371ms  59.823535ms
             CVL          0          0s           0s
             DB           99         9.662559ms   623.41��s
Service APIs Server       5          2.21327ms    712.295��s

DB APIs Globals: New: 81 Delete: 81 PeakNew: 9 ZeroGetHits 69

 Databases:
 2:
 Table Name:RADIUS_SERVER_STATS
           GetEntry  GetKeys    GetKeysPattern GetMap GetMapAll Total
           --------  -------    -------------- ------ --------- -----
 Hits      18        9          9              0      0         27
 CacheHits 8         4          4              0      0
 Time      873.797��s 1.616516ms 1.616516ms     0s     0s 2.490313ms
 Peak      142.141��s 514.06��s   514.06��s       0s     0s 514.06��s

 4:
 Table Name:RADIUS_SERVER
           GetEntry  GetKeys   GetKeysPattern GetMap GetMapAll Total
           --------  -------   -------------- ------ --------- -----
 Hits      18        9         9              0      0         27
 CacheHits 8         4         4              0      0
 Time      902.556��s 804.151��s 804.151��s      0s     0s 1.706707ms
 Peak      99.049��s  317.916��s 317.916��s      0s     0s 317.916��s

 Table Name:RADIUS
           GetEntry  GetKeys GetKeysPattern GetMap GetMapAll Total
           --------  ------- -------------- ------ --------- -----
 Hits      27        0       0              0      0         27
 CacheHits 12        0       0              0      0
 Time      4.39008ms 0s      0s             0s     0s 4.39008ms
 Peak      623.41��s  0s      0s             0s     0s 623.41��s

 Table Name:LDAP_SERVER
           GetEntry  GetKeys   GetKeysPattern GetMap GetMapAll Total
           --------  -------   -------------- ------ --------- -----
 Hits      9         9         9              0      0         18
 CacheHits 4         4         4              0      0
 Time      395.091��s 680.368��s 680.368��s      0s     0s 1.075459ms
 Peak      108.309��s 214.24��s  214.24��s       0s     0s 214.24��s

()@: ~ $

```

## Collecting Statistics from Telemetry Server

`gnoi_client` program from the telemetry container can be used for dumping the statistics on any sonic switch.
This program will also available on the build servers at `sonic-telemetry/build/bin` directory.
Output will be a JSON value as shown below.

```text
~ # docker exec telemetry gnoi_client -insecure -username admin -password YourPaSsWoRd -module Debug -rpc GetStats
Unable to connect to syslogD
{
  "cvl-stats": {
    "Hits": 0,
    "Time": 0,
    "Peak": 0
  },
  "db-stats": {
    "new-db": 24,
    "delete-db": 13,
    "peak-open": 20,
    "new-time": 25260500,
    "peak-new-time": 13473300,
    "zero-get-ops-db": 6,
    "dbs": [
      {
        "name": "APPL_DB",
        "all-tables": {
          "hits": 582,
          "total-time": 204580500,
          "peak-time": 1316600,
          "get-entry-hits": 372,
          "get-keys-hits": 106,
          "get-keys-pattern-hits": 210,
          "get-entry-time": 128736800,
          "get-keys-time": 39683300,
          "get-keys-pattern-time": 75843700,
          "get-entry-peak-time": 1316600,
          "get-keys-peak-time": 1257000,
          "get-keys-pattern-peak-time": 1257000
        },
        "all-maps": {}
      },
      {
        "name": "COUNTERS_DB",
        "all-tables": {
          "hits": 322,
          "total-time": 98935000,
          "peak-time": 9217000,
          "get-entry-hits": 322,
          "get-entry-time": 98935000,
          "get-entry-peak-time": 9217000
        },
        "all-maps": {
          "hits": 206,
          "total-time": 103472600,
          "peak-time": 1912100,
          "get-map-all-hits": 206,
          "get-map-all-time": 103472600,
          "get-map-all-peak-time": 1912100
        }
      },
      {
        "name": "CONFIG_DB",
        "all-tables": {
          "hits": 909,
          "total-time": 336121300,
          "peak-time": 12203100,
          "get-entry-hits": 589,
          "get-keys-hits": 216,
          "get-keys-pattern-hits": 320,
          "get-entry-time": 203859100,
          "get-keys-time": 84894200,
          "get-keys-pattern-time": 132262200,
          "get-entry-peak-time": 12203100,
          "get-keys-peak-time": 1191200,
          "get-keys-pattern-peak-time": 5866800
        },
        "all-maps": {}
      },
      {
        "name": "STATE_DB",
        "all-tables": {
          "hits": 106,
          "total-time": 35465800,
          "peak-time": 1170500,
          "get-entry-hits": 103,
          "get-keys-hits": 3,
          "get-keys-pattern-hits": 3,
          "get-entry-time": 33627900,
          "get-keys-time": 1837900,
          "get-keys-pattern-time": 1837900,
          "get-entry-peak-time": 1170500,
          "get-keys-peak-time": 840600,
          "get-keys-pattern-peak-time": 840600
        },
        "all-maps": {}
      }
    ]
  }
}
```

Note: Time stats and table stats can be enabled similar to the REST server examples shown in previous sections.

# Limitations

## Only Read side instrumented

Instrumented only for select Read|Get side DB Access Layer API

## Performance

Time Statistics, or Table Statistics collection may have performance impact.
After collection, the engineer/developer will need to delete the TRANSLIB_DB|default key (or the fields of that key controlling the DB Access Layer API statistics collection). It is not desirable to have these configurations saved in the startup CONFIG_DB.json file.

## Interpretation of zero in time columns

When time statistics are not being collected, the corresponding rows may have 0s in the column. This can be co-related with the number of Hits, to distinguish it from a case where no time was spent in the corresponding DB Layer API call.  For example, if the number of Hits were non-zero, but the time columns were 0, then the inference would be that time statistics were not being collected. An effort is underway to make this distinction more apparent.

## Interaction with OnChange cache

The OnChange cache, which is used for supporting gNMI Telemetry OnChange subscriptions, shares some of the datastructures with the per-connection cache. The reported statistics do not make this distinction.

# References

## Management Framework

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md

## Translib

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md#3226-Translib

## GNMI

https://github.com/openconfig/reference/blob/master/rpc/gnmi/gnmi-specification.md

## DBAL

https://github.com/Azure/SONiC/blob/master/doc/mgmt/Management%20Framework.md#32265-db-access-layer

## Redis

https://redis.io/topics/introduction

