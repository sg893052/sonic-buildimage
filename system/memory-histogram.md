# Memory Histogram

Memory Histogram

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
| 0.1 | 04/30/2021 |  Senthil Kumar Guruswamy    | Initial version           |
| 0.2 | 06/07/2021 |  Senthil Kumar G/ Kalimuthu V  | Full HLD               |
| 0.3 | 07/31/2021 |  Senthil Kumar G/ Kalimuthu V  | Full HLD Update        |
| 0.4 | 08/09/2021 |  Senthil Kumar G   | CLI Headers update        |
| 0.5 | 10/11/2021 |  Senthil Kumar G   | Update CLI outputs        |

# About this Manual

This document provides general information about Memory Histogram in SONiC.

# Scope

This document describes the high-level design of the Memory Histogram in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**         | **Meaning**             |
|------------------|-------------------------------------|
|AI                 |  Artificial Intelligence          |
|RSS                |     Resident Set Size  |


# 1 Feature Overview

SONiC is an open-source network operating system based on Debian Linux, integrated with various applications.
Memory is one of the crucial resources which is being used across multiple applications simultaneously. SONiC applications are meant to run continuously unless the user requests to reload the system. In a long running system, if memory is being leaked by application or over used by application, it will result in system panic because of out of memory condition. It is essential to monitor the memory usage of a process periodically and analyze the usage for over a period of time.
Many open source tools do exist to collect the current memory status of processes. But we need an infrastructure to collect raw memory data in regular intervals and convert them to useful information and report it for analysing.

In this HLD, the memory histogram feature tracks the memory usage of processes, dockers and system periodically and reports history of memory usage which helps in diagnosing memory related issues. Also, memory leak detection can be identified via CLI.


## 1.1 Requirements

### 1.1.1 Functional Requirements

The memory histogram should periodically poll the memory usage at Process level, Docker level and System level at regular intervals and collect the raw data to be stored.
This memory statistics data can be viewed using appropriate show commands for user analysis.

1. Should collect memory footprints of all the processes, dockers and system periodically.
2. This feature should be enabled by default.
3. Sampling interval would be predifined as 3 minutes as fixed for this release. Support to configure this will be added in future release.
4. Should provide histogram view for a maximum of last one month(30 days). Support to configure this will be added in future release.
5. CLI option will be provided to view the histogram per process, docker & system level.
6. CLI option to view memory usage at selected time
7. AI feature will be provided to collect the potential memory leak info which should be visible in appropriate CLI output.
8. The memory footprints collected will be stored in a binary format with compression enabled.
9. Memory histogram CLIs needs to be included in techsupport collection.

### 1.1.2 Configuration and Management Requirements

### 1.1.3 Scalability Requirements
- NA

### 1.1.4 Warm/fast/cold Boot Requirements
-NA

# 2 Design
## 2.1 Overview
This section explains the design techniques involved in Memory histogram feature.

## 2.2 Current Model
The CLI "show processes memory" exists to know the current memory usage of all the processes.

## 2.3 Feature Details

### 2.3.1 Memory data collection:
    - For every "3 minutes" interval, each process RSS value is collected.
    - Also, collect docker and system level memory usage in the time interval.
    - For system, parameters like total, used, free, available, buffers, cached are all collected.
    - "psutil" linux utility will be used to collect rss data for processes and system.
    - "docker stats" will be used to collect docker memory usage data.
    - A separate service is introduced for this activity.



### 2.3.2 Storage area and data schema: 
    - Collected memory footprint will be dumped into a disk file under /var/log/histogram for a day.
    - Format of storage will be binary and file will be in compressed format to save space.
    - At the end of the day(23:59), the disk file will be rotated  by cron job and the rotation count will be 30. Hence last 30 days data is preserved.
    - Also, another file(total) exists for a day data containing the sum of all entries of the day and count info.
    - Schema of memory footprint collection:
        mem_dict = {"ctime":<current_time>, "pmem":pmem_dict, "smem":smem_dict, "dmem":dmem_dict, "count":<entry_count> }
        pmem_dict= {"process":{process1:<mem>, process2:<mem>, process3:<mem>},"count":<count>}
        smem_dict = {"system":{used:<mem>, free:<mem>, buffers:<mem>, cached:<mem>, available:<mem>},"count":<count>}
        dmem_dict = {"docker":{docker1:<mem>, docker2:<mem>, docker3:<mem>},"count":<count>} 



