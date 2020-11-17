# Applying Resource usage limits to SONiC Dockers



## Background


Field issues seen with SONiC in the recent weeks have exposed a few obvious issues with supportability especially when it comes to root causing and analysis on a live system. The primary reason is that SONiC currently does not have enough safeguards to protect the system in terms of high CPU utilization and/or high memory usage, when subjected to packet storms or simply due to issues in the code leading to memory leaks.

To ensure robustness to the overall supportability of the system, one of the (many) safeguards that is needed is to have an upper-limit to all our applications in terms of CPU and Memory resources that can be used. The rest of this document explains the requirements, options and next steps to implementation.

## Requirements

- Provide an infrastructure to limit any one application from “over-using” CPU and Memory resources either due high system load or due to a mis-behaving code, that can prevent remedial actions by sysadmin.
- The imposed limits should be realistic. The limiting scheme should not prevent applications from working seamlessly in normal and scaled configurations/loads.
- Ensure limits are enforced only when CPU and/or memory thresholds are trending towards critical levels.
- When a threshold is crossed by an application, provide a mechanism to reliably restart the troublesome module (thereby freeing up it's resources) - the rest of the SONiC system should restart around this as required, and the system should continue on.
- Collect as much techsupport/debug information when resource limiting measures are enforced.

## Resource Limiting Options

**Option 1**:  Preset limits to each docker
Limits are passed when the docker is launched thru ‘docker create ...’ command
Applications within the Docker will have to share this preset limit.
Docker is restarted when limits are crossed. 
No Application level code changes needed. However, Docker control scripts have to be enhanced to ensure docker exits when any application is terminated.

**Option 2**: Preset limits at the application level - setrlimit()
Each application implements its own CPU and Memory limits using setrlimit() system call.
Application should handle events when resource limits are crossed.
Code changes are complex.

**Option 3:** Use setrlimit() for memory and external tools to monitor CPU
cpulimit(8) is an external tool that throttles CPU utilization of the process w/o changing its priority. This enables scripts like sysmonitor.sh to detect applications using high CPU and throttle it down.
Code changes in every application is needed for imposing memory limits. Also sysmonitor.sh needs to be enhanced for monitoring top CPU consumers.

While Option 2 and 3 provide a more finer granularity to control and throttle each application, the complexity lies in code changes and also in making judgement on what limits to set. Additionally, it is not easy to test such limits in all possible configurations. Hence they are not being considered.

SONiC is already Docker based and the Docker infrastructure provides very flexible ways to apply limits at the Docker level rather than to each application. Using option 1 a decent resource limiting scheme can be implemented without complex code changes and resource sizing exercises. The rest of this document will focus on details of implementation using Option 1.

CPU Limits
The Docker infrastructure provides many knobs to control CPU resource throttling. These parameters can be set when the docker is created. For SONiC, only the following knobs will be used:

	       --cpus=<number of CPUs allocated>
           --cpu-shares=<A relative weightage to the Docker>

[https://docs.docker.com/config/containers/resource_constraints
](url)

To allocate a specific number of CPUs for each docker is not a simple decision. This needs careful analysis and testing such limits is also complex.  Hence a “top-down” approach is considered. 
All SONiC dockers will be assigned N-1 CPU cores.  Here ‘N’ stands for the total number of CPUs on the device. This scheme also ensures that at least one core (but not limited to) is available for host applications and kernel threads. It also ensures that when the system is under distress, administrators can login to the box for any manual intervention and run host applications/scripts to collect and transfer logs.

Every Docker is assigned a relative weightage based on their criticality. This ensures that dockers with higher weights will always get a higher CPU share than the ones with lower (even if they are trying to use up more CPU).  This is illustrated in the below example:
In the below o/p, snmp has higher cpu-shares value than sflow. In both dockers the same test program is run which consumes 100% of 1 CPU when run without limits. When configured with --cpu-shares, and with both contending on the same CPU, the following is observed.

     CONTAINER ID       NAME        CPU %         MEM USAGE / LIMIT     MEM %                
     2a92eda3e510       snmp        51.67%        64.63MiB / 15.14GiB   0.42%                
     43198efba177       sflow       48.44%        22.69MiB / 100MiB     22.69%               

The default cpu-shares value assigned to each docker is 1024. As a part of this implementation, critical dockers should have higher value than 1024 and non-critical dockers should have lower value.  Relative weightage of each docker will be decided based on its criticality.
Note that cpu-shares are soft-limits that are enforced when there are resource constraints. If there are ample CPU resources, applications are allowed to use as much as they need. 

## Memory Limits


As with CPU, setting of memory limits will also use a more democratic approach.
All dockers will be allocated 90% of total memory available on the system

        --memory=<0.9 * TotalMem>
Memory limit is a hard limit. When any docker crosses this threshold, the application with highest resident memory (within that docker) will be killed.
To ensure that only the process in the Docker is killed (and not panic the system), the kernel OOM behavior  controlled by vm.panic_on_oom sysctl value should be set to 0. 
Each application inside the Docker should be set up to trigger a docker exit when terminated.

## Configuring Docker Limits

To implement the above scheme the following changes need to be implemented.
<<<<<<< HEAD
All docker limit values will be centralized in a JSON file under /etc/sonic/docker_limits.json. See Appendix for details.
Assign cpu-shares value for each Docker.
A wrapper script (sonic-dockerlimits) will return the limits in a string that can directly be used with the ‘docker create’ or 'docker update' command.  

     Usage:
      sonic-dockerlimits < -mem | -cpu | -sys | -update > <container name> | -all> 
     
     For e.g.
      # Get both CPU and Memory Limits
      sonic-dockerlimits -sys snmp
      Output:  “--cpus=7 --cpu-shares=900 --memory=10g ”

      # Get memory limits only
      sonic-dockerlimits -mem sflow

      # Get CPU limit only
      sonic-dockerlimits -cpu syncd

      # Re-apply limits by reading /etc/sonic/docker_limits.json for a specific docker
      sonic-dockerlimits -update tam
      Output: No output. Verify the new limits by running 'docker stats'

      # Re-apply limits for all dockers specificied in /etc/sonic/docker_limits.json
      sonic-dockerlimits -update -all
      Output: No output. Verify the new limits by running 'docker stats'

## Factory Default Settings

The GA version of Broadcom SONiC will have tuned limits that will be available in /etc/sonic/docker_limits.json. During the first boot, the settings will be applied once all the Dockers are up and running. This action is done only once on the system. The limits are not updated in any subsequent reboots or 'config reloads'. The factory default resource limits will be saved in /var/log/docker_memlimits.log. This file should not be deleted.


## Docker / Process Restartability
SONiC already has systemd / supervisord based restartability for both Docker and process terminations. This infrastructure already supports restarting dependent Dockers when a critical Docker exits.  As part of resource limiting changes, each Docker owner has to decide whether the Docker should be restarted or just a process that was killed to be restarted. 

These changes have to be done for all Dockers/processes. 

Scope of changes for Docker restart
<<<<<<< HEAD
The below gerrit change was done for ICCPD.  This can be used as a reference to make the config changes:
     
   
      https://gerrit-csg-nos.devops.broadcom.net:8083/c/sonic/sonic-buildimage/+/23400

## Scope of changes for Process restart
In pmon Docker, all processes are independent. Hence only a terminated process is instrumented to restart. Follow the changes in this file for process restartability.

sonic-buildimage/dockers/docker-platform-monitor/docker-pmon.supervisord.conf.j2

## User Altering and Log Collection

The sysmonitor.sh is already enhanced to alert the user when different memory thresholds are crossed.  Apart from generating appropriate syslogs at each level,  a tech-support dump is collected as well. These changes are under review in the following gerrit id:

      https://gerrit-csg-nos.devops.broadcom.net:8083/c/sonic/sonic-buildimage/+/24678

## Kernel OOM behavior configuration
To enable cgroups based process termination when memory limits are crossed, the kernel parameter vm.panic_on_oom should be set to 0.  The current default value is set to 2 (panic on OOM). Setting this to 1 will ensure that only the process in the Docker is killed and will not panic the kernel. 

However, if any application on the host consumes enough memory which could push the AvaialableMemory to 0, a kernel panic is initiated. In case of SONiC, we have safeguards in the sysmonitor script to initiate a tech-support and a reboot when the system reaches 95% memory consumption. Hence, we avoid panicking the device for OOM altogether.

      https://sysctl-explorer.net/vm/panic_on_oom/


## Appendix A


The value of each docker should be set to a value higher / equal to / lower than 1024 (default) to ensure proper prioritization on allocating CPU resources. 

Possible values are: 512, 768,1024,1280,1536,1792,2048

Dockers can have the same value (if they have the same priority)

Suggested Values:


          Priority 0: 1792
          Priority 1: 1280,1536
          Priority 2: 1024,768
          Priority 3: 512

| Docker    | Priority  | CPUShare-def | CPUShare-Custom | Justification | owner  |
|:----------|:----------|:-------------|:----------------|:--------------|:-------|
| snmp      | 3         | 512          |                 |               |        |
| sflow     | 3         | 512          |                 |               |        |
| udld      | 1         | 1280         |                 |               |        |
| lldp      | 3         | 512          |                 |               |        |
| stp       | 1         | 1536         |                 |               |        |
| nat       | 2         | 1024         |                 |               |        |
| l2mcd     | 2         | 1024         |                 |               |        |
| telemetry | 3         | 512          |                 |               |        |
| dhcp_relay| 3         | 512          |                 |               |        |
| radv      | 2         | 1024         |                 |               |        |
| tam       | 3         | 512          |                 |               |        |
| syncd     | 1         | 1536         |                 |               |        |
| teamd     | 1         | 1536         |                 |               |        |
| iccpd     | 1         | 1536         |                 |               |        |
| bgp/FRR   | 1         | 1536         |                 |               |        |
| mgmt-fw   | 3         | 512          |                 |               |        |
| swss      | 0         | 1792         |                 |               |        |
| broadview | 3         | 1024         |                 |               |        |
| pmon      | 2         | 1024         |                 |               |        |
| database  | 0         | 2048         |                 |               |        |
| snmp      | 3         | 512          |                 |               |        |

Docker Documentation Ref: https://docs.docker.com/config/containers/resource_constraints/

## Appendix B

```
An example of  /etc/sonic/docker-limits.json
{
	"database" :  {
		"cpus" : "auto",
		"cpu-shares" : "2048",
		"cpuset" : "notused",
		"memory" : "auto"
	},
           "syncd" :  {
		"cpus" : "auto",
		"cpu-shares" : "1536",
		"cpuset" : "notused",
		"memory" : "auto"
	},
           ...
}
```

Legend:

| Value.    | Description.          |
|:----------|:----------------------| 
| "auto"   | sonic-dockerlimits script will dynamically compute this based on the device its running on. This is the default setting to be used, when precise upper limits for Memory or CPU cannot be determinted. When set to "auto", the CPU count will be set to N-1 (N = Total CPUs in the system) and Memory will be set to 90% of Available memory that remains after all Dockers are loaded. |
| "notused" | The value is indicative only. Any settings here will be currently ignored.|
| "{value}" | An integer value as shown in the above table. Applies to only cpu-shares.|






## Appendix C - Testing Guidelines

### Testing Restartability

As a part of this infrastructure, Docker Restartability is needed for each docker when a process terminates/terminated. This functionality is independent and hence it can be tested so as well using below two options:
Option 1: Once the relevant infra files for systemd based restart is implemented, the  user should be able to induce a docker restart by running 
docker exec <docker-name> pkill <app-name>
For e.g docker exec syncd pkill /usr/bin/syncd
OR
 docker exec <docker-name> kill -9 <pid>  
NOTE: The pid should be the one that is assigned inside the docker
Option 2:  The debugSh infrastructure allows each application to instrument self-termination based on an external signal. The details of implementing this infrastructure is explained in this document

### Testing Memory and CPU Limits

The CLI command: sonic-dockerlimits

The CLI sonic-dockerlimits is a command that reads /etc/sonic/docker-limits.json and based on the command line options it translates that command line parameter to be used with the ‘docker create’ CLI. This has already been added to all docker startup scripts. Users need not have to take any action. However, the ‘-update’ option allows docker limits to be updated at runtime and will help in testing limits. This is explained in the subsequent section.  

**NOTE:** Please ensure vm.panic_on_oom = 0. If not set, manually set this using the command ‘sysctl -w vm.panic_on_oom=0’  (This will be eventually set automatically. But it may not be in your build yet).


### Test to induce a memory limit breach

As explained in the earlier sections, the limits configured for all dockers are saved in the file /etc/sonic/docker_limits.json. To test docker limits in action, change the limits to a smaller value instead of the default ‘auto’ settings and then use the below mentioned tools to induce memory or CPU load.  Here is an example for syncd:

**Step 1:** Edit /etc/sonic/docker_limits.json and change the setting for syncd as follows
      …
 "syncd" :  {
		"cpus" : "auto",
		"cpu-shares" : "1536",
		"cpuset" : "notused",
		"memory" : "auto"  ⬅  {Change this to “1g”}
	},

**Step 2:** Update the  docker properties
Run ‘sonic-dockerlimits -update syncd’

**Step 3:** Verify if the new limit has taken effect


```
admin@sonic:~$ docker inspect syncd | grep -i cpu
            "CpuShares": 1536,
            "NanoCpus": 3000000000,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "CpuCount": 0,
            "CpuPercent": 0,
admin@sonic:~$
admin@sonic:~$ docker inspect syncd | grep Memory
            "Memory": 6442450944,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": -1,
            "MemorySwappiness": null,

```


**Step 3a:** Using ‘docker stats command

```
admin@sonic:~$ docker stats --all --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}" --no-stream syncd
CONTAINER           CPU %               MEM USAGE / LIMIT
syncd               35.36%              314.8MiB / 6GiB


```

Typically syncd consumes about 300-400MB based on the number of ports. By running the below command within the syncd docker, the memory consumption will artificially increase and when the limit is crossed, the process should be killed and the docker should restart.

Copy the memhog utility from /projects/csg_sonic/gh891339/dlimits/memhog

**Step 4:**

Run ‘docker exec syncd memhog 1000’. Here 1000 corresponds to 1000MB or 1G. 

Monitor memory consumptions in another window by running ‘docker stats syncd’

### Testing CPU limits

When the CPU limit is breached, Dockerd will not terminate any process but it will throttle down all processes in the docker in terms of its control group time slice on the CPU. The effect of throttling down can be verified by running the ‘docker stats’ command.

To induce high CPU consumption from within a docker the following can be done:

- Copy the cpuhog tool from /projects/csg_sonic/gh891339/dlimits/cpuhog onto the DUT:/home/admin
- Run ‘docker cp /home/admin/cpuhog <docker-name>:/root’
- Start ‘docker stat’ from a different window to monitor CPU utilization before .
- Run ‘docker exec <docker-name> /root/cpuhog’
- Note the CPU utilization throttle down, when cpuhog runs.
- You can also log into your docker and use the ‘top’ command to monitor the throttling effect.

### Test Programs

The following utils can be used to induce CPU and memory usage.  The REAMDE has info on how to use these utils:
```
<<<<<<< HEAD
sonic-buildimage/files/rlimits/test-utils> ls -l
total 21
-rwxr-xr-x 1 gh891339 users  614 Oct 15 15:01 cpuhog.c
-rwxr-xr-x 1 gh891339 users  815 Oct 15 15:01 memhog.c
-rwxr-xr-x 1 gh891339 users  612 Oct 15 15:01 README

```
