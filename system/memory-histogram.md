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
| 0.1 | 04/30/2021 |  Senthil Kumar Guruswamy    | Initial version          |

# About this Manual

This document provides general information about Memory Histogram in SONiC.

# Scope

This document describes the high-level design of the Memory Histogram in SONiC.

# Definition/Abbreviation

### Table 1: Abbreviations
| **Term**         | **Meaning**             |
|--------------------------|-------------------------------------|
TBD

# 1 Feature Overview

SONiC is an open-source network operating system based on Debian Linux, integrated with various applications.
Memory is one of the crucial resources which is being used across multiple applications simultaneously. SONiC applications are meant to run continuously unless the user requests to reload the system. In a long running system, if memory is being leaked by application or over used by application, it will result in system panic because of out of memory condition. It is essential to monitor the memory usage of a process periodically and analyze the usage for over a period of time.
Many open source tools do exist to collect the current memory status of processes. But we need an infrastructure to collect raw memory data in regular intervals and convert them to useful information and report it for analysing.

In this HLD, the memory histogram feature tracks the memory usage of processes periodically and reports history of memory usage which helps in diagnosing memory related issues.


## 1.1 Requirements

### 1.1.1 Functional Requirements

The memory histogram should periodically poll the memory usage of processes and collect the raw data.
- Collecting raw data:
	1. Frequency of collection ? 
	2. How many samples/entries ? 
		Data can be captured in the form of time slot intervals. 
		Each time slot can have 1 or more entries. 
		The below slots will be tuned over the course of development as per the ask.
			1. Three minutes slot (1 entry)
	        2. One hour slot ( 6 entries)
	        3. One day slot ( 8 entries)
	        4. One week slot (7 entries)
	        5. One month slot (4 entries)

- Storage place:
	Redis database. Since it polls the memory stats at a fixed interval, all the data can't be stored in the redis because it creates too many entries in the database.
	Hence captured data is stored in the form of time slot intervals as above.
	For a given process, it can store upto last one month of process historical data.
	Storage technique in redis db will be optimized over the course of development.

- Level of monitoring:
	Process level
	Docker level
	System level

-CLI to fetch the stored data:
	show histogram memory process slot <timeslot>
       - shows the processes data from given timeslot
	show histogram memory system slot <timeslot>
	   - show the system data from given timeslot
	show histogram memory docker slot <timeslot>
	   - show the docker data from given timeslot


### 1.1.2 Configuration and Management Requirements

### 1.1.3 Scalability Requirements
- NA

### 1.1.4 Warm/fast/cold Boot Requirements
-TBD
 
# 2 Design
## 2.1 Overview

TBD

## 2.2 Current Model

TBD

## 2.3 Feature Details

TBD

## 2.4 KLISH/CLICK Commands

TBD 

## 2.5 Developer Guidelines

TBD

# 3 Unit Test

TBD