### 2.3.3 View collected Memory Statistics:

    - click and klish CLIs are provided to view the memory statistics.

      show histogram memory <process/docker/system> [stime <datetime> [ | [etime <datetime>]] ] [filter <filterstring>] [analyze <leak>]
    



### 2.3.4 Memory leak detection logic:
    - As the data is collected, diff of mem is calculated for each process, docker,system and stored along with the collected memory statistics.
    - These diff details will be displayed with the analyze option and leak keyword in the above histogram CLI.
    - If the diff column value for is high for a process or docker or system in the specified time slots, user can decide that it is a possible memory leak. 
    - This leak detection applies to process/docker/system level.




## 2.4 KLISH/CLICK Commands Details:
    This section covers the details of CLIs and its output formats.
       show histogram memory <process/docker/system> [stime <datetime> [ | [etime <datetime>]] ] [filter <filterstring>] [analyze <leak>] 
    
    - With no options specified, 30 days record will be displayed for all processes or dockers or system.
    
    - Options Details:
      Following are options:
         stime - start datetime in recommended ISO datetime format
         etime - end datetime in recommended ISO datetime format
         filter - filter string like process name, docker name, system memory values. Patten matching works.
         analyze - "leak" word is fixed for this release.

         - Recommended ISO datetime format input for stime and etime:
              'July 23'
              '2 days ago'
              'now'
              '3 hours ago'
              '10 minutes ago'
              'yesterday'
              'today'
              'july 1 3:00:00'
              'Aug 01 06:43:40'
              '1 july 2021'
              '2 am'
              '7/24'

          - filter is for search or grep for a pattern (or) exact name string under Process List or Docker List  or System List.

          - analyze option with leak word is used to display the leak data.

        
    - Input check: 
        - Input datetime difference should not be greater than 30 days and less then 3 minutes.
          Also, future datetime should not be the input.
          For above cases,
          "Date format error, differnce in days should not be greater than 30 days"
          "Datetime format error, end should not be geater than current time"
          "Datetime format error, difference in minutes should not be less than 3 minutes"

        - When only stime input is given but not etime, then default etime will be "now".
        - When only etime input is given but not stime, then default stime will be "now"-"30 days"
        - When both stime and etime are not given, then last 30 days data will be displayed.


    - Display Format:
        - When the given time slot difference is more then 2 days, then daily data is displayed.
            Example: stime "july22" etime "aug 1"   

        - When the given time slot difference is more than 2 hours and less then 2 days, then hourly data is displayed.
            Example: stime "36 hours ago" etime "now"

        - When the given time slot difference is less than 2 hours(120 minutes), then minutes data is displayed.
            Example, stime "15 minutes ago" etime "now"

        - Output Header Info:
          - Generic info like input Start Time, End Time, Current Time is displyed first.
          - For memory list display, fixed columns are Curr memory, High/Low of the given time slot range, Process List or Docker List or System List.
          - For leak diff display, fixed columns are Diff, Process Leak or Docker Leak or System Leak.
          - For process daily data,
            - First row displays Days for the time slot range and Current,  High/Low, Process List
            - Second row displays the days range. 
              Grouping of days happen accordingly to restrict the number of columns to max slot 11.
              Days [09-12] means 09th day to 11th day 23:59 hours.
            - Third row displays the date
          - Similarly for hourly and minutes data and for docker and system level.

          - Example:
            List Header:
```
            Start Time   : 2021-08-01 09:15:51
            End Time     : 2021-08-02 09:15:51
            Current Time : 2021-08-02 09:15:51

               Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current        High/Low      Process List
             [09-12] [12-15] [15-18] [18-21] [21-00] [00-03] [03-06] [06-09] [09-09]
              Aug/1   Aug/1   Aug/1   Aug/1   Aug/1   Aug/2   Aug/2   Aug/2   Aug/2
             ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------   
```


            Leak Header:
