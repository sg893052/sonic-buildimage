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

# About this Manual

This document provides general information about Memory Histogram in SONiC.

# Scope

This document describes the high-level design of the Memory Histogram in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**         | **Meaning**             |
|------------------|-------------------------------------|
AI                      Artificial Intelligence
RSS                     Resident Set Size


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
    - Collected memory footprint will be dumped into a disk file under /var/log/ for a day.
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
              '2024 aug 1'
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
          "Datetime format error, differnce in minutes should not be less than 3 minutes"

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

            Start Time   : 2021-08-01 09:15:51
            End Time     : 2021-08-02 09:15:51
            Current Time : 2021-08-02 09:15:51

               Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current        High/Low      Process List
             [09-12] [12-15] [15-18] [18-21] [21-00] [00-03] [03-06] [06-09] [09-09]
              Aug/1   Aug/1   Aug/1   Aug/1   Aug/1   Aug/2   Aug/2   Aug/2   Aug/2
             ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------   



            Leak Header:

            Start Time   : 2021-08-02 06:51:03
            End Time     : 2021-08-02 09:51:03
            Current Time : 2021-08-02 09:51:03

               Hours   Hours   Hours   Hours Diff     Process Leak
             [06-07] [07-08] [08-09] [09-09]
              Aug/2   Aug/2   Aug/2   Aug/2
             ------- ------- ------- ------- ------- ----------------------------------


       
        - Output Data info:
            - Data from the particular grouped time slots are displayed.
            - Current will display current memory of each process/docker/system.
            - High/Low will display the Highest and Lowest memory from the given input time slot range.
            - Process List will display process names along with the process id in bracket.
            - Docker List will display docker names along with the short container id in bracket.
            - System List will display total,used,free,buffers,cached,available keywords along with pid 1
                Example:
             ------- ------- ------- ------- ------- ------- ----------------------------------
              544.5M       0       0       0  0B       544.5M/0B      syncd(5711)
               15.6M       0       0       0  0B        15.6M/0B      python3(10720)
               23.1M       0       0       0  23.1M     23.1M/0B      sysmonitor.py(3307)
               20.2M       0       0       0  20.7M     20.2M/0B      systemd-journald(351)
               16.0M       0       0       0  16.0M     16.0M/0B      python(3468)

             - When no data is available and the given input time slot is valid, only header is displayed with empty data info.



### 2.4.1 Process Level:
show histogram memory process
- Displays the last 30 days data for all processes.

