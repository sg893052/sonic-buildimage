#  SQA Test Plan
#  SONiC : Role-Based Access Control
#  SONiC 3.0 Project and Buzznik Release

[TOC]

# Test Plan Revision History
| Rev  |    Date    |        Author        |      Change Description      |
| :--: | :--------: | :------------------: | :--------------------------: |
| 0.1  | 11/12/2019 | Prudvi Mangadu       |      Initial version         |

# List of Reviewers
| Function     |         Name         |
| :------:     | :------------------: |
|   Dev        | Jeff Yin             |
|   QA         | Anil Kumar Kolkaleti |
|   QA         | Giri Babu Sajja      |
|   Auto-infra | Kalyan Vadlamani     |


# List of Approvers
| Function  |           Name         | Date Approved |
| :-------: | :--------------------: | :-----------: |
|   Dev     |  Jeff Yin              |               |
|   QA      |  Anil Kumar Kolkaleti  |               |
|   QA      |  Giri Babu Sajja       |               |


# Definition/Abbreviation
| **Term** | **Meaning**                                   |
| -------- | --------------------------------------------- |
| RBAC     | Role-Based Access Control                     |
| AAA      | Authentication, Authorization, Accounting     |
| CLI      | Command-Line Interface                        |
| REST     | REpresentational State Transfer               |
| gNMI     | gRPC Network Management Interface             |
| NBI      | North-bound Interfaces (CLI, REST, gNMI)      |


# Feature Overview
The code will extend the existing Klish (CLI) and REST Server modules in the sonic-mgmt-framework repository. Klish will be extended to enable authentication to the REST server (depending on the ultimately chosen approach), and the REST Server will need to be extended to map transactions to a user and pass that username data to the Translib.The gNMI server, which currently exists in the sonic-telemetry repository, needs to support passing the username down to Translib as well.The Translib code (also in sonic-mgmt-framework) will be extended to support RBAC via Linux Groups. It will receive username data from the REST/gNMI NBIs and perform the Group lookup for a given user.For user management, a service must run on the host to sync the Redis User DB with the Linux user database and vice-versa.


# 1 Test Focus Areas
## 1.1 Functional Testing 
  - NBI Authentication
  - CLI Authentication to REST server
  - Translib Enforcement of RBAC
  - Linux Groups
  - Certificate-based Authentication for gNMI and REST
  - Local User Management and UserDB Sync
  - Configuration and Management Requirements


## 1.2 Scale and Performance Testing
  - Adding authentication to NBIs will result in some performance overhead, especially when doing operations involving asymmetric cryptography. Care should be taken to leverage performance-enhancing features of a protocol wherever possible.
  
 
## 1.3  Reboot/Reload/Upgrade Testing
   - Not Applicable


# 2 Topologies
## 2.1 Topology


# 3 Test  Case and Objectives
## 3.1 Functional

### 3.1.1 Verify that non-admin user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.

| **Test ID**    | **FtOpSoScRBACFn001**                                      |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                      |
| **Steps**      | **Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open SSH session using the credentials configured in step1.<br/>3.Execute show commands.<br/>4. Try to configure some non-default config.<br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |



### 3.1.2 Verify that non-admin user doesn’t have all permissions except show (get) commands when SSH to the system with pubkey. 

| **Test ID**    | **FtOpSoScRBACFn002**                            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin user doesn’t have all permissions except show (get) commands when SSH to the system with pubkey.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                      |
| **Steps**      | **Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open SSH session using the pubkey generated based configured credentials in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |



### 3.1.3  Verify that admin user will have all permissions when SSH to the system with username/password.



| **Test ID**    | **FtOpSoScRBACFn003**              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin user will have all permissions when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                      |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the admin user with admin role.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/> 4. Verify that config commands execute successfully.** |



### 3.1.4 Verify that admin user will have all permissions when SSH to the system with with pubkey. 
| **Test ID**    | **FtOpSoScRBACFn004**                                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin user will have all permissions when SSH to the system with with pubkey.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the admin user with admin role.<br/>2. Open SSH session using the pubkey generated based configured credentials in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands execute successfully.**  |



### 3.1.5   Verify that non-admin linux user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.

| **Test ID**    | **FtOpSoScRBACFn005**                              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin linux user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with Operator role using linux shell.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successfully.** |


### 3.1.6 . Verify that admin linux user will have all permissions when SSH to the system with username/password.  

| **Test ID**    | **FtOpSoScRBACFn006**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin linux user will have all permissions when SSH to the system with username/password** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the admin user with admin role using linux shell.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands execute successfully.**  |


### 3.1.7  Verify that user doesn’t have any permissions while user is configured with non-existing group role when SSH to the system with username/password.