```
            Start Time   : 2021-08-02 06:51:03
            End Time     : 2021-08-02 09:51:03
            Current Time : 2021-08-02 09:51:03

               Hours   Hours   Hours   Hours Diff     Process Leak
             [06-07] [07-08] [08-09] [09-09]
              Aug/2   Aug/2   Aug/2   Aug/2
             ------- ------- ------- ------- ------- ----------------------------------
```

       
        - Output Data info:
            - Data from the particular grouped time slots are displayed.
            - Current will display current memory of each process/docker/system.
            - High/Low will display the Highest and Lowest memory from the given input time slot range.
            - Process List will display process names along with the process id in bracket.
            - Docker List will display docker names along with the short container id in bracket.
            - System List will display total,used,free,buffers,cached,available keywords.
                Example:
```
              ------- ------- ------- ------- -------   ----------  ----------------------------------
              544.5M       -       -       -  0B       544.5M/0B      syncd(5711)
               15.6M       -       -       -  0B        15.6M/0B      python3(10720)
               23.1M       -       -       -  23.1M     23.1M/0B      sysmonitor.py(3307)
               20.2M       -       -       -  20.7M     20.2M/0B      systemd-journald(351)
               16.0M       -       -       -  16.0M     16.0M/0B      python(3468)
```
             - When no data is available and the given input time slot is valid, only header is displayed with empty data info.



### 2.4.1 Process Level:
show histogram memory process
- Displays the last 30 days data for all processes.

```
# show histogram memory process

Start Time   : 2021-09-11 10:07:03
End Time     : 2021-10-11 10:07:03
Current Time : 2021-10-11 10:07:03
----------------------------------
    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Process List
 [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-02] [02-05] [05-08] [08-11] [11-11]
  Sep/11  Sep/14  Sep/17  Sep/20  Sep/23  Sep/26  Sep/29  Oct/2   Oct/5   Oct/8   Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -       -       -       -       -  504.8M  504.9M   504.8M/504.8M  syncd(6778)
       -       -       -       -       -       -       -       -       -       -   20.3M  20.3M     20.3M/20.3M   sysmonitor.py(4342)
       -       -       -       -       -       -       -       -       -       -   19.4M  19.4M     19.4M/19.4M   sysmonitor.py(4347)
```


show histogram memory process filter "procname"
- Displays the last 30 days data of a particular process.

```
# show histogram memory process filter bgp

Start Time   : 2021-09-11 10:13:15
End Time     : 2021-10-11 10:13:15
Current Time : 2021-10-11 10:13:15
----------------------------------
    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Process List
 [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-02] [02-05] [05-08] [08-11] [11-11]
  Sep/11  Sep/14  Sep/17  Sep/20  Sep/23  Sep/26  Sep/29  Oct/2   Oct/5   Oct/8   Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -       -       -       -       -   18.4M  18.6M     18.4M/18.4M   bgpd(8179)
       -       -       -       -       -       -       -       -       -       -    3.2M  3.2M       3.2M/3.2M    bgp.sh(5184)
       -       -       -       -       -       -       -       -       -       -    3.1M  3.1M       3.1M/3.1M    bgp.sh(5181)
```

show histogram memory process stime "start time" etime "end time"
- Displays the all the processes memory data from stime to etime slot