[#]show histogram memory process

Start Time   : 2021-07-02 13:04:01
End Time     : 2021-08-01 13:04:01
Current Time : 2021-08-01 13:04:01

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Process List
 [02-05] [05-08] [08-11] [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-01] [01-01]
  Jul/2   Jul/5   Jul/8   Jul/11  Jul/14  Jul/17  Jul/20  Jul/23  Jul/26  Jul/29  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0  544.5M  544.5M     5.3G/0B      syncd(5711)
       0       0       0       0       0       0       0       0       0       0   13.5M  14.4M     13.5M/0B      python3(23259)
       0       0       0       0       0       0       0       0       0       0   23.1M  23.1M    231.2M/0B      sysmonitor.py(3307)



show histogram memory process filter "procname"
- Displays the last 30 days data of a particular process.


[#]show histogram memory process filter bgp

Start Time   : 2021-07-02 13:06:35
End Time     : 2021-08-01 13:06:35
Current Time : 2021-08-01 13:06:35

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Process List
 [02-05] [05-08] [08-11] [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-01] [01-01]
  Jul/2   Jul/5   Jul/8   Jul/11  Jul/14  Jul/17  Jul/20  Jul/23  Jul/26  Jul/29  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0   19.2M  19.2M    192.3M/0B      bgpd(7073)
       0       0       0       0       0       0       0       0       0       0    3.0M  3.0M      29.8M/0B      bgp.sh(4181)


show histogram memory process stime "start time" etime "end time"
- Displays the all the processes memory data from stime to etime slot

[#]show histogram memory process stime "5 days ago" etime "now" filter bgp

Start Time   : 2021-07-27 13:10:42
End Time     : 2021-08-01 13:10:42
Current Time : 2021-08-01 13:10:42

    Days    Days    Days    Days    Days    Days Current     High/Low      Process List
 [27-28] [28-29] [29-30] [30-31] [31-01] [01-01]
  Jul/27  Jul/28  Jul/29  Jul/30  Jul/31  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0   19.2M  19.2M    192.3M/0B      bgpd(7073)
       0       0       0       0       0    3.0M  3.0M      29.8M/0B      bgp.sh(4181)




[#]show histogram memory process stime "12 hours ago" etime "now" filter bgp

Start Time   : 2021-08-01 01:12:52
End Time     : 2021-08-01 13:12:52
Current Time : 2021-08-01 13:12:52

   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current     High/Low      Process List
 [01-03] [03-05] [05-07] [07-09] [09-11] [11-13] [13-13]
  Aug/1   Aug/1   Aug/1   Aug/1   Aug/1   Aug/1   Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0   19.2M  19.2M     19.2M/0B      bgpd(7073)
       0       0       0       0       0       0    3.0M  3.0M       3.0M/0B      bgp.sh(4181)



[#]show histogram memory process stime "15 minutes ago" etime "now" filter bgp

Start Time   : 2021-08-01 12:58:35
End Time     : 2021-08-01 13:13:35
Current Time : 2021-08-01 13:13:35

 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current      High/Low      Process List
 [58-00] [00-02] [02-04] [04-06] [06-08] [08-10] [10-12] [12-13]
   12PM    01PM    01PM    01PM    01PM    01PM    01PM    01PM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0   19.2M       0   19.2M       0   19.2M   19.2M  19.2M     19.2M/19.2M   bgpd(7073)
       0       0    3.0M       0    3.0M       0    3.0M    3.0M  3.0M       3.0M/3.0M    bgp.sh(4181)




show histogram memory process filter bgp analyse <leak>
- Displays the specific process memory leak data

[#]show histogram memory process stime "5 days ago" etime "now" filter bgp analyze leak

Start Time   : 2021-07-27 13:15:47
End Time     : 2021-08-01 13:15:47
Current Time : 2021-08-01 13:15:47

    Days    Days    Days    Days    Days    Days Diff     Process Leak
 [27-28] [28-29] [29-30] [30-31] [31-01] [01-01]
  Jul/27  Jul/28  Jul/29  Jul/30  Jul/31  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0      0B  0B      bgpd(7073)
       0       0       0       0       0      0B  0B      bgp.sh(4181)






### 2.4.2 Docker level:
show histogram memory docker
- Displays the last 30 days data for all dockers.

[#]show histogram memory docker

Start Time   : 2021-07-02 13:17:34
End Time     : 2021-08-01 13:17:34
Current Time : 2021-08-01 13:17:34

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Docker List
 [02-05] [05-08] [08-11] [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-01] [01-01]
  Jul/2   Jul/5   Jul/8   Jul/11  Jul/14  Jul/17  Jul/20  Jul/23  Jul/26  Jul/29  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0   18.5M  18.5M    184.8M/0B      gbsyncd(e580c71)
       0       0       0       0       0       0       0       0       0       0   29.3M  29.3M    292.7M/0B      radv(3529483)
       0       0       0       0       0       0       0       0       0       0   29.1M  29.1M    291.0M/0B      tam(2ac8198)
       0       0       0       0       0       0       0       0       0       0   31.5M  31.5M    315.0M/0B      nat(cdb8877)




show histogram memory docker filter <dockername>
- Displays the last 30 days data of a particular docker.

[#]show histogram memory docker filter vrrp

Start Time   : 2021-07-02 13:19:00
End Time     : 2021-08-01 13:19:00
Current Time : 2021-08-01 13:19:00

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      Docker List
 [02-05] [05-08] [08-11] [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-01] [01-01]
  Jul/2   Jul/5   Jul/8   Jul/11  Jul/14  Jul/17  Jul/20  Jul/23  Jul/26  Jul/29  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0   33.7M  33.7M    337.1M/0B      vrrp(d5c2e11)




show histogram memory docker stime "start time" etime "end time" 
- Displays the all the docker memory usage data between given timeslots.

[#]show histogram memory docker stime "2 days ago" etime "now"

Start Time   : 2021-07-30 13:21:49
End Time     : 2021-08-01 13:21:49
Current Time : 2021-08-01 13:21:49

    Days    Days    Days Current      High/Low      Docker List
 [30-31] [31-01] [01-01]
  Jul/30  Jul/31  Aug/1
 ------- ------- ------- ------- ------- ----------------------------------
       0       0   18.5M  18.5M    184.8M/0B      gbsyncd(e580c71)
       0       0   29.3M  29.3M    292.7M/0B      radv(3529483)
       0       0   29.1M  29.1M    291.0M/0B      tam(2ac8198)


[#]show histogram memory docker  stime "23 hours ago" etime "now" filter "swss"

Start Time   : 2021-07-31 14:26:46
End Time     : 2021-08-01 13:26:46
Current Time : 2021-08-01 13:26:46

   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current     High/Low      Docker List
 [14-17] [17-20] [20-23] [23-02] [02-05] [05-08] [08-11] [11-13]
  Jul/31  Jul/31  Jul/31  Jul/31  Aug/1   Aug/1   Aug/1   Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0   80.6M  80.6M     80.6M/0B      swss(f53e379)



[#]show histogram memory docker  stime "23 minutes ago" etime "now" filter "swss"

Start Time   : 2021-08-01 13:04:28
End Time     : 2021-08-01 13:27:28
Current Time : 2021-08-01 13:27:28

 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current     High/Low      Docker List
 [04-07] [07-10] [10-13] [13-16] [16-19] [19-22] [22-25] [25-27]
   01PM    01PM    01PM    01PM    01PM    01PM    01PM    01PM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0   80.6M   80.6M   80.6M   80.6M   80.6M   80.6M   80.6M  80.6M     80.6M/80.6M   swss(f53e379)

[#]




show histogram memory dockers analyze leak
- Displays the specific docker memory leak data

[#]show histogram memory docker  stime "10 days ago" etime "now" analyze leak

Start Time   : 2021-07-22 13:29:27
End Time     : 2021-08-01 13:29:27
Current Time : 2021-08-01 13:29:27

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Diff     Docker Leak
 [22-23] [23-24] [24-25] [25-26] [26-27] [27-28] [28-29] [29-30] [30-31] [31-01] [01-01]
  Jul/22  Jul/23  Jul/24  Jul/25  Jul/26  Jul/27  Jul/28  Jul/29  Jul/30  Jul/31  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0      0B  0B      gbsyncd(e580c71)
       0       0       0       0       0       0       0       0       0       0      0B  0B      radv(3529483)
       0       0       0       0       0       0       0       0       0       0      0B  0B      tam(2ac8198)
       0       0       0       0       0       0       0       0       0       0      0B  0B      nat(cdb8877)
       0       0       0       0       0       0       0       0       0       0      0B  0B      lldp(2bcda21)
       0       0       0       0       0       0       0       0       0       0      0B  0B      l2mcd(393a51b)
       0       0       0       0       0       0       0       0       0       0      0B  0B      snmp(fd1b654)
       0       0       0       0       0       0       0       0       0       0      0B  0B      mgmt-framework(8a1d330)
       0       0       0       0       0       0       0       0       0       0      0B  0B      sflow(003e427)
       0       0       0       0       0       0       0       0       0       0      0B  0B      stp(e92d5a0)
       0       0       0       0       0       0       0       0       0       0      0B  0B      dhcp_relay(16541e5)
       0       0       0       0       0       0       0       0       0       0      0B  0B      telemetry(53e6a51)
       0       0       0       0       0       0       0       0       0       0      0B  0B      udld(605610f)
       0       0       0       0       0       0       0       0       0       0      0B  0B      syncd(b742d2b)
       0       0       0       0       0       0       0       0       0       0      0B  0B      teamd(85ec413)
       0       0       0       0       0       0       0       0       0       0      0B  0B      iccpd(bcf638c)
       0       0       0       0       0       0       0       0       0       0   40.0K  11.9K   pmon(073f5bf)
       0       0       0       0       0       0       0       0       0       0  399.2K  98.1K   swss(f53e379)
       0       0       0       0       0       0       0       0       0       0      0B  0B      bgp(fe1d198)
       0       0       0       0       0       0       0       0       0       0      0B  0B      vrrp(d5c2e11)
       0       0       0       0       0       0       0       0       0       0  606.3K  546.9K  database(dda4487)





### 2.4.3 System Level:
show histogram memory system
- Displays the last 30 days data of the system

[#]show histogram memory system

Start Time   : 2021-07-02 13:30:26
End Time     : 2021-08-01 13:30:26
Current Time : 2021-08-01 13:30:26

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      System List
 [02-05] [05-08] [08-11] [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-01] [01-01]
  Jul/2   Jul/5   Jul/8   Jul/11  Jul/14  Jul/17  Jul/20  Jul/23  Jul/26  Jul/29  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0   15.1G  15.1G     15.1G/0B      total(1)
       0       0       0       0       0       0       0       0       0       0    3.1G  3.1G      15.1G/0B      used(1)
       0       0       0       0       0       0       0       0       0       0    9.8G  9.8G      15.1G/0B      free(1)
       0       0       0       0       0       0       0       0       0       0  476.1M  476.2M   476.2M/0B      buffers(1)
       0       0       0       0       0       0       0       0       0       0    1.8G  1.8G       1.8G/0B      cached(1)
       0       0       0       0       0       0       0       0       0       0   11.8G  11.8G     11.8G/0B      available(1)




show histogram memory system filter <string>
- Displays the system memory usagedata with the filter

[#]show histogram memory system filter available

Start Time   : 2021-07-02 13:31:32
End Time     : 2021-08-01 13:31:32
Current Time : 2021-08-01 13:31:32

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Current     High/Low      System List
 [02-05] [05-08] [08-11] [11-14] [14-17] [17-20] [20-23] [23-26] [26-29] [29-01] [01-01]
  Jul/2   Jul/5   Jul/8   Jul/11  Jul/14  Jul/17  Jul/20  Jul/23  Jul/26  Jul/29  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0   11.8G  11.8G     11.8G/0B      available(1)


show histogram memory system stime "time" etime "time"
-  Displays the system memory usagedata in given time slots
[#]show histogram memory system stime "5 days ago" etime "now"

Start Time   : 2021-07-27 13:33:31
End Time     : 2021-08-01 13:33:31
Current Time : 2021-08-01 13:33:31

    Days    Days    Days    Days    Days    Days Current     High/Low      System List
 [27-28] [28-29] [29-30] [30-31] [31-01] [01-01]
  Jul/27  Jul/28  Jul/29  Jul/30  Jul/31  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0   15.1G  15.1G    151.4G/0B      total(1)
       0       0       0       0       0    3.1G  3.1G      30.7G/0B      used(1)
       0       0       0       0       0    9.8G  9.8G      98.0G/0B      free(1)
       0       0       0       0       0  476.1M  476.2M     4.6G/0B      buffers(1)
       0       0       0       0       0    1.8G  1.8G      18.0G/0B      cached(1)
       0       0       0       0       0   11.8G  11.8G    118.4G/0B      available(1)


[#]show histogram memory system stime "20 hours ago" etime "now"

Start Time   : 2021-07-31 17:35:05
End Time     : 2021-08-01 13:35:05
Current Time : 2021-08-01 13:35:05

   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours   Hours Current     High/Low      System List
 [17-19] [19-21] [21-23] [23-01] [01-03] [03-05] [05-07] [07-09] [09-11] [11-13] [13-13]
  Jul/31  Jul/31  Jul/31  Jul/31  Aug/1   Aug/1   Aug/1   Aug/1   Aug/1   Aug/1   Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0   15.1G  15.1G     15.1G/0B      total(1)
       0       0       0       0       0       0       0       0       0       0    3.1G  3.1G       3.1G/0B      used(1)
       0       0       0       0       0       0       0       0       0       0    9.8G  9.8G       9.8G/0B      free(1)
       0       0       0       0       0       0       0       0       0       0  476.2M  476.2M   476.2M/0B      buffers(1)
       0       0       0       0       0       0       0       0       0       0    1.8G  1.8G       1.8G/0B      cached(1)
       0       0       0       0       0       0       0       0       0       0   11.8G  11.8G     11.8G/0B      available(1)

[#]


[#]show histogram memory system stime "100 minutes ago" etime "now"

Start Time   : 2021-08-01 11:55:50
End Time     : 2021-08-01 13:35:50
Current Time : 2021-08-01 13:35:50

 Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Minutes Current     High/Low      System List
 [55-05] [05-15] [15-25] [25-35] [35-45] [45-55] [55-05] [05-15] [15-25] [25-35] [35-35]
   11AM    12PM    12PM    12PM    12PM    12PM    12PM    01PM    01PM    01PM    01PM
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0   15.1G  15.1G     15.1G/0B      total(1)
       0       0       0       0       0       0       0       0       0       0    3.1G  3.1G       3.1G/0B      used(1)
       0       0       0       0       0       0       0       0       0       0    9.8G  9.8G       9.8G/0B      free(1)
       0       0       0       0       0       0       0       0       0       0  476.2M  476.2M   476.2M/0B      buffers(1)
       0       0       0       0       0       0       0       0       0       0    1.8G  1.8G       1.8G/0B      cached(1)
       0       0       0       0       0       0       0       0       0       0   11.8G  11.8G     11.8G/0B      available(1)

[#]



show histogram memory system analyze leak
- Display leak data per system level

[#]show histogram memory system stime "20 days ago" etime "now" analyze leak

Start Time   : 2021-07-12 13:37:00
End Time     : 2021-08-01 13:37:00
Current Time : 2021-08-01 13:37:00

    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days    Days Diff     System Leak
 [12-14] [14-16] [16-18] [18-20] [20-22] [22-24] [24-26] [26-28] [28-30] [30-01] [01-01]
  Jul/12  Jul/14  Jul/16  Jul/18  Jul/20  Jul/22  Jul/24  Jul/26  Jul/28  Jul/30  Aug/1
 ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ------- ----------------------------------
       0       0       0       0       0       0       0       0       0       0      0B  0B      total(1)
       0       0       0       0       0       0       0       0       0       0      0B  0B      used(1)
       0       0       0       0       0       0       0       0       0       0      0B  0B      free(1)
       0       0       0       0       0       0       0       0       0       0      8K  8K      buffers(1)
       0       0       0       0       0       0       0       0       0       0      0B  0B      cached(1)
       0       0       0       0       0       0       0       0       0       0      0B  0B      available(1)

[#]


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

