# SONiC Configuration Setup Service

# High Level Design Document
#### Rev 0.6

# Table of Contents
  * [List of Tables](#list-of-tables)
  * [Revision](#revision)
  * [About This Manual](#about-this-manual)
  * [Scope](#scope)
  * [Definitions](#definitions)
  * [1. Feature Overview](#1-feature-overview)
    * [1.1 Requirements](#11-requirements)
    * [1.2 Design Overview](#12-design-overview)
  * [2. Functionality](#2-functionality)
    * [2.1 Target Use Cases](#21-target-deployment-use-cases)
    * [2.2 Functional Description](#22-functional-description)
    * [2.3 CLI](#23-cli)
  * [3. Unit Tests](#3-unit-tests)
  * [4. Appendix](#4-appendix)


# List of Tables
[Table 1: Definitions](#table-1-definitions)

# Revision
| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 07/16/2019  |   Rajendra Dendukuri      | Initial version                   |
| 0.2 | 07/22/2019 | Rajendra Dendukuri | Update Test plan, fixed review comments |
| 0.3 | 10/16/2019 | Rajendra Dendukuri | Added an example usecase of password migration |
| 0.4 | 02/25/2020 | Rajendra Dendukuri | Added:<BR>    Installer migration hooks capability<BR>    Automatic backup and restore of migration hooks|
| 0.5 | 07/29/2020 | Rajendra Dendukuri | Added ConfigDB version scheme and downgrade support|
| 0.6 | 08/27/2020 | Rajendra Dendukuri | Re-worked ConfigDB migration logic |

# About this Manual
This document provides details about how the switch configuration is handled on a SONiC device.

# Scope
This document describes functional behavior of the proposed config-setup service. It also explains the dependency between various other SWSS services and the config-setup service.

# Definitions
### Table 1: Definitions
| **Term**       | **Definition**               |
| -------------- | ---------------------------- |
| Config DB      | SONiC Configuration Database |
| config-setup   | Configuration Setup          |
| startup-config | /etc/sonic/config_db.json    |
| ZTP            | Zero Touch Provisioning      |

# 1 Feature Overview

SONiC switch configuration is stored in a Redis database instance called Config DB. The contents of Config DB reflect most of the configuration of a SONiC switch. The contents of Config DB can be saved in a file */etc/sonic/config_db.json* using the *config save* CLI command. During switch bootup, Config DB is populated with the intended configuration present in the file */etc/sonic/config_db.json*.  Through out this document the term startup-config is used to refer to */etc/sonic/config_db.json*.



When a new SONiC firmware version is installed from ONIE, the newly installed image does not include a *startup-config*. A  startup-config has to be created on first boot. Also when the user upgrades from firmware version A to version B, the startup-config needs to be migrated to the new version B.



SONiC is a collection of various switch applications whose configuration is not always stored in Config DB and hence not present in the startup-config file. First boot configuration creation and configuration migration for these applications also needs to be handled so that all applications in a SONiC switch provide their intended functionalities.



A new service, ***config-setup***, is being proposed to take care of all of the above described activities. Some of the functions provided by the config-setup service are already being handled by *updategraph* service.  As part of proposed changes, functionality dealing with configuration management is moved from *updategraph* to   *config-setup* service. In future, the *updategraph* service can be removed all together and config-setup can be the single place where SONiC configuration files are managed.




## 1.1 Requirements


### 1.1.1 Functional Requirements

1. The config-setup service must provide the ability to generate a configuration when there is none present.

2. The config-setup service must be extensible enough so that any additions to it can be done without requiring any changes to the core service script.

3. It must be backward compatible with the current SONiC configuration generation scheme which makes use of t1/l2/empty config presets.

4. It must support configuration of components beyond the *startup-config* (*/etc/sonic/config_db.json*) file.  Example frr configuration.

5. It should support intermediate reboots during config initialization process. For example, some changes to switch SDK files may require a reboot to get applied to hardware.

6. It must provide infrastructure to support config migration when the user installs a new SONiC firmware version and reboots to switch to the newly installed version.

7. The config-setup service must also take into consideration of other methods of configuring the switch. e.g  ZTP, updategraph.



### 1.1.2 Configuration and Management Requirements
*config-setup* service provides a command line tool */usr/bin/config-setup* which should provide following functionality:

1. Create factory default configuration on first boot
2. Create factory default configuration on demand
3. Take backup of configuration files when a new SONiC firmware version is installed
4. Restore backed up configuration files and apply when user switches from SONiC firmware version A to version B

### 1.1.3 Warm Boot Requirements

When the switch reboots in warm boot mode, *config-setup* service must ensure that config migration steps do not affect warm boot functionality.



## 1.2 Design Overview

### 1.2.1 Basic Approach
*config-setup* service is heavily inspired by the functionality provided by the existing *updategraph* service. config_migration and config_initialization sections of updategraph have been migrated to the new *config-setup* service. Core logic is kept intact and some additional enhancements have been made to consider the case when the ZTP feature is available and if it is administratively enabled or disabled.



In addition to this borrowed functionality, few provisions for user defined extensions (hooks) have been added to perform customizations which are implementation based.



### 1.2.2 Systemd service dependencies
The *config-setup.service* runs after *rc-local.service* and *database.service.* It requires *database.service*. The *updategraph.service* requires *config-setup.service* and runs only after *config-setup.service*. In the future when the *updategraph.service* is removed from SONiC, all systemd services which have dependency on *updategraph.service* will be modified to depend on the *config-setup.service*.



# 2 Functionality
## 2.1 Target Deployment Use Cases
The *config-setup* service is used to perform the following functions:

1. Switch boots a SONiC image which was installed on an empty storage device

2. A new SONiC firmware image is installed and user switches to the newly installed image

3. SONiC switch boots without a startup-config file present


## 2.2 Functional Description

### 2.2.1 Config Setup Hooks

To extend the functionality of the *config-setup* script, users are not expected to modify this script directly. Instead users are required to place executable *hook* scripts in corresponding hooks directory which extend the config-setup script's capability. All executable scripts in the hooks directory are invoked inline using the Bourne shell '.' command. The hook scripts are invoked in lexicographical order of their filenames. The exit status of the hook script is captured in the shell variable *exit_status*. Before each hook is executed,  the shell variable *exit_status* is set to zero. If the hook script succeeded at the task for which it was invoked, the value remains as zero. The hook scripts can modify the value of exit_status to indicate the exit status if any error is encountered.



If required, within a hook script a system reboot operation can be performed. If such an event occurs, the config-setup service will resume executing hooks from the beginning of the hook where reboot was issued. Any hooks previously executed will not be executed again. The satisfying condition for initiating a reboot and the reboot operation itself is not implemented in the config-setup service. Instead this logic needs to be implemented as part of the hook script.



In the following sections, supported hooks for including user extensions are described.



### 2.2.1 Config Initialization

When a SONiC switch boots, the config-setup service starts after the database service exits. It detects if the switch is booting for the first time by checking for existence of the */tmp/pending_config_initialization* file.

At this point if ZTP is enabled, config-setup service loads the ZTP configuration into Config DB and continues the rest of the boot process. ZTP service will then perform provisioning data discovery and download the configuration for the switch. If updategraph is enabled, *config-setup* services exits and lets updategraph handle the rest of config initialization steps.



If both ZTP and updategraph are not enabled, config-setup service is responsible to create a factory default startup-config for the switch and load it to Config DB.



#### Factory Default Config Hooks

Hooks Directory: */etc/config-setup/factory-default-hooks.d*

If defined by the user these are executed during factory default config creation step of *config-setup* service. User can choose to create the startup-config file, frr configuration files and any other files using pre-defined logic. Various application packages can install hooks with logic to create their own factory default configuration, config-setup service will execute these recipes.



Below is an example hook to generate a config_db.json using a pre-defined factory default template file.

*/etc/config-setup/factory-default-hooks.d/10-02-render-config-db-json*

```bash
#Render the config_db.json.j2 template if defined
CONFIG_DB_TEMPLATE=/usr/share/sonic/templates/config_db.json.j2

PLATFORM=sonic-cfggen -H -v DEVICE_METADATA.localhost.platform
PRESET=(head -n 1 /usr/share/sonic/device/$PLATFORM/default_sku)
HW_KEY=${PRESET[0]}

if [ -f ${CONFIG_DB_TEMPLATE} ] && [ "${HW_KEY}" != "" ]; then
    sonic-cfggen -H -k ${HW_KEY} -p -t ${CONFIG_DB_TEMPLATE} > /etc/sonic/config_db.json
    exit_status=$rv
fi
```



#### Preset Configuration

If a startup-config was not created by the factory default hooks, config-setup service uses the config preset defined in the /usr/share/sonic/device/*$platform*/*default_sku* file. This is the current observed functionality provided by the updategraph service.



### 2.2.2 Config Migration

The user can use the *sonic_installer* utility to install a new version of SONiC firmware. As part of this procedure, sonic_installer takes a backup of all files in the directory */etc/sonic* and copies them as */host/old_config*. The *config-migration-pre-hooks* and the *config-migration-post-hooks.d* which are described in the subsequent sections are also backed up as part of this action.

Later when the switch boots into the newly installed image, a file */tmp/pending_config_migration* is created by rc.local service and config-setup service detects its presence and starts the configuration migration action. The backed up files in /host/old_config are then restored to /etc/sonic directory and the restored startup-config file is loaded to Config DB. The *config-migration-pre-hooks* and the *config-migration-post-hooks.d* are also restored to the newly installed image from their backed up copies. If the new image contains files in the migration hooks directories with the same filename as the backed up files, they are not overwritten and instead the files in the newly installed image will be used for configuration migration. This allows the newer version images to provide updated migration logic and deprecate the older (possibly incorrect) implementations.



#### Config migration Hooks - Pre (Take Backup)

Hooks Directory: */etc/config-setup/config-migration-pre-hooks.d*

Config migration hooks provides various applications the ability to extend their config migration step and define their own backup scripts that are invoked when */etc/sonic* is backed up by the *sonic_installer install* command. Based on the specific requirement of an application, the corresponding config-migration-pre-hook  for the application implements the appropriate recipe. These hooks can also be invoked by using the "config-setup backup" command. After the *sonic_installer install* command is executed, the *config-setup backup* command is automatically invoked which in turn executes the *Config migration Hooks - Pre* scripts. 



Below is an example hook to take a copy of the known ssh hosts file.

*/etc/config-setup/config-migration-pre-hooks.d/backup-known-ssh-hosts*

```bash
#Take a backup of known_hosts file for all users
mkdir -p /host/ssh_config
for user in $(ls -1 /home); do
    if [ -e /home/${user}/.ssh/known_hosts ]; then
        cp /home/${user}/.ssh/known_hosts /host/ssh_config/${user}_known_hosts
    fi
done
```

The *Config migration Hooks - Pre* are intended solely for the end user to add their own scripts as part of post deployment provisioning; these scripts are not to be included as part of the  SONiC installer image, *sonic-xxx.bin*. Instead, it is encouraged to use the *installer migration hooks* described in the next section to be part of the SONiC installer image, *sonic-xxx.bin*.

#### Installer migration Hooks - Pre (Take Backup)

Hooks Directory: */var/run/config-setup/installer-migration-hooks*

The functionality of installer migration hooks is similar to the Config migration Hooks - Pre. These hooks are also used to perform configuration backup actions and are executed
by the *config-setup backup* command after executing the hooks defined in */etc/config-setup/config-migration-pre-hooks.d*. These hooks are packaged as part of the SONiC installer image, *sonic-xxx.bin*, and are copied to the */var/run/config-setup/* as part of the SONiC installer image extraction step. As the installer migration hooks are part of the newly installed image, they are easier to provide new configuration backup functionality and fix any migration issues exposed by the hooks defined in * Config migration Hooks - Pre*.



The installer migration hooks corresponding to an installed SONiC image are available in the directory */host/image-<build_version>/installer-migration-hooks*. If the *config-setup backup* is executed explicitly by the user and the  */var/run/config-setup/installer-migration-hooks* directory is not present, the hooks present in the directory */host/image-<build_version>/installer-migration-hooks* of the current active image are executed. This allows the user to perform manual config backup and restore operation on the current active image. If the  */var/run/config-setup/installer-migration-hooks* directory  exists, it will take precedence as it indicates that the user has just installed a new image and is preparing to boot into the newly installed image.



#### Configuration Backup Flowchart

![](images/Config_Backup.png)


#### Config migration Hooks - Post (Restore Backup)

Hooks Directory: */etc/config-setup/config-migration-post-hooks.d*

These hooks are executed by the config-setup service when switch boots into a newly installed image and also a snapshot of backed up configuration files are found in /host/old_config directory. Applications can install corresponding hooks in the *config-migration-post-hooks.d* directory, which restore the files that were backed up in the *config-migration-pre-hooks*.



Below is an example hook to restore known ssh hosts file from a backed up files.

*/etc/config-setup/config-migration-post-hooks.d/restore-ssh-known-hosts*

```bash
#Restore known_hosts file for all users
if [ -d /host/ssh_config ]; then
    for user in $(ls -1 /home); do
        if [ -e /host/ssh_config/${user}_known_hosts ]; then
            cp -f /host/ssh_config/${user}_known_hosts /home/${user}/.ssh/known_hosts
        fi
    done
fi
# Remove the copy
rm -rf /host/ssh_config/*_known_hosts
```



#### Configuration Restore  Flowchart

![](images/Config_Restore.png)

### 2.2.3 Config Detection

On every switch bootup, the config-setup service starts and detects if a startup-config file is present or not. If startup-config does not exist, config Initialization action is performed. Please refer to the section 2.2.1 for more details.



### 2.2.4 ConfigDB Migration

The configuration state of a SONiC switch is stored in a file */etc/sonic/config_db.json* in JSON format. During bootup, the contents of the */etc/sonic/config_db.json* are loaded into the RedisDB which is called ConfigDB. When installing a new SONiC image, the saved contents are migrated over to the new image. This section describes how the stored configuration file */etc/sonic/config_db.json* is transformed and migrated such that it works across different SONiC releases.



#### 2.2.4.1 ConfigDB Version

The contents of the ConfigDB are assigned a version using the following format:

```
Table: VERSIONS
Key: DATABASE
Field: VERSION
Value: version_x_y_z
```

**RedisDB dump**

```
128.127.0.0.1:6379[4]> hgetall VERSIONS|DATABASE
1) "VERSION"
2) "version_1_0_2"
127.0.0.1:6379[4]>
```

**JSON Representation**

```
"VERSIONS": {
    "DATABASE": {
        "VERSION": "version_1_0_2"
    }
}
```



In the Enterprise SONiC distributed by Broadcom, the following policy is implemented while assigning ConfigDB version numbers. 

**Format:      version\_&lt;major-number&gt;\_&lt;minor-number&gt;\_&lt;db-version-id&gt;**

 **major-number** : Same as the major Enterprise SONiC release, the code version belongs to. E.g 3, 4. This will act as a quick reference to the major Enterprise SONiC release where this ConfigDB version was introduced.

**minor-number** : Same as the minor Enterprise SONiC release, the code version belongs to. E.g 1 in 3.1, 0 in 3.0.5. This will act as a quick reference to the  minor Enterprise SONiC release version where this ConfigDB version was introduced.

**db-version-id** : A number starting from 1 which is serially incremented with every ConfigDB version change within a *&lt;major-number&gt;\_&lt;minor-number&gt;* series.



*Note: The above versioning policy will be implemented starting from Enterprise SONiC release 3.1 onwards. The ConfigDB versions from the previous releases (e.g version_1_0_1, version_1_0_2) will be left as is.*



The ConfigDB versions used by Enterprise SONiC will not align with the ConfigDB versions used by the community SONiC. As a result any attempts of configuration migration to/from Enterprise SONiC from/to community SONiC will likely fail. It is advised that users can either use the --skip_migration option when installing a SONiC image or install the image from the ONIE prompt.



#### 2.2.4.2 ConfigDB Version Registry

The ConfigDB version registry is used to identify the ConfigDB versions supported on a given Enterprise SONiC release version. This version map also serves as a lookup table to determine what is the least Enterprise SONiC version to which the current switch configuration can be migrated to and also what is the latest version supported.



The ConfigDB version registry is stored in a JSON format in the file */etc/sonic/config_db_version_registry.json*. The below is an example.



```
{
  "CONSTANTS": {
    "VERSION_MIN": "version_1_0_2",
    "VERSION_LATEST": "version_4_0_1",
    "VERSION_UNKNOWN": "version_4096_4096_4096"
  },
  "CONFIG_DB_VERSIONS": {
    "version_1_0_2" {
       config : null,
       data : null
    },
    "version_3_1_1" {
    },
    "version_3_1_2" {
    },
    "version_3_2_1" {
    },
    "version_4_0_1" {
    }
  }
}
```



**CONFIG_DB_VERSIONS** - A dictionary used to store the least supported ConfigDB versions. It can also optionally store the *config* snapshot of ConfigDB contents corresponding to that version and a private data dictionary to store abstract *data* used for transformation logic.

**VERSION_LATEST** - The latest ConfigDB version that is currently known to be in use.

**VERSION_MIN** - The minimum ConfigDB version that is supported.

**VERSION_UNKNOWN** - A version to be assigned when an unknown version is detected.

If a ConfigDB version is not found in an */etc/sonic/config_db.json* file, it is assumed to be equal to *VERSION_MIN*. If a ConfigDB version is not found in the Redis Config DB instance, it is assumed to be equal to *VERSION_LATEST*.



For a quick reference, the latest ConfigDB version supported in an Enterprise SONiC image is available in the */etc/sonic/sonic_version.yml*. A copy of the */etc/sonic/sonic_version.yml* is also available in the */host/image-&lt;sonic version&gt;/sonic_version.yml* to be accessible to all installed SONiC images.



#### 2.2.4.3 ConfigDB Version Upgrade

During a switch bootup, the contents of the */etc/sonic/config_db.json* file are loaded into ConfigDB. After populating the ConfigDB, the contents of the ConfigDB are checked for version compatibility. This ensures that all applications consume data in an expected format. 



A compatible version must be:

- Defined in the version registry

- &lt;= *VERSION_LATEST*

- &gt;= *VERSION_MIN*

No additional restrictions are expected to be placed while upgrading from a lesser version of ConfigDB to a larger (newer) version of the ConfigDB contents. Any such restrictions advertised in the Release notes of a certain version of Enterprise SONiC should be treated as exceptions.



The supported version checks are performed by the *db_migrator.py* tool which is provided by the *python-sonic-utilities* package. The *db_migrator.py* reads the contents of the ConfigDB and performs the necessary transformations of the ConfigDB contents using the defined migration APIs defined in the tool.  A newer version of ConfigDB may contain a different table/key layout compared to the older version which was read from the *config_db.json* and these transformations are used to perform these migrations. The goal of these transformations is to make it compliant to the *VERSION_LATEST*.



During SONiC configuration load operations, the ConfigDB version of the */etc/sonic/config_db.json* file is first checked for compatibility before loading  to the ConfigDB.  If the migration APIs or version compatibility checks fail, the configuration load operation is aborted.



#### 2.2.4.4 ConfigDB Version Downgrade

Consider the case where a user is currently on the latest version of SONiC (v3.1) which is using *version_3_1_1* of ConfigDB. The user plans to migrate to an older version of SONiC (v3.0) which uses an older version of ConfigDB, *version_1_0_2*. For a seamless transition from SONiC v3.1 to SONiC v3.0. it is required that the contents of the saved configuration file *config_db.json* are transformed(downgraded) to a compatible version (*version_1_0_2*). The knowledge of the ConfigDB *version_3_1_1* is available only with the *db_migrator.py* of SONiC v3.1. The intended target ConfigDB version information (*version_1_0_2*) in some scenarios is available only after the SONiC v3.0 image boots.



 The *db_migrator.py* was originally designed to perform ConfigDB migration from an older version to a newer version. To allow downgrading of a ConfigDB from a newer version to an older version, the *db_migrator.py* tool is enhanced to support the following:

- Migration API's to transform ConfigDB from Version_N to Previous(Version_N) and Version_N to Next(Version_N)
- Migration API's to transform config_db.json from Version_N to Previous(Version_N) and Version_N to Next(Version_N)



*Note: Previous(Version_N) and Next(Version_N) are indicative references to a valid config db version number which will be used as a step to reach a final target version. The next version number may not necessarily be the next sequential version. The next step version chosen will depend on the resolved migration path as explained in the Section Version Migration Path.*



When a SONiC image is installed, the current */etc/sonic/config_db.json* is saved into the */host/old_config* directory to be accessed by the installed image when it starts for the first time. Since the new image's ConfigDB version may be different from the version of */etc/sonic/config_db.json* necessary transformations need to be done on its contents to make it compatible with the new ConfigDB version. The following flowchart and pseudocode explain how a configuration backup operation is performed.

![DB Migration](images/db_migrator_downgrade.png)





```
function migrate_backup(input: version, target_version
                        output: best_effort_config_db, min_version_config_db, registry) {
      # Read config DB contents
     config_db = read('/etc/sonic/config_db.json')
      # Resolve best effort path and minimum version path
      (best_path, min_path) = rgstry.get_db_migrate_backup_path(version, target_version)
      for path in [best_path, min_path] {
          if (path != null) {
              tmp_config_db = dup(config_db)
              # Execute migration APIs on config db contents for each successive pairs of
              # versions defined in the resolved path lists
              for (idx = 0; idx < len(path); idx++) {
                  ver = path[idx]
                  if (idx > 0) {
                      prev_ver = path[idx-1]
                      migration_api = "migrate_{}_to_{}".format(prev_version, version)
                      res = migrate_prev_ver_to_ver()
                      if (res != SUCCESS && path == best_path) {
                         # Fatal operation
                         exit("Failed to execute migration API")
                      } else if (res != SUCCESS && path == min_path) {
                         # Stop migration efforts but proceed with already migrated contents
                         log("Failed to execute migration API during backup to minimum version")
                         break
                      }
                  }
                  # Update config_db with the new version
                  tmp_config_db.set_version(ver)
                  # Save current config db contents in the registry
                  rgstry[ver].save(ver, tmp_config_db)
              }
              # Save transformed config db contents to be used by the new image
              if (path == best_path) {
                save(tmp_config_db, "/host/old_config/config_db.json")
              } else {
                save(tmp_config_db, "/host/old_config/min_config_db.json")
              }
          }
      }
      # Save the registry to be migrated to the new image for reference
      rgstry.save("/host/old_config/prev_config_db_version_registry.json")
}
```



#### 2.2.4.5 ConfigDB Version Migration Process

When a SONiC image is installed,  the configuration migration scripts are executed as described in the [section 2.2.2 Config Migration](#222-config-migration). As part of the configuration backup step, the *config_db.json* is downgraded to match the ConfigDB version of the newly installed image. If the target ConfigDB version is *unsupported*, then configuration backup process is aborted and none of the switch configuration is migrated to the newly installed image. In the case where the target ConfigDB version is not defined or is in an invalid format, the target ConfigDB version is assumed to be VERSION_MIN.



If the user wants to perform a warm-reboot as part of a new SONiC image installation, the *db_migrator.py* tool is used to backup the ConfigDB contents in the RedisDB instance before saving them into a RDB file. During the new image bootup, the RDB file contents are restored and are expected to be in a compatible format.



It is important to note that the ConfigDB downgrade process executes on the image where a new image is being installed. The idea is to prepare (downgrade) the backup configuration such that when the installed image boots, the configuration is in a compatible format. If  any error is observed while processing the ConfigDB data during bootup of the new image, the switch uses factory default configuration. If the ZTP feature is available and enabled, the switch starts ZTP discovery to download the switch configuration. This allows the switch to be online and accessible to the user at all times.



 The below flow diagram explains the configuration migration flow during a SONiC image installation.



![ConfigDB_VersionMigration](images/ConfigDB_VersionMigration.png)



#### 2.2.4.6 Version Migration Path

The ConfigDB version registry provides a list of versions that are supported by a SONiC image. The list of supported versions will grow as per the needs of the Enterprise SONiC product during its lifetime. Certain ConfigDB versions may be introduced in certain older releases of Enterprise SONiC on a need basis and not necessarily in the serial order of their version number. For example, the maintenance release 3.0.8 may have been released after the 3.1.2 maintenance release and both these releases introduced a new ConfigDB version. Also with an increasing number of supported versions, the many to many combination of possible image migrations increases by many fold. 

- Many to many migration combinations
- ConfigDB versions introduced in a nonlinear time order

To solve the above identified complexity, a well-defined and deterministic algorithm is being proposed to identify a migration path between any two supported ConfigDB versions in the version registry.



**Incremental Migration APIs**

The migration APIs are defined to only between two consecutive ConfigDB versions of the same Major.Minor release and between the two least versions of every supported Major.Minor release. Consider the below example,  incremental migration APIs are defined only between the connected versions. *version_3_1_3* to *version_4_1_1* is not defined. This allows the versions to be reachable to each other while also reducing the number of such migration APIs to be defined. It is to be noticed that in some scenarios the path between two versions may not be the shortest. It is a general understanding that individual maintenance releases may not introduce changes to ConfigDB schema changes and thus require to introduce a new maintenance ConfigDB version.

![Version Registry Map](images/db_registry_map.png)



Each incremental migration API is defined as a function *migrate&lt;version&gt;to_&lt;next_version&gt;()* in the *DBMigrator* class of the *db_migrator.py* tool. E.g migrate_version_3_1_1_to_version_4_1_1().

**Recovering Lost Data**

Every time a migration API is executed, the ConfigDB content is modified. This may result in some table(s)/key(s)/field(s) to be added/removed/replaced and thus result in a loss of data. Especially when the migration path is longer than expected. For example when moving from *version_6_1_3* to *version_3_1_3*, some of the data that was in *version_6_1_3* may be required for the migration API between *version_3_1_2* and *version_3_1_3*. To recover the lost data, before performing any transformations, the current snapshot of the ConfigDB contents is saved in the version registry in the corresponding version object's config dictionary. The migration APIs further down the migration path, can implement logic to retrieve the saved configuration from the version registry and use it to perform a transformation which can reinstate the lost information.



**Configuration Backup operation**

During a configuration back operation, the from version is assured to be a known version, but the target version may or may not be a known version. To draw a migration path to an unknown version can be non-trivial and also may have multiple possibilities. To make it more deterministic, two possible paths are resolved as explained below

**Best Effort Path:** It is the optimal path from a chosen version to a known target version or a lesser and closer version to an unknown target version.

**Minimum Version Path:** It is the optimal path from chosen version to VERSION_MIN.

Two copies of Config DB contents (best effort version, minimum path version) are prepared and transferred over to the newly installed image. The registry holding the saved ConfigDB contents for each of the versions defined in the best effort path and minimum version path is also transferred over to the newly installed image for reference.



**Configuration Restore operation**

When the newly installed image boots, if the best effort version exists in the version registry, the best effort config db json contents are loaded to the ConfigDB and migrated to the VERSION_LATEST of the new image. If the best effort version is not known, the minimum version config db json is loaded and migrated to the VERSION_LATEST. The migrated registry file from the previous image is also loaded and provided as an input argument to the migration APIs to recover lost data.



**Upgrade path:** It is the optimal path from either of the best effort version or minimum version to VERSION_LATEST.



If both the best effort version and minimum version are not supported in the new image or if there is any failure while executing migration APIs, the switch boots up with factory default configuration.



#### 2.2.4.7 ConfigDB Migration Examples

This section describes various scenarios that may happen when migrating between ConfigDB versions across different Enterprise SONiC releases.



![Migration Scenarios](images/DB_version_map_1.png)



![Migration Scenarios](images/DB_version_map_2.png)



![Migration Scenarios](images/DB_version_map_3.png)



![Migration Scenarios](images/DB_version_map_4.png)



![Migration Scenarios](images/DB_version_map_5.png)



![Migration Scenarios](images/DB_version_map_6.png)



![Migration Scenarios](images/DB_version_map_7.png)



![Migration Scenarios](images/DB_version_map_8.png)



![Migration Scenarios](images/DB_version_map_9.png)



![Migration Scenarios](images/DB_version_map_10.png)



![Migration Scenarios](images/DB_version_map_11.png)



![Migration Scenarios](images/DB_version_map_12.png)

#### 2.2.4.8 Guidelines to developers

This section provides guidelines for the developers to help design incremental migration APIs

- The ConfigDB migration must be used only for table schema changes and should not be used to introduce an applications default configuration values. To introduce default configuration values the below the changes must be made instead:
  - Factory default configuration template (organization_extensions/vendor/broadcom/config_profiles/&lt;*profile*&gt;/templates/config_db.json.j2)
  - Individual application requiring the ConfigDB contents should assume default values and if required write contents to the ConfigDB when the application is starting up
- The migration APIs need to be carefully crafted so that they always return success.  There may be situations where, more than one migration APIs will attempt to perform the same ConfigDB migration logic. Such situations need to be handled and not throw an error. Failure scenarios should be avoided. This will reduce the unpredictability when migrating across many versions.
- The migration APIs need to be designed to operate both on ConfigDB connector as well as a config_db.json dictionary

#### 2.2.4.9 Restrictions

- ConfigDB version between Enterprise SONiC daily builds of a given release is not guaranteed. This is to reduce the number of ConfigDB versions in a given release thus reducing the number of incremental migration APIs that need to be coded. The side effect of this choice is that during the course of a release development, there will be instances where some upgrade/downgrade steps fail. SQA and developers are suggested to take corrective steps to erase the saved configuration and proceed with their work.
- ConfigDB version 1_0_1 used in the Enterprise SONiC release 2.x(Arlo and Arlo+) will not be supported for configuration migration. Users are advised to skip configuration migration and use ZTP during upgrade from Arlo/Arlo+ to Buzznik+ release.
- Upgrade from from Buzznik+ EA builds provided to the customers to the latest Buzznik+ builds which include these proposed changes will result in the switch come up with factory default configuration. Configuration migration will not be supported.
- Consider the case where Dynamic Port Breakout configuration is added by the user in a Buzznik+ image. When the configuration is downgraded to Buzznik-MR release, ConfigDB migration does not happen and the Buzznik-MR image comes up with factory default configuration. This restriction is being imposed because it is not possible to accurately identify the mapping between Buzznik+ breakout configuration with static breakout configuration used in the Buzznik-MR release.



## 2.3 CLI
Following are the commands supported by the config-setup tool. These are Linux shell commands.



***config-setup factory***

This command is executed to create factory default configuration. Please refer to the section 2.2.1 for more details. Note that this command simply creates a configuration and does not load it into Config DB. It is up to the calling entity to execute either 'config reload' or switch reboot for the configuration to take effect.



***config-setup backup***

This command is executed to take a backup copy of SONiC configuration files that need to be migrated over when a new firmware image is  installed and booted in to. Please refer to section 2.2.2 for more details.



***config-setup boot***

This command is executed as part of system bootup by the config-setup service. Users must not execute this command on Linux shell even though it is possible to. The actions performed  by this command are described in section 2.2.



# 3 Unit Tests

1. Install SONiC firmware image on an empty disk using ONIE or similar bare metal provisioning tool. Verify that a factory default configuration is created.

2. Delete startup-config and reboot the switch. Verify that a factory default configuration is created.

3. Install a new SONiC firmware image using the "sonic_installer install" command and reboot the switch to boot into the newly installed image. Verify that the startup-config file used in the old SONiC firmware is restored to be used by the new image.

4. When ZTP is enabled, verify that the ZTP configuration is loaded when the SONiC switch boots without a startup-config. Factory default config does not get created in this case.

5. Verify that the updategraph service takes over the config creation role if it is enabled and SONiC switch boots without a startup-config.

6. Verify that the user specified config-setup-factory hooks are executed when the switch boots without a startup-config.

7. Verify that the user specified config-migration-pre hooks are executed when a new SONiC firmware image is installed.

8. Verify that when the switch boots into a newly installed SONiC image, the user specified config-migration-post hooks are executed.

9. Verify that the exit status of the user defined hooks is correctly read and reported by the config-setup service in syslog. If the hook script is syntactically incorrect, it will be reported with failed exit status.

10. When the switch boots with a saved startup-config, verify that the config-setup service does not perform any additional actions.

11. Verify that the user can execute the "config-setup factory" command manually without requiring a switch reboot.

12. Verify that the user can execute the "config-setup backup" command multiple times so that any new configuration file changes are picked up again.

13. Verify that when a config-setup hook script issues a switch reboot, all the hook scripts previously executed are not executed again upon subsequent  switch boot.

14. Verify that config-migration hook scripts are executed even when the switch boots in warm boot mode. This will allow scripts to perform any actions required for some applications which are not covered by the warm boot functionality.

15. Verify that config-setup-factory hook scripts are not executed when switch boots in warm boot mode.

16. Verify that the config-migration-pre and config-migration-post hook scripts are automatically migrated to the newly installed image.

17. Verify that the config-migration-pre and config-migration-post hook scripts migrated to the newly installed image do not overwrite hook scripts with same name in the newly installed image.

18. Verify that the installer migration scripts provided as part of the SONiC installer image are executed after the executing the hooks defined in config-migration-pre. This action is performed by the *config-setup backup* command.

19. Verify ConfigDB downgrade migration across two major releases where there is a ConfigDB version change

20. Verify ConfigDB downgrade migration across two manor releases within a major release where there is a configDB version change

21. Verify ConfigDB downgrade migration between daily builds across two different releases where there is a configDB version change

22. Verify that when ConfigDB downgrade migration fails, the switch boots with a default configuration when ZTP is disabled or switch performs ZTP discovery if ZTP is enabled

23. Verify that the ConfigDB downgrade migration is restricted by the minimum version specified in the ConfigDB map file

24. Verify all scenarios mentioned in the [section  2.2.4.7 ConfigDB Migration Examples](#2247-configdb-migration-examples)

    

# 4 Appendix

This section provides example extensions of the Configuration Setup service to perform configuration migration in a SONiC switch.

## 4.1 Linux Users, Groups and Passwords Migration

When a new SONIC image is installed, a new Linux root filesystem is created. As a result, any changes made in the Linux root filesystem are not carried forward to the newly installed image. Only specific contents in the /etc/sonic directory (e.g config_db.json, frr config) are migrated to the new image as part of config migration. Simple changes in Linux, such as change of admin user password or Unix group membership, are not transferred over as part of config migration.

The config-setup migration hooks infrastructure can be used to migrate following information from existing image to newly installed image:
1. Newly created non-system users (GID >= 1000)
2. Password for the newly created users and the admin user
3. Newly created user groups
4. /home directory which has all user home directories
5. User email 

When a new SONiC switch image is installed using the "sonic_installer install" command, the /var/lib/config-setup/installer-migration-hooks/01-local-users-pre script extracted from the new SONiC image is executed. It takes a backup copy of all of the files listed below and copies them to /host/backup/users_info directory.

```
/etc/passwd
/etc/group
/etc/shadow
/etc/gshadow
/home/*
/var/spool/mail
```

After the SONiC switch is rebooted to boot into the newly installed image, the /etc/config-setup/config-migration-post-hooks.d/01-local-users-post script is executed which reads the files in /host/backup/users_info directory and migrates contents of the backed up data to the Linux root filesystem of newly installed user. After migrating the user data, the directory /host/backup/users_info is removed.