| **Test ID**    | **FtOpSoScRBACFn007**            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that user doesn’t have any permissions while user is configured with non-existing group role when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with non-existing group/role.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |



### 3.1.8   Verify that non-admin tacacs user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.


| **Test ID**    | **FtOpSoScRBACFn008**            |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin tacacs user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user on TACACS as remote auth server.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |



### 3.1.9 Verify that admin tacacs user will have all permissions when SSH to the system with username/password.
| **Test ID**    | **FtOpSoScRBACFn009**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin tacacs user will have all permissions when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the admin user on TACACS as remote auth server.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands executed successfully.** |


### 3.1.10  Verify that non-admin Radius user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.

| **Test ID**    | **FtOpSoScRBACFn010**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin Radius user doesn’t have all permissions except show (get) commands when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user on Radius as remote auth server.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |



### 3.1.11   Verify that admin Radius user will have all permissions when SSH to the system with username/password.


| **Test ID**    | **FtOpSoScRBACFn011**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin Radius user will have all permissions when SSH to the system with username/password.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the admin user on Radius as remote auth server.<br/>2. Open SSH session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands executed successfully.** |



### 3.1.12   Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to REST server with Password-based Authentication with JWT token-based authentication

| **Test ID**    | **FtOpSoScRBACFn012**                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to REST server with Password-based Authentication with JWT token-based authentication** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                 |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open REST session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |



### 3.1.13  Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to REST server with Certificate-based Authentication with JWT token-based authentication.

| **Test ID**    | **FtOpSoScRBACFn013**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to REST server with Certificate-based Authentication with JWT token-based authentication.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open REST session using the Certificate from configured credentials in step1.<br/>3. Execute show commands.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |



### 3.1.14   Verify that admin user will have all permissions when authenticate to REST server with Password-based Authentication with JWT token-based authentication.


| **Test ID**    | **FtOpSoScRBACFn014**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin user will have all permissions when authenticate to REST server with Password-based Authentication with JWT token-based authentication.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the admin user with admin role.<br/>2. Open REST session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands executed successfully.** |



### 3.1.15   Verify that admin user will have all permissions when authenticate to REST server with Certificate-based Authentication with JWT token-based authentication.


| **Test ID**    | **FtOpSoScRBACFn015**                                               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin user will have all permissions when authenticate to REST server with Certificate-based Authentication with JWT token-based authentication.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                               |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open REST session using the Certificate from configured credentials in step1.<br/>3. Execute show commands.<br/><br/>Expected Behavior:<br/>2. Verify that Session opened fine with credentials.<br/>4. Verify that config commands executed successfully.** |


### 3.1.16   Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to gNMI server with Password-based Authentication with Token-based authentication.

| **Test ID**    | **FtOpSoScRBACFn016**                     |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to gNMI server with Password-based Authentication with Token-based authentication.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open gNMI session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** | 




### 3.1.17  Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to gNMI server with Certificate-based Authentication.

| **Test ID**    | **FtOpSoScRBACFn017**              |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that non-admin user doesn’t have all permissions except show (get) commands when authenticate to gNMI server with Certificate-based Authentication.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                   |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open gNMI session using the Certificate from configured credentials in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands should not executed successful.** |


### 3.1.18    Verify that admin user will have all permissions when authenticate to gNMI server with Password-based Authentication with Token-based authentication.

| **Test ID**    | **FtOpSoScRBACFn018**                  |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin user will have all permissions when authenticate to gNMI server with Password-based Authentication with Token-based authentication.** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                   |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the admin user with admin role.<br/>2. Open gNMI session using the credentials configured in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands executed successfully.** |


### 3.1.19 Verify that admin user will have all permissions when authenticate to gNMI server with Certificate-based Authentication.

| **Test ID**    | **FtOpSoScRBACFn019**               |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that admin user will have all permissions when authenticate to gNMI server with Certificate-based Authentication** |
| **Test Setup** | **Topology**                                                 |
| **Type**       | **Functional**                                                   |
| **Steps**      | **Procedure:<br/>Procedure:<br/>1. Configure the non-admin user with Operator role.<br/>2. Open gNMI session using the Certificate from configured credentials in step1.<br/>3. Execute show commands.<br/>4. Try to configure some non-default config.<br/><br/>Expected Behavior:<br/>1. Verify that configuration is success.<br/>2. Verify that Session opened fine with credentials.<br/>3. Verify that show commands executed successfully.<br/>4. Verify that config commands executed successfully.** |


## 4 Reference Links
<https://github.com/project-arlo/SONiC/pull/37/files/28ffb3aea7eaf98968a3fdb97cc761087976c7ea#diff-331ea0661fab951445616f9d1b3fb62a>