```
# show histogram memory process stime "5 days ago" etime "now" filter bgp

Start Time   : 2021-10-06 10:13:58
End Time     : 2021-10-11 10:13:58
Current Time : 2021-10-11 10:13:58
----------------------------------
    Days    Days    Days    Days    Days    Days Current     High/Low      Process List
 [06-07] [07-08] [08-09] [09-10] [10-11] [11-11]
  Oct/6   Oct/7   Oct/8   Oct/9   Oct/10  Oct/11
 ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -   18.4M  18.6M     18.4M/18.4M   bgpd(8179)
       -       -       -       -       -    3.2M  3.2M       3.2M/3.2M    bgp.sh(5184)
       -       -       -       -       -    3.1M  3.1M       3.1M/3.1M    bgp.sh(5181)


#  show histogram memory process stime "12 hours ago" etime "now" filter bgp

Start Time   : 2021-10-10 22:14:44
End Time     : 2021-10-11 10:14:44
Current Time : 2021-10-11 10:14:44
----------------------------------
   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current     High/Low      Process List
 [22-00] [00-02] [02-04] [04-06] [06-08] [08-10] [10-10]
  Oct/10  Oct/11  Oct/11  Oct/11  Oct/11  Oct/11  Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -   18.4M  18.6M     18.4M/18.4M   bgpd(8179)
       -       -       -       -       -       -    3.2M  3.2M       3.2M/3.2M    bgp.sh(5184)
       -       -       -       -       -       -    3.1M  3.1M       3.1M/3.1M    bgp.sh(5181)


#  show histogram memory process stime "15 minutes ago" etime "now" filter bgp
Oct 11 10:15:25.615725 2021 sonic ERR telemetry#/supervisor-proc-exit-listener: Process telemetry exited unexpectedly. Terminating supervisor...

Start Time   : 2021-10-11 10:00:22
End Time     : 2021-10-11 10:15:22
Current Time : 2021-10-11 10:15:22
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current      High/Low      Process List
 [00-02] [02-04] [04-06] [06-08] [08-10] [10-12] [12-14] [14-15]
   10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -   18.6M       -   18.6M   18.6M       -   18.6M   18.6M  18.6M     18.6M/18.6M   bgpd(8179)
       -    3.2M       -    3.2M    3.2M       -    3.2M    3.2M  3.2M       3.2M/3.2M    bgp.sh(5184)
       -    3.1M       -    3.1M    3.1M       -    3.1M    3.1M  3.1M       3.1M/3.1M    bgp.sh(5181)
```



show histogram memory process filter <procname> analyse <leak>
- Displays the specific process memory leak data

```
# show histogram memory process filter mem_leak stime " 30 minutes ago"

Start Time   : 2021-09-20 04:28:19
End Time     : 2021-09-20 04:58:19
Current Time : 2021-09-20 04:58:19
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current    High/Low    Process List
 [28-31] [31-34] [34-37] [37-40] [40-43] [43-46] [46-49] [49-52] [52-55] [55-58] [58-58]
   04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -  752.0K    2.3M    3.3M    1.3M    5.6M    1.5M       -  1.5M       5.6M/752.0K  mem_leak(20951)

# show histogram memory process filter mem_leak stime " 30 minutes ago" analyze leak

Start Time   : 2021-09-20 04:28:26
End Time     : 2021-09-20 04:58:26
Current Time : 2021-09-20 04:58:26
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Diff     Process Leak
 [28-31] [31-34] [34-37] [37-40] [40-43] [43-46] [46-49] [49-52] [52-55] [55-58] [58-58]
   04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM    04AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       -       -       -       -      0B    1.6M    1.0M   -2.0M    4.2M   -4.1M       -  744.0K  mem_leak(20951)

```
Note: In the above example, mem_leak process started with base 752.0K followed by the increase in its memory consumption to 5.6M and then finally reduced it to 1.5M.
In the analyze leak cli output, 744K is the total diff (which is total increase as it is positive) from the base 752.0K of the process mem_leak.






### 2.4.2 Docker level:
show histogram memory docker
- Displays the last 30 days data for all dockers.


```
# show histogram memory docker

Start Time   : 2021-09-11 10:35:41
End Time     : 2021-10-11 10:35:41
Current Time : 2021-10-11 10:35:41
----------------------------------
    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Docker List
 [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-02] [02-05] [05-08] [08-11] [11-11]
  Sep/11  Sep/14  Sep/17  Sep/20  Sep/23  Sep/26  Sep/29  Oct/2   Oct/5   Oct/8   Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -       -       -       -   22.7M   22.5M  23.2M     22.7M/22.5M   radv(bfef843)
       -       -       -       -       -       -       -       -       -   31.9M   31.9M  32.7M     31.9M/31.9M   sflow(6d4aeb2)
       -       -       -       -       -       -       -       -       -   24.9M   23.5M  45.5M     24.9M/23.5M   telemetry(a639856)
```

