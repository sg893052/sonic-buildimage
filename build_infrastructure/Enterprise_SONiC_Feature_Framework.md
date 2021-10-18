# Enterprise SONiC Feature Framework

Version 0.1

## Table of Contents

1. [Revision History](#revision-history)
2. [Objectives](#objectives)
3. [Enterprise SONiC Features and Components](#enterprise-sonic-features-and-components)
   1. [Components](#components)
   2. [Features](#features)
   3. [Feature Configuration](#feature-configuration)
   4. [Component Presence Checks](#component-presence-checks)
4. [Generic Package](#generic-package)
5. [Optional Click Commands](#optional-click-commands)
6. [Guidelines for SONiC Developers](#guidelines-for-sonic-developers)

## Revision History

| Version | Date       | Author             | Change Description                    |
| ------- | ---------- | ------------------ | ------------------------------------- |
| 0.1     | 10/14/2021 | Rajendra Dendukuri | Initial draft. Used for 4.0.0 release |



## Objectives

1.  Define a process to classify various SONiC applications developed for Enterprise SONiC
2.  Define a process to allow certain SONiC applications to be included or excluded from an Enterprise SONiC package
3.  Define a method to check if a certain application is included or excluded in an Enterprise SONiC package. This decision needs to be made available at runtime.



## Enterprise SONiC Features and Components

As of release 4.0.0, Enterprise SONiC is available in the following packaging

- Enterprise Advanced
- Enterprise Base
- Cloud Advanced
- Cloud Base
- Campus

Each of these packages include a specific list of SONiC applications as per the Enterprise SONiC marketing and engineering teams inputs. The list of applications supported is available in the release documentation and may change in future.



### Components

A component in Enterprise SONiC is defined as a SONiC application which is packaged as a self-sufficient docker container. A component is included or excluded from a SONiC package by including or excluding the corresponding docker container.

Example: l2mcd, udld, stp, nat

The components which are supposed to exhibit different capabilities based on the SONiC package, the docker container image uses a different name to uniquely identify itself.

Examples: docker-syncd-brcm-ent-advanced, docker-sonic-mgmt-framework-campus, docker-swss-brcm-cld-advanced

This allows multiple SONiC packages to be compiled using the same *sonic-buildimage* repository.



All components must define an INCLUDE_XXX=y option in rules/config. This allows build time decisions to include or exclude a certain component.

```
rules/config
------------
# INCLUDE_SFLOW - build docker-sflow for sFlow support
INCLUDE_SFLOW = y

slave.mk
--------
export include_sflow="$(INCLUDE_SFLOW)"
```



The supported list of components are defined in the */etc/sonic/init_cfg.json* as keys in the FEATURE table. This information is also available in the ConfigDB. All components, except database, swss and syncd, are assumed to optional and need to be implemented with that assumption.



The host Debian o/s is also considered as a component even thought it is not a docker container.




### Features

An Enterprise SONiC feature is an application which is a part of one or more components. Thus by definition it can also be a part of the Debian host o/s.

Example: PoE, Multi-site DCI, DHCP Snooping, Tail stamping, hostcfgd



A feature may be packaged as a debian (.deb) or a wheel(.whl) package. The feature is included or excluded based on the corresponding package inclusion in the docker. In some scenarios, the feature package is included in all SONiC packages and based on the feature configuration of the Package it is decided at SONiC runtime if the feature is active and usable.



Application developers introducing a feature should plan ahead of time to ensure that a feature can be easily excluded at compile time (best case) or at the minimum using a run time check. This enables the marketing team and also the engineering team to defer the decision of a feature's support to a future date. There can be varied customer requirements and also unforeseen device resource constraints (low memory, less disk space) in future.



Minor customizations to SONiC can also be named as a feature. For example, SNMP statistics capability is disabled in Campus package to reduce memory usage. This allows for a possible exclusion of SNMP statistics feature from other packages in future.



#### Feature Configuration

Enterprise SONiC features whose code cannot be excluded from a SONiC package at build time can use a *feature configuration* to decide at runtime if the feature is active or not. The *feature configuration* is made available using *is feature present check* APIs as defined in the *featurecfg* debian package.

Each feature is assigned a unique feature ID and is defined in the file *src/broadcom-exclusive/src/featurecfg/common/src/brcm_package.h*.

```
typedef enum _brcm_feature_t {
    FEATURE_START                     = 0,
    FEATURE_L2_PVST                   = 1,
    FEATURE_EVPN_CONTROL_PLANE        = 2,
    FEATURE_IFA_1_1                   = 4,
    FEATURE_CHEF_FOR_EVPN             = 5,
    FEATURE_FRR_BGP_EVPN_MLAG         = 6,
    FEATURE_L2_VXLAN                  = 7,
    FEATURE_L2_VXLAN_SCALE            = 8,
    FEATURE_L3_VXLAN                  = 9,
    FEATURE_L2_LVTEP                  = 10,
    FEATURE_L3_LVTEP                  = 11,
    FEATURE_LINUX_PTP                 = 12,
    FEATURE_IPV4_PIMSSM               = 13,
    FEATURE_RPVST_PLUS_MLAG           = 14,
    FEATURE_PVST_MLAG                 = 15,
    FEATURE_TAILSTAMPING              = 16,
    FEATURE_MOD                       = 17,
    FEATURE_MULTI_SITE_DCI            = 18,
    FEATURE_ICMP6_PTB_HASHING         = 19,
    FEATURE_DHCP_SNOOPING             = 20,
    FEATURE_POE                       = 21,
    FEATURE_SNMP_STATS                = 22,
    FEATURE_MAX
} brcm_feature_t;

```



Applications can link to *libbrcmpackage.so*  and make use of the *brcm_is_feature_supported()* API to check if a feature is present in the package or not. It is recommended that applications which may require multiple feature present checks use this API once to obtain the information and cache the value in a  global variable and use it for future reference within the application. This limits the exposure to future changes to the *brcm_is_feature_supported()* API. A Make variable FEATURECFG_SUPPORT is set to 1 if the feature configuration support is available. This variable can be used to optionally link to the libbrcmpackage.so usage and also inject appropriate #ifdef's to be used in the code. See the below below example usage in the swss component.



```
bash-4.2$ pwd
/projects/csg_sonic/rd925731/repo/dev/sonic-400-335/src/sonic-swss
bash-4.2$ git grep FEATURECFG
cfgmgr/Makefile.am:if FEATURECFG_SUPPORT
cfgmgr/Makefile.am:vxlanmgrd_CFLAGS += -DFEATURECFG=$(FEATURECFG_SUPPORT)
cfgmgr/Makefile.am:vxlanmgrd_CPPFLAGS += -DFEATURECFG=$(FEATURECFG_SUPPORT)
cfgmgr/vxlanmgr.cpp:#ifdef FEATURECFG
cfgmgr/vxlanmgr.cpp:#ifdef FEATURECFG
cfgmgr/vxlanmgr.cpp:#else /* FEATURECFG */
cfgmgr/vxlanmgr.cpp:#endif /* FEATURECFG */
configure.ac:AM_CONDITIONAL(FEATURECFG_SUPPORT, test x$FEATURECFG_SUPPORT = x1)
orchagent/Makefile.am:if FEATURECFG_SUPPORT
orchagent/Makefile.am:orchagent_CFLAGS += -DFEATURECFG=$(FEATURECFG_SUPPORT)
orchagent/Makefile.am:orchagent_CPPFLAGS += -DFEATURECFG=$(FEATURECFG_SUPPORT)
orchagent/ipmcorch.cpp:#ifdef FEATURECFG
orchagent/ipmcorch.cpp:#ifdef FEATURECFG
orchagent/main.cpp:#ifdef FEATURECFG
orchagent/main.cpp:#ifdef FEATURECFG
orchagent/main.cpp:#endif /* FEATURECFG */

bash-4.2$ git grep brcm_is_feature_supported
cfgmgr/vxlanmgr.cpp:   rv = brcm_is_feature_supported(FEATURE_L2_VXLAN, &m_is_vxlan_feature_enable);
cfgmgr/vxlanmgr.cpp:     SWSS_LOG_NOTICE("brcm_is_feature_supported FEATURE_L2_VXLAN failed");
cfgmgr/vxlanmgr.cpp:   rv = brcm_is_feature_supported(FEATURE_L2_VXLAN_SCALE, &is_vxlan_scale_enabled);
cfgmgr/vxlanmgr.cpp:     SWSS_LOG_NOTICE("brcm_is_feature_supported FEATURE_L2_VXLAN_SCALE failed");
orchagent/ipmcorch.cpp:    rv = brcm_is_feature_supported(FEATURE_IPV4_PIMSSM, &is_pim_feature_supported);
orchagent/ipmcorch.cpp:        SWSS_LOG_NOTICE("brcm_is_feature_supported FEATURE_IPV4_PIMSSM failed");
orchagent/main.cpp:    rv = brcm_is_feature_supported(FEATURE_L2_VXLAN, &is_vxlan_feature_enabled);
orchagent/main.cpp:        SWSS_LOG_NOTICE("brcm_is_feature_supported FEATURE_L2_VXLAN failed");
orchagent/main.cpp:    rv = brcm_is_feature_supported(FEATURE_L2_VXLAN_SCALE, &is_vxlan_scale_enabled);
orchagent/main.cpp:        SWSS_LOG_NOTICE("brcm_is_feature_supported FEATURE_L2_VXLAN_SCALE failed");
bash-4.2$

```



A python API  *is_present()* is made available as part of the *sonic_py_common* library to check if a feature is supported or not.

```
Python 2.7.16 (default, Oct 10 2019, 22:02:15)
[GCC 8.3.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> from sonic_py_common import device_info
>>> device_info.is_present(feature="LINUX_PTP")
True
>>> device_info.is_present(feature="POE")
False
```



An application, feature-check is also available to be used in bash scripts to check if a feature us supported or not.

```
root@sonic:/home/admin# feature-check -h
usage: feature-check [-h] [-q] [-l] [NAME]

Check capabilities supported by Enterprise SONiC

positional arguments:
  NAME         Capability supported by Enterprise SONiC

optional arguments:
  -h, --help   show this help message and exit
  -q, --quiet
  -l, --list
  
root@sonic:/home/admin# feature-check POE
POE is not supported
root@sonic:/home/admin# echo $?
1
root@sonic:/home/admin# feature-check LINUX_PTP
LINUX_PTP is supported
root@sonic:/home/admin# echo $?
0
root@sonic:/home/admin# feature-check -l
Supported features:
IFA_1_1
LINUX_PTP
TAILSTAMPING
MOD
SNMP_STATS
```



The list of supported features is available in the */etc/sonic/sonic_branding.yml* file. This file can be used as an input to the *sonic-cfggen* and used to render j2 templates based on a features presence. 

```
root@sonic:/home/admin# cat test.j2
{% if 'POE' in features %}
PoE is supported
{% else %}
PoE is not supported
{% endif %}

{% if 'LINUX_PTP' in features %}
LINUX_PTP is supported
{% else %}
LINUX_PTP is not supported
{% endif %}


root@sonic:/home/admin# sonic-cfggen -y /etc/sonic/sonic_branding.yml -t test.j2
PoE is not supported

LINUX_PTP is supported
```



#### Component Presence Checks

Similar to features, following APIs are available for component present checks. Use docker container name to identify the component.

```
Python 2.7.16 (default, Oct 10 2019, 22:02:15)
[GCC 8.3.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> from sonic_py_common import device_info
>>> device_info.is_present(component="stp")
False
>>> device_info.is_present(component="nat")
True
```



For bash applications, use component-check

```
root@sonic:/home/admin# component-check -h
usage: component-check [-h] [-q] [-l] [NAME]

Check capabilities supported by Enterprise SONiC

positional arguments:
  NAME         Capability supported by Enterprise SONiC

optional arguments:
  -h, --help   show this help message and exit
  -q, --quiet
  -l, --list
root@sonic:/home/admin# component-check nat
nat is supported
root@sonic:/home/admin# component-check stp
stp is not supported
root@sonic:/home/admin#
```



The list of available components is available in the */etc/sonic/init_cfg.json* file.



## Generic Package

Each supported packages of Enterprise SONiC is compiled using a specific target image name

- Enterprise Advanced - target/sonic-broadcom-enterprise-advanced.bin
- Enterprise Base - target/sonic-broadcom-enterprise-enterprise.bin
- Cloud Advanced - target/sonic-broadcom-cloud-advanced.bin
- Cloud Base - target/sonic-broadcom-cloud-base.bin
- Campus - target/sonic-broadcom-campus.bin



To enable source code customers and development partners, a generic SONiC image is supported which includes all features and components. Certain proprietary and associated packages may not be available but rest of the features can be made available. The *target/sonic-broadcom.bin* is used to build an unbranded image.



When building an unbranded image:

- *files/build_templates/init_cfg.json.j2* must include all components
- Applications linking to *libbrcmpackage.so* should consider a scenario where the *featurecfg* deb package is not available
  - Use FEATURECFG_SUPPORT = 0 / 1make variable to check if libbrcmpackage.so is available or not
- feature check python APIs and feature-check command always return success
- component-check and is_present(component=xx) continue to work and refer to the content of /etc/sonic/init_cfg.json. They return True/False based on the input component's presence

To test building an unbranded image, remove the *src/broadcom-exclusive* directory and build the image *target/sonic-broadcom.bin*.



## Optional Click Commands

The sonic_py_common.device_info.is_present() API can be used to hide unsupported Click CLI commands. It can also be used to report an error when unsupported arguments are used. This API doesn't depend on other components like the database and thus can be used in Click commands without any risk of a race condition.



## Guidelines for SONiC Developers

- Identify if your application is a component and requires a separate docker container or can be made part of an existing container.
- Do not create a new container unless it is absolutely necessary. Each new container creates additional resource (CPU, memory, disk) overhead and creates a negative effect on low end platforms.
- Check with the Enterprise SONiC marketing team if your feature is an optional feature. If yes
  - Create an enum entry in the "*src/broadcom-exclusive/src/featurecfg/common/src/brcm_package.h*" file
  - If required, create an appropriate enum in the SAI code *ocp_sai/include/brcm_sai_feature.h*
  - Use FEATURE_XXX naming notation and always include them between FEATURE_START and FEATURE_MAX
  - Modify appropriate instances of  *src/broadcom-exclusive/src/featurecfg/package-name/src/brcm_package.cpp* to include feature in the feature configuration of that package
- Define package variant specific docker images for components which exhibit different behavior on include different packages based on package variant
  - e.g *src/broadcom-exclusive/rules/docker-swss-brcm-campus.mk* and *src/broadcom-exclusive/dockers/docker-swss-brcm*
- Include or exclude components from the package installer
  - e.g "src/broadcom-exclusive/installers/campus.mk" see usage of SONIC_CAMPUS_DOCKERS_EXCLUDED
- Include or exclude components from the init configuration
  - Add conditional entries in *files/build_templates/init_cfg.json.j2*
- Add dependencies to include the FEATURECFG_XXX debian package or the SONIC_PY_COMMON_PY3 wheel package.
  - sonic_py_common is par tof most of the docker containers. The featurecfg is already included in the swss container.
- Use appropriate capability check APIs wherever required
  -  feature-check Linux command
  - component-check Linux command
  - sonic_py_common.device_info.is_present() PY2/PY3 API
  - /etc/sonic/sonic_branding.yml
- Use the sonic_py_common.device_info.is_present() API in Click CLI commands to hide the unsupported CLI commands or arguments.
- Every customization made to an Enterprise SONiC package must be represented as either a component or feature. "if package == campus"  are allowed only to create the following and not used for any other purposes
  - /etc/sonic/sonic_branding.yml
  - /etc/sonic/init_cfg.json
  - Include exclude docker containers from the installer
- Ensure that all supported packages combinations compile and work
- Ensure that the unbranded image *sonic-broadcom.bin* can be compiled when the *src/broadcom-exclusive* directory is removed. It must boot fine and also have all optional features available outside the *broadcom-exclusive* repo enabled