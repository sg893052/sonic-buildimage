# Feature Name
Trace Process stack
# High Level Design Document
#### Rev 0.1

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definition/Abbreviation](#definitionabbreviation)
  
# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 16/02/2022  |   Karthik Raj        | Initial version                   |

# About this Manual
This document provides general information about tracing in sysmonitor the processes which use high cpu

# Scope
This document describes the high level design of process tracing.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**                 | **Meaning**                         |
|--------------------------|-------------------------------------|
|                          |                                     |

# 1 Feature Overview

Sometimes incidents of process reaching 100% cpu are reported. It is oftentimes difficult to reproduce such issues. It would be useful, therefore, to dump stack traces of such processes as in when their cpu usage goes above threshold for a longer duration.

The complete stack backtrace of a process can be obtained by using gdb or other mechanisms like ptrace, libunwind. However applying these apparatuses on a running process is
tantamount to tampering with the process. Hence it should not be used in customer/real time environment. But other convenient and easy-to-use things like CPU registers,
virtual memory mapping, kernel stack, which are readily available in procfs, can be dumped for analysis in hindsight. 

## 1.1 Requirements

### 1.1.1 Functional Requirements
Sysmonitor daemon currently raises critical syslog message when a process crosses 90% cpu. It is not required to consider seriously the processes which spike to 100% cpu once in a while. The emphasis should be on those processes which hog the cpu for a longer duration. Hence the stack collection should be initiated only when a process's cpu usage lingers in critical range for 10 minutes. - 10 minutes is the periodicity of the cpu monitoring thread in sysmonitor. Following are the info which should be dumped.

1. /proc/pid/stat      -  Gives stats like Instruction pointer, stack pointer, irq, softirq, number of context switches, and so on.
2. /proc/pid/maps      -  Gives virtual memory mapping of all sections of memory layout.

    The current instruction pointer retrieved from /proc/stat can be translated, with the help of /proc/maps, to find the place at which the process is stuck. 

3. /proc/pid/stack     -  Gives kernel stack of the process

  If system call is the cause of the trouble, a cue can be taken from /proc/stack while analysing.

4. In case of debug image, gdb should be used to obtain backtrace. 

root@sonic:/var/log/process# cat stress_process/15_02_2022_10_55_46/5229/backtrace

0x00005614eb43616f in do_prime ()

Thread 1 (process 5229):
0  0x00005614eb43616f in do_prime ()
1  0x00005614eb4361d0 in main ()
        [Inferior 1 (process 5229) detached]

# 2 Design

Traces should be dumped on this path - /var/log/process. The unique identifier should be the process name, for pid is ever changing. Under a process name, there can be multiple
process ids. For 'bash' or 'python' processes, the script name should be the identifier.

This should be the format : /var/log/process/processname/timestamp/pid/taskid

e.g.

root@sonic:/var/log/process# ls -ltr stress_process/

total 8
drwxr-xr-x 3 root root 4096 Feb 15 10:45 15_02_2022_10_45_58
drwxr-xr-x 3 root root 4096 Feb 15 10:46 15_02_2022_10_46_57

root@sonic:/var/log/process# ls -ltr stress_process/15_02_2022_10_46_57/

total 4
drwxr-xr-x 3 root root 4096 Feb 15 10:46 5229

root@sonic:/var/log/process# ls -ltr stress_process/15_02_2022_10_46_57/5229/

total 8
drwxr-xr-x 2 root root 4096 Feb 15 10:46 5229
-rw-r--r-- 1 root root  169 Feb 15 10:46 backtrace

root@sonic:/var/log/process# ls -ltr stress_process/15_02_2022_10_46_57/5229/5229/

total 12
-r--r--r-- 1 root root  307 Feb 15 10:46 stat
-r-------- 1 root root   25 Feb 15 10:46 stack
-r--r--r-- 1 root root 2016 Feb 15 10:46 maps

In case of a multi-threaded process, it is imperative to dump traces of all its threads. See for example traces of syncd below

root@sonic:/var/log/process/syncd/15_02_2022_11_32_24/7661# ls

11428  11605  8056  8059  8062  8064  8103  8145  8290  8294  8296  8363  8365  8412  8416  8529  8557  8567  8569
11603  7661   8058  8060  8063  8089  8105  8153  8291  8295  8324  8364  8402  8414  8418  8546  8561  8568


# 2.1  Log rotation

The monitoring thread checks the cpu % every 10 minutes and if in case the process is at critical cpu the next time, trace is dumped again.
Atmost 5 such traces should be dumped. After 5 dumps, the oldest one should be deleted and should be replaced with the current one.  

The dumps should remain for eternity unless or until the user manually deletes them. sysmonitor restart or system reboot should not have an impact on them. 

# 2.2 Disk space

It would take around 2.5kB for a single trace dump. Max 5 dumps => 12.5kB per thread. In course of time, if we assume there are 50 processes, with 5 threads on an average, that have reached cpu usage above 90%, then the disk usage would be 3MB. Hence disk usage should not be a problem. 