show histogram memory docker filter <dockername>
- Displays the last 30 days data of a particular docker.

```
# show histogram memory docker filter vrrp

Start Time   : 2021-09-11 10:37:25
End Time     : 2021-10-11 10:37:25
Current Time : 2021-10-11 10:37:25
----------------------------------
    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Docker List
 [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-02] [02-05] [05-08] [08-11] [11-11]
  Sep/11  Sep/14  Sep/17  Sep/20  Sep/23  Sep/26  Sep/29  Oct/2   Oct/5   Oct/8   Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -       -       -       -   24.0M   24.1M  24.9M     24.1M/24.0M   vrrp(5008479)
```


show histogram memory docker stime "start time" etime "end time" 
- Displays the all the docker memory usage data between given timeslots.

```
#  show histogram memory docker stime "2 days ago" etime "now"

Start Time   : 2021-10-09 10:37:50
End Time     : 2021-10-11 10:37:50
Current Time : 2021-10-11 10:37:50
----------------------------------
    Days    Days    Days Current      High/Low      Docker List
 [09-10] [10-11] [11-11]
  Oct/9   Oct/10  Oct/11
 ------- ------- ------- ------- -------------- ----------------------------------
       -   22.8M   22.5M  23.3M     22.8M/22.5M   radv(bfef843)
       -   32.0M   31.9M  32.8M     32.0M/31.9M   sflow(6d4aeb2)

# show histogram memory docker stime "23 hours ago" etime "now" filter "swss"

Start Time   : 2021-10-10 11:39:51
End Time     : 2021-10-11 10:39:51
Current Time : 2021-10-11 10:39:51
----------------------------------
   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current     High/Low      Docker List
 [11-14] [14-17] [17-20] [20-23] [23-02] [02-05] [05-08] [08-10]
  Oct/10  Oct/10  Oct/10  Oct/10  Oct/10  Oct/11  Oct/11  Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
   44.7M   44.8M   44.8M   44.7M   44.7M   44.7M   49.4M   46.4M  52.3M     49.4M/44.7M   swss(6fd8f6d)


# show histogram memory docker stime "23 minutes ago" etime "now" filter "swss"

Start Time   : 2021-10-11 10:17:28
End Time     : 2021-10-11 10:40:28
Current Time : 2021-10-11 10:40:28
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current     High/Low      Docker List
 [17-20] [20-23] [23-26] [26-29] [29-32] [32-35] [35-38] [38-40]
   10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -   52.4M   52.3M  52.5M     52.4M/52.3M   swss(6fd8f6d)
```


show histogram memory dockers analyze leak
- Displays the specific docker memory leak data

```
# show histogram memory docker filter stp stime " 30 minutes ago"

Start Time   : 2021-10-11 10:25:30
End Time     : 2021-10-11 10:55:30
Current Time : 2021-10-11 10:55:30
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current    High/Low    Docker List
 [25-28] [28-31] [31-34] [34-37] [37-40] [40-43] [43-46] [46-49] [49-52] [52-55] [55-55]
   10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -   25.5M   25.5M   25.5M   26.2M   26.2M   25.7M    1.0G       -  1.0G       1.0G/25.5M   stp(1798e37)


# show histogram memory docker filter stp stime " 30 minutes ago" analyze leak

Start Time   : 2021-10-11 10:25:51
End Time     : 2021-10-11 10:55:51
Current Time : 2021-10-11 10:55:51
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Diff     Docker Leak
 [25-28] [28-31] [31-34] [34-37] [37-40] [40-43] [43-46] [46-49] [49-52] [52-55] [55-55]
   10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM    10AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       -       -       -      0B   71.7K      0B  624.6K      0B -471.0K 1006.5M       -  1006.7M stp(1798e37)
```

Note: In the analyze leak cli output, 1006.7M is the total diff (which is total increase as it is positive) from the base 25.5M of the docker stp.







### 2.4.3 System Level:
show histogram memory system
- Displays the last 30 days data of the system

