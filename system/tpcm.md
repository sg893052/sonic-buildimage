

# Third Party Container Management

## High Level Design Document
**Rev 0.1**

## Table of Contents

* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)
* [Requirements Overview](#requirements-overview)
  * [Functional Requirements](#functional-requirements)
  * [Configuration and Management Requirements](#configuration-and-management-requirements)
  * [Scalability Requirements](#scalability-requirements)
  * [Coldboot/Warmboot Requirements](#coldbootwarmboot-requirements)
* [Functional Description](#functional-description)
  * [TPCM and TPC Storage](#tpcm-and-tpc-storage)
  * [TPC Image Generation](#tpc-image-generation)
  * [TPCM Service](#tpcm-service)
  * [Resource Limitation and Configuration](#resource-limitation-and-configuration)
  * [TPCM CLIs](#tpcm-clis)
  * [TPC Install](#tpc-install)
  * [TPC Uninstall](#tpc-uninstall)
  * [TPC List](#tpc-list)
  * [TPC Docker Upgrade](#tpc-docker-upgrade)
  * [System Reboot](#system-reboot)
* [CLI](#cli)
* [Unit Test](#unit-test)

# List of Tables

[Table 1: Abbreviations](#table-1-abbreviations)

# Revision

Rev   |   Date   |  Author   | Change Description
:---: | :-----:  | :------:  | :---------
1.0   | 15/05/20 | Kalimuthu | Initial version
2.0   |          |           |  


# About this Manual

This document describes the Third Party Container Management(TPCM) framework in the SONiC Network Operating System(NOS). This feature helps the user to install and manages the Third Party Containers(TPC) in SONiC.

# Scope

This document describes the high level design details of Third Party Container Management framework that supports of  installation/uninstallation, configuration and upgrade of the TPC Dockers.  It also describes the tools and commands used for the TPC image management. The document covers that TPC's are from Docker containers and not from other sources like LXC, podman etc. 

# Definition/Abbreviation

### Table 1: Abbreviations

| **Term**     |  **Meaning**                  |
|:-------------|:------------------------------|
| TPC  | Third party container                 |
| TPCM | Third party container management      |
| ONIE | Open network installation environment  |
|  | |


# Requirements Overview

This document describes mechanisms for Third Party Container Management(TPCM) in  the SONiC environment. The TPCM allows the user to Install and Manage the custom container into the SONiC.  

SONiC is a network operating system based on the Debian distribution. The  SONiC components are tightly integrated with the installation image that are created during the build time itself. It allows the user to install the additional tools from the standard Debian repo, but any addition of new components to the SONiC system requires the rebuilding of the SONiC binary image. This feature allows user to install the third party components in the form of containers and that can be loaded and integrated as part of the SONiC system independently without rebuilding of SONiC image. 

SONiC provides the ability to pull and install Docker containers from any Docker Hub Registry using standard Docker pull/load commands, but it requires a utility in the SONiC to manage the external Docker containers. Docker consists of two parts 
- 1. Docker IMAGE - It is a read-only Docker image layer.
- 2. Docker CONTAINER - It is read-write Docker image layer on top of Docker IMAGE. 

Docker image can be tagged as multiple Docker images and for each image, one or more Docker containers can be created. Docker supports standard commands (add/update/remove) to manipulate the containers and image layers in the system. But it doesn't support the SONiC Docker management functions like Container restart upon system reboot, Container persistence and migration across SONiC image upgrades, Seamless container upgrade, service association for each container, Container System resource configuration, Monitoring, etc. The TPCM provides a framework to manage these functions in SONiC system.   

TPCM should support the following Docker life cycle:
	1. Docker image can be downloaded and installed/ docker can be pulled from docker registry.
	2. This will be installed in current SONIC FS.
	3. Docker container creation and running container.
	4. When the docker runs, can store data into its file system or mounted FS.
	5. Docker snapshot can be saved as image to be exported elsewhere.
	6. Docker stop/ remove/ of container.
	7. Removal of docker image.
	8. Upgrade of an existing docker container.

## Functional Requirements

### TPCM Framework Requirements

- Provide a mechanism for customers to bring their custom Docker container(TPC) into the SONiC OS environment.
- TPCM framework should provide an interface to install, uninstall, upgrade and list the TPC in the SONiC system.
- It should support that zero or more TPC can be installed on the SONiC system.
- TPC image upgrade shall be independent of SONiC image upgrade.
- SONiC image upgrade shall migrate all the currently installed TPCs into the newly installed image seamlessly. 
- TPCM should provide systemd service support for each of the TPC installed on the system.
- All the TPCs should get started automatically upon fast/warm/cold reboot
- TPCM should provide support for installation and upgrade of TPC from multiple sources - http server, scp/sftp server, local media and external Docker registry.
- TPCM should provide support for seamless TPC upgrade with the provision of container data backup and restore. 
- TPCM should provide an interface to enforce the TPC resource[ DISK, CPU and MEMORY ] usage limitation, but the current release covers only the CPU limitation. 

## Configuration and Management Requirements

- In order to integrate the TPC with SONiC, a set of config and show commands shall be supported.

### Config commands
- Clik and Klish Config commands shall be supported for TPCM to add, remove and upgrade of TPC container into SONiC.

### Show commands
- Show commands to display the list of installed TPC containers 
 
## Scalability Requirements
- Since the TPC folder is shared with SONiC partition, it adds the limitation to the amount of data stored on the SONiC partition. 
- Hardware resources(such as memory, CPU and data Volume mount) usage limit is enforced through predefined configuration for all the TPC Dockers.

## Coldboot/Warmboot Requirements
- The system should auto start all the installed TPC Dockers.

# Functional Description


## TPCM and TPC Storage

- All the TPCs and it's Image layers are stored as port of SONiC Docker image file system. The Docker image FS storage mostly located on the /var/lib/docker/overlay2 folder which is specific to each SONiC image in the system. A new TPCM folder is created in the SONiC partition for storing all the TPC container management files. The TPCM storage is shared across all the SONiC image in the system. The TPCM storage mainly contains the following contents.

1. TPC systemd service files.
2. Private data volume for each TPC.
3. One Shared data volume for all the TPCs.
4. Resource configuration file for each TPC.
5. TPCM service files and utility scripts that gets installed as part of SONiC installation.
6. Used as temporary storage for storing the TPC container image during SONiC image upgrade.



## TPC Image Generation

- Docker image can be created using one of the following methods:
	1. Pulled directly from an external Docker Hub Registry.
	2. Imported/Loaded from a Docker image file which got created using docker export/save command from an existing docker image and it will in the form of compressed tar format.
	3. Created from a custom Docker Build file using Docker build utilities.
	4. Created from an existing Docker container by saving the container image as a new Docker image.

## TPCM Service

- The TPCM service is a SONiC systemd service and it gets added as part of SONiC during build itself. This service takes care of migration of running containers into the newly installed image. During the SONiC image upgrade, it saves all the running containers into TPC storage folder. After reboot, it restores all the TPCs into the newly loaded image.


## Resource Limitation and Configuration

TPCM feature will be supported on systems with a minimum of 32G. To support auto migration of TPCM dockers, we need double the size and hence only half of it can be effectively used.
By default, for each TPC docker, the CPU resource limit is restricted to 20%. Memory and Disk usage limitations are not part of current release scope.

## TPCM CLIs

- TPCM related operations are implemented through a CLI called 'tpcm'. Set of options with optional parameters shall be provided to perform TPCM operations. 

- Supported operations:
		
		tpcm install   => Install the TPC image into SONiC system.
		tpcm uninstall => Uninstall the TPC image from SONiC system.
		tpcm upgrade   => Upgrade the existing TPC container.
		tpcm list      => list the existing TPC images.


## TPC Install

- The TPCM install option installs the TPC container into SONiC system. The container image can be created in two forms, either from an image file or pulled from an externel Docker registry. 
- The TPC image can be installed into SONiC system by using one of the following sources:
	- HTTP SERVER - Installed from http server.
	- SCP PATH    - Copied from a remote server through scp protocol.
	- SFTP SERVER - Copied from a remote server through sftp protcol.
	- MEDIA PATH  - Copied from a local media folder.
	- DOCKER HUB  - Downloaded from remote Docker registry.

- One or more TPC images can be installed on the SONiC system. Once the SONiC OS is up, the TPC instance can be brought up into SONiC by running tpcm install command. 

- Example:

		tpcm install tpc-name /path/docker-tpcimage.gz -y

- While installing the TPC image, it creates the sonic service for each of the TPC's to bring up the TPC container up.  So the loading of the TPC container image is needed only once, On the subsequent reboot, these TPC services will be started automatically by the systemd manager in the SONiC system.
- The TPC service file is generated for each TPC container. The generated service is stored under tpcm storage folder and also installed on the systemd service folder.

- During the installation process, the following steps get executed.
 1. Download the TPC image from one of the sources.
 2. Install the TPC image into SONiC system.
 3. Create a systemd service and resource configuration for the newly installed TPC image.
 4. Create a container for the newly installed TPC image. 
 5. Bring up container instance by starting the TPC service. 
 
## TPC Uninstall

- The uninstallation option will remove the TPC container from the SONiC system. 

	- Example:
		
			tpcm uninstall \<tpc-name\>  -y
		
- During the uninstallation process, the following steps get executed.

 1. Stops the running TPC container by stopping the corresponding TPC service.
 2. Removes the TPC service file from the SONiC systemd service manager.
 3. Removes the TPC from the SONiC system.
 4. Removes the TPC image from the SONiC system
 
-  Once the TPC and its associated service is removed, the subsequent SONiC reboot will not auto start the removed TPC services.

## TPC List

- The TPCs can be listed using the tpcm list command. 
		
		# tpcm list 

### Running TPC Dockers

- The loaded TPC can be viewed using the following regular Docker command to view the running Dockers. This command will list all the running containers including the SONiC as well as TPC Docker containers.

		# docker ls 
		
## TPC Docker Upgrade

- The TPCM framework allows the user to upgrade the running TPC Dockers.  The tcpm framework upgrade option allows the user to upgrade an existing TPC docker with a new one. During the upgrade process, TPCM provides the interface to run the pre/post script of a container if it is present inside the TPC container. These hook allows the user to take the backup of data from existing container and restore it back to the upgraded Docker. The pre hook is executed as part of running container and the post hook is executed as part of the upgraded container. The hook scripts should be placed in a fixed folder in the TPC container with the name as /tpc/hook/pre.sh' and 'tpc/hook/post.sh'. The pre script should create a backup file with the name as '/tpc/backup/data.tar.gz' and the post script should restore the contents from the data file. For each TPC, the volume '/tpc/' is mounted from host partition '/host/tpc/\<tpc-name\>'.

- Example:

		# tpcm upgrade  \<tpc-name\> \<tpc-image source\> -y 
 
- It executes the following sequence to upgrade the running Docker.
	1. Run the TPC pre hook script
	3. It stops the already running TPC container
	4. Remove the existing TPC container
	5. Loads the new TPC image into the Docker image list.
	6. Create the new TPC container from newly loaded TPC image
	7. Run the newly created container
	8. Execute the TPC post script
	9. Remove the old TPC image.

# CLI:

## 1. Installing the TPC image

	TPC image can be loaded and installed in two forms:

#### TPC image from a file:
	It uses the download protocol to download the TPC image into a temporary path and then that gets loaded into Docker image filesystem. 
	1. Load the TPC image file from a external http/https server

	- Syntax:
	#### tpcm install \<tpc-container-name\> [--args \<container args\>] \<URL\> [-y]
	where:
	\<tpc-container-name\>  => Name of the TPC container to be loaded.
	--args  => It specifies the additional container creation parameters.
	\<URL\> => It is http/https web url from which the TPC image can be downloaded.
	[-y]  => User confirmation
	- Example:
		tpcm install mydocker http://myserver/path/mydocker.tar.gz -y

	2. Load the image file from a external server through scp/sftp

	- Syntax:
	#### tpcm install \<tpc-container-name\> --protocol \<scp/sftp\> --server \<server name\>  --username \<username\> [--password \<password\>] \<TPC image path\> [--args \<container args\>] [-y]
	where:
	\<tpc-container-name\>  => Name of the TPC container to be loaded.
	--protocol  => It specifies the protocol through which it downloads the image and it can be either scp or sftp.
	--server    => Specifies the remote server name from which image is being download.
	--username  => Specifies the remote server user credentials.
	--password  => Specifies the remote server user's password. If passwd is not given, prompted passwd for during the image download.
	--args      => It specifies the additional container creation parameters.
	\<TPC image path\> => Path on the remote server where the TPC image is located.
	[-y] => User confirmation
	- Example:

			tpcm install mydocker --protocol scp --server myserver  --username myuser /path/mydocker.tar.gz -y
			Password:

	3. Load the image file from a local media path
	- Syntax:
	#### tpcm install \<tpc-container-name\> \<TPC image path\> [--args \<container args\>] [-y]
	where:
	\<TPC image path\> => Local media path where the TPC image is located.
	--args => It specifies the additional container creation parameters.
	[-y]   => User confirmation
	- Example:

			tpcm install mydocker /media/usb/path/mydocker.tar.gz -y

		
#### Load the TPC image from a external Docker repo
	It loads the TPC image directly from external Docker registry into local Docker image filesystem.

	- Syntax:
	#### tpcm install \<tpc-container-name\> \<Image name\>[:\<Tagname\>] [--args \<container args\>] [-y]

	- where 
	\<tpc-container-name\>  => Name of the TPC container to be loaded.
	\<Image name\>  => Name of the image to be downloaded.
	[Tagname]  => Optional, Tagname of the image, by default 'latest' would be taken. 
	--args => It specifies the additional container creation parameters.
	[-y] => User confirmation
	
## 2. Uninstalling the TPC image

#### tpcm uninstall \<container-name\> [-y]

- where 
	\<tpc-container-name\>  => Name of the TPC container to be removed.
	[-y] => User confirmation

## 3. Upgrading the TPC image

#### tpcm  upgrade  \<container-name\> <same options as install\> [--skip_data]

- where 
	--skip_data => If set, container data backup and restore steps will be skipped.
	All other options are same as install command.
	

## 4.  List the TPC images

- List the all TPC image that are installed on the system

#### tpcm  list   


## TPCM with Mgmt VRF 
- Creating a management VRF will enslave eth0 under a layer 3 construct (VRF). This will ensure traffic exchanges between the third-party docker to any internal server is not advertised on the data ports.
- If Mgmt-VRF is configured, Users should note that:
	1. All Dockers in SONiC run on host IP stack (Default VRF)
	2. There is no option to run specific Dockers under Management VRF
	3. The following ip rule should be added to allow access to ‘eth0’ for all Dockers
		# sudo ip rule add pref 32768 from all lookup mgmt


## SONiC image NetInstall

- When SONiC is being net-installed through ONIE, it wipes out the entire SONiC partition. So the user should take the backup of existing TPC contents and restore them back manually if required.  


## SONiC image upgrade 

- When SONiC is being upgraded, the following sequence gets executed.

 1. Save all the TPC container as a new TPC image layers.
 2. Export all the TPC image in the form of compressed tar file and store them into the TPCM storage folder
 3. After the reboot, restore all the Docker container into the newly loaded SONiC image.

## System Reboot
- During the system bootup, the following sequence gets executed.

 1. Start the SONiC TPCM service as part for systemd startup
 2. When SONiC TPCM service startup script is being executed, it checks for TPC container files presence in the TPC folder, if yes, load all the TPC Dockers along with its associated service file into SONiC system and start all the TPC services.
 3. Else, graceful exit from the startup script.

# Unit Test


  | SNO |  Unit Testcase 
 :------| :----------------------------------------------------
    1 | Verify the TPCM service gets created as part of SONiC.
	2 | Verify the TPC install from http/https server.
	3 | Verify the TPC install from a remote server using SCP protocol.
	4 | Verify the TPC install from a remote server using SFTP protocol.
	5 | Verify the TPC install from a local media path
	6 | Verify the TPC install from an external Docker registry.
	7 | Verify the TPC install with different parameter combinations.
	8 | Verify the TPC uninstall of running containers.
    9 | Verify the TPC upgrade with an already installed image.
    10| Verify the TPC uninstall and its associated service file removal.
    11| Verify the TPC service auto startup after reboot.
    12| Verify the SONiC image upgrade with TPC container migration.
    13| Verify the SONiC ONIE uninstall with TPC container removal.
	14| Verify the Hardware resource Disk/CPU/Memory limitations.
	15| Verify the TPC list of all TPC dockers.
	16| Verify the TPC service start/stop/restart.