```
[#]show histogram memory system

Start Time   : 2021-09-11 11:06:56
End Time     : 2021-10-11 11:06:56
Current Time : 2021-10-11 11:06:56
----------------------------------
    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      System List
 [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-02] [02-05] [05-08] [08-11] [11-11]
  Sep/11  Sep/14  Sep/17  Sep/20  Sep/23  Sep/26  Sep/29  Oct/2   Oct/5   Oct/8   Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -       -       -   15.1G   15.1G   15.3G  15.6G     15.3G/15.1G   total
       -       -       -       -       -       -       -       -    2.5G    2.3G    2.3G  2.2G       2.5G/2.3G    used
       -       -       -       -       -       -       -       -   11.0G   10.9G   11.0G  11.9G     11.0G/10.9G   free
       -       -       -       -       -       -       -       -  251.2M  330.5M  318.1M  174.3M   330.5M/251.2M  buffers
       -       -       -       -       -       -       -       -    1.5G    1.6G    1.6G  1.3G       1.6G/1.5G    cached
       -       -       -       -       -       -       -       -   12.4G   12.5G   12.7G  13.1G     12.7G/12.4G   available
```

show histogram memory system filter <string>
- Displays the system memory usagedata with the filter

```
[#]show histogram memory system filter available

Start Time   : 2021-09-11 11:08:18
End Time     : 2021-10-11 11:08:18
Current Time : 2021-10-11 11:08:18
----------------------------------
    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      System List
 [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-02] [02-05] [05-08] [08-11] [11-11]
  Sep/11  Sep/14  Sep/17  Sep/20  Sep/23  Sep/26  Sep/29  Oct/2   Oct/5   Oct/8   Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
       -       -       -       -       -       -       -       -   12.4G   12.5G   12.7G  13.1G     12.7G/12.4G   available
```


show histogram memory system stime "time" etime "time"
-  Displays the system memory usagedata in given time slots

```
[#] show histogram memory system stime "5 days ago" etime "now"

Start Time   : 2021-10-06 11:08:46
End Time     : 2021-10-11 11:08:46
Current Time : 2021-10-11 11:08:46
----------------------------------
    Days    Days    Days    Days    Days    Days Current     High/Low      System List
 [06-07] [07-08] [08-09] [09-10] [10-11] [11-11]
  Oct/6   Oct/7   Oct/8   Oct/9   Oct/10  Oct/11
 ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
   15.1G   15.1G   15.1G   15.1G   15.1G   15.3G  15.6G     15.3G/15.1G   total
    2.5G    2.4G    2.4G    2.3G    2.4G    2.3G  2.2G       2.5G/2.3G    used
   10.9G   11.0G   11.0G   10.9G   10.7G   11.0G  11.9G     11.0G/10.7G   free
  249.6M  251.9M  268.7M  344.2M  378.6M  318.1M  174.4M   378.6M/249.6M  buffers
    1.5G    1.5G    1.5G    1.6G    1.7G    1.6G  1.3G       1.7G/1.5G    cached
   12.3G   12.4G   12.5G   12.6G   12.5G   12.7G  13.0G     12.7G/12.3G   available


[#]show histogram memory system stime "20 hours ago" etime "now"

Start Time   : 2021-10-10 15:09:21
End Time     : 2021-10-11 11:09:21
Current Time : 2021-10-11 11:09:21
----------------------------------
   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current     High/Low      System List
 [15-17] [17-19] [19-21] [21-23] [23-01] [01-03] [03-05] [05-07] [07-09] [09-11] [11-11]
  Oct/10  Oct/10  Oct/10  Oct/10  Oct/10  Oct/11  Oct/11  Oct/11  Oct/11  Oct/11  Oct/11
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
   15.1G   15.1G   15.1G   15.1G   15.1G   15.1G   15.1G   15.2G   15.6G   15.4G   15.6G  15.6G     15.6G/15.1G   total
    2.4G    2.4G    2.4G    2.4G    2.4G    2.4G    2.4G    2.4G    2.2G    2.0G    2.5G  2.3G       2.5G/2.0G    used
   10.6G   10.6G   10.6G   10.6G   10.6G   10.6G   10.6G   10.7G   11.9G   11.5G   11.6G  11.9G     11.9G/10.6G   free
  381.1M  381.9M  382.5M  383.1M  383.5M  384.0M  384.4M  369.3M  204.8M  225.8M  173.8M  174.5M   384.4M/173.8M  buffers
    1.8G    1.8G    1.8G    1.8G    1.8G    1.7G    1.7G    1.7G    1.3G    1.7G    1.3G  1.3G       1.8G/1.3G    cached
   12.5G   12.5G   12.5G   12.5G   12.4G   12.4G   12.4G   12.5G   13.0G   13.1G   12.7G  13.0G     13.1G/12.4G   available


[#]show histogram memory system stime "100 minutes ago" etime "now"

Start Time   : 2021-10-11 09:30:00
End Time     : 2021-10-11 11:10:00
Current Time : 2021-10-11 11:10:00
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current     High/Low      System List
 [30-40] [40-50] [50-00] [00-10] [10-20] [20-30] [30-40] [40-50] [50-00] [00-10] [10-10]
   09AM    09AM    09AM    10AM    10AM    10AM    10AM    10AM    10AM    11AM    11AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
   15.6G   15.6G   15.6G   15.1G   15.1G   15.1G   15.4G   15.6G   15.6G   15.6G       -  15.6G     15.6G/15.1G   total
    1.6G 1013.2M    1.0G    2.1G    2.2G    2.2G    2.2G    2.3G    2.5G    2.5G       -  2.2G       2.5G/1013.2M used
   12.4G   12.1G    7.7G   11.4G   11.3G   11.3G   11.7G   11.8G   11.6G   11.7G       -  11.9G     12.4G/7.7G    free
  223.0M  248.0M  319.9M  247.3M  249.8M  250.7M  195.1M  170.1M  172.7M  174.0M       -  174.6M   319.9M/170.1M  buffers
    1.3G    2.3G    6.5G    1.4G    1.4G    1.4G    1.3G    1.3G    1.3G    1.3G       -  1.3G       6.5G/1.3G    cached
   13.7G   14.3G   14.3G   12.7G   12.6G   12.7G   12.9G   13.0G   12.7G   12.8G       -  13.0G     14.3G/12.6G   available
```



show histogram memory system analyze leak
- Display leak data per system level

```
# show histogram memory system stime "10 minutes ago" filter used

Start Time   : 2021-10-11 11:06:31
End Time     : 2021-10-11 11:16:31
Current Time : 2021-10-11 11:16:31
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current    High/Low    System List
 [06-07] [07-08] [08-09] [09-10] [10-11] [11-12] [12-13] [13-14] [14-15] [15-16] [16-16]
   11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- -------------- ----------------------------------
    2.2G       -       -    2.2G       -       -    5.2G       -       -    5.2G       -  5.2G       5.2G/2.2G    used

# show histogram memory system stime "10 minutes ago" filter used analyze leak

Start Time   : 2021-10-11 11:06:41
End Time     : 2021-10-11 11:16:41
Current Time : 2021-10-11 11:16:41
----------------------------------
 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Diff     System Leak
 [06-07] [07-08] [08-09] [09-10] [10-11] [11-12] [12-13] [13-14] [14-15] [15-16] [16-16]
   11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM    11AM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       -       -       -      0B       -       -    2.9G       -       -    8.9M       -  3.0G    used
```

Note: In analyze leak output, 3.0G is the total increase in the 'used' memory from the base 2.2G of the system.



## 2.4 Techsupport

- The following command are included in techsupport collection
  show histogram memory process
  show histogram memory docker
  show histogram memory system

- Output will be available in histogram.mem.ps , histogram.mem.docker and histogram.mem.system files in techsuport dump.



## 2.5 Developer Guidelines

NA

# 3 Unit Test

1. Test the process level CLIs
2. Test the docker level CLIs
3. Test the system level CLIs
4. Test the memory leak CLIs post generating leak in a docker
5. Validation the inputs of CLIs

