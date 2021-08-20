#  SQA Test Plan
#  REST Query Parameter Support for GET requests
#  SONiC 4.0.0 Release 

[TOC]

# Test Plan Revision History
| Rev  |    Date    |        Author              |    Change Description      |
| :--: | :--------: | :------------------------: | :------------------------: |
| 0.1  |  8/8/2021  |  Ramprakash Reddy Kanala   |    Initial version         |


# List of Reviewers
| Function  |         Name         |
| :------:  | :------------------: |
|   DEV     |                      |
|   QA      |  Kalyan Vadlamani    |

# List of Approvers
| Function  |           Name         | Date Approved |
| :-------: | :--------------------: | :-----------: |
|   DEV     |                        |               |
|   QA      |  Kalyan Vadlamani      |               |

# Definition/Abbreviation
| **Term** | **Meaning**                                   |
| -------- | --------------------------------------------- |
| QP       | Query parameter                               |
| REST     | Representational State Transfer               |
| HLD      | High Level Design Document                    |
| URI      | Uniform Resource Identifier                   |

### Objective 
The main objective of this document is to cover the test cases that will be executed to verify DEPTH, CONTENT and FIELD based query parameters. Topologies and test cases for testing the feature will be discussed as part of this document.

Query Parameter support for GET requests going to be supported in 4.0.0 release.

### Scope
This test plan will cover following aspects of Query Parameter Support for GET requests.
1.Validating multiple types of query parameters. 
2.List is not supported in FIELDS QP.
3.GNMI extension for the REST-like query parameter is not supported.
4.Event stream filter is not supported.

# Feature Overview
RESTCONF operation allows zero or more query parameters to be present in the request URI. QP can be used as a complementary option to allow the user to query for selected data of their interest. Management infra will provide support the following QPs for GET request ? Depth,Content,Fields QP support will be enabled for open-config yang, sonic yang and ietf yang models.

# 1 Test Focus Areas
## 1.1 Functional Testing 
   - Verify depth based query parameters.
   - Verify content based query parameters.
   - Verify fields based query parameters.

## 1.2 Scale and Performance Testing
  - Performance with QP should be better or equal to the current GET request performance without QP.
 
## 1.3 Negative Testing  
  - Server should reject if any QP other than DEPTH,CONTENT and FIELDS in request URI.
 
## 1.4  Reboot/Reload/Upgrade Testing
  NA
 
# 2 Topologies
  D1

# 3 Test Case and Objectives
## 3.1 Functional
### 3.1.1 Verify that all leaves/leaflist should be present in response.
| **Test ID**    | **REST_QP_311**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that all leaves/leaflist should be present in response** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with depth 0 or Unbounded <br/> 2) Verify that all leaves/leaflist should be present in response.<br/>|

### 3.1.2 Verify that all leaves/leaflist till N level should be present in response.
| **Test ID**    | **REST_QP_312**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that all leaves/leaflist till N level should be present in response** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with depth N <br/> 2) Verify that all leaves/leaflist till N level should be present in response.<br/>|

### 3.1.3 Verify that all contents of container should be present in response.
| **Test ID**    | **REST_QP_313**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that all contents of container should be present in response** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with depth 0 or Unbounded <br/> 2) Verify that all contents of container should be present in response.<br/>|

### 3.1.4 Verify that all contents of container till N level should be present in response.
| **Test ID**    | **REST_QP_314**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that all contents of container till N level should be present in response** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with depth N <br/> 2) Verify that all contents of container till N level should be present in response.<br/>|

### 3.1.5 Verify that all contents of list should be present in response.
| **Test ID**    | **REST_QP_315**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that all contents of list should be present in response** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with depth 0 or Unbounded <br/> 2) Verify that all contents of list should be present in response.<br/>|

### 3.1.6 Verify that contents of list till N level should be present in response.
| **Test ID**    | **REST_QP_316**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that contents of list till N level should be present in response** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with depth N <br/> 2) Verify that contents of list till N level should be present in response.<br/>|


### 3.1.7 Verify that QP returns only config descendant data nodes.
| **Test ID**    | **REST_QP_317**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that QP returns only config descendant data nodes** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with config content-type <br/> 2) Verify that QP returns only config descendant data nodes.<br/>|

### 3.1.8 Verify that QP returns only non config descendant data nodes.
| **Test ID**    | **REST_QP_318**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that QP returns only non config descendant data nodes** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with non config or operational content-type <br/> 2) Verify that QP returns only non config descendant data nodes.<br/>|

### 3.1.9 Verify that QP returns all descendant data nodes.
| **Test ID**    | **REST_QP_319**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that QP returns all descendant data nodes** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with all content-type <br/> 2) Verify that QP returns all descendant data nodes.<br/>|

### 3.1.10 Verify that QP returns only data node, matching the single field.
| **Test ID**    | **REST_QP_3110**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that QP returns only data node, matching the single field** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with single field <br/> 2) Verify that QP returns only data node, matching the single field.<br/>|

### 3.1.11 Verify that QP returns only data node, matching multiple fields.
| **Test ID**    | **REST_QP_3111**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that QP returns only data node, matching multiple fields** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with multiple fields saperated by semi-colon(;)<br/> 2) Verify that QP returns only data node, matching multiple field.<br/>|

### 3.1.12 Verify that QP returns all descendant data nodes, matching the fields inside child container path.
| **Test ID**    | **REST_QP_3112**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that QP returns all descendant data nodes, matching the fields inside child container path** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with child container fields path <br/> 2) Verify that QP returns all descendant data nodes, matching the fields inside child container path.<br/>|

### 3.1.13 Verify that QP returns data nodes matching the fields at current level and all descendant data nodes, matching the fields inside child container path.
| **Test ID**    | **REST_QP_3113**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that QP returns data nodes matching the fields at current level and all descendant data nodes, matching the fields inside child container path** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with fields at current level & child container fields path <br/> 2) Verify that QP returns data nodes matching the fields at current level and all descendant data nodes, matching the fields inside child container path.<br/>|

### 3.1.14 Verify that multiple QP type support in a single GET is limited to content and depth only.
| **Test ID**    | **REST_QP_3114**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that multiple QP type support in a single GET is limited to content and depth only** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send multiple QP type with content and depth saperated by ampersand(&)<br/> 2) Verify that QP returns data based on provided content type and depth.<br/>|


## 3.2 Scale and Performance Test Cases
### 3.2.1 Verify performance of QP with multiple/MAX fields is better than GET request without QP.
| **Test ID**    | **REST_QP_321**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify performance of QP with multiple/MAX fields is better than GET request without QP** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with MAX/multiple fields <br/> 2) Send GET request without fields for the same parent URI <br/> 3) Verify performance of QP with multiple/MAX fields is better than GET request without QP.<br/>|


## 3.3 Negative Test Cases
### 3.3.1 Verify server should reject the request if DEPTH is out of range.
| **Test ID**    | **REST_QP_331**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify server should reject the request if DEPTH is out of range** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with depth 65536 <br/> 2) Verify REST server should retuen with 400(Bad request) status code.<br/>|

### 3.3.2 Verify server should reject the request if CONTENT is not one of "all", "config", "non-config".
| **Test ID**    | **REST_QP_332**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify server should reject the request if CONTENT is not one of "all", "config", "non-config"** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with operational content-type <br/> 2) Verify REST server should retuen with 400(Bad request) status code.<br/>|

### 3.3.3 Verify server should reject the request if FIELDS type having list.
| **Test ID**    | **REST_QP_333**                                |
| -------------- | :----------------------------------------------------------- |
| **Test Name**  | **Verify that all leaves/leaflist should be present in response** |
| **Test Setup** | **Topology**                                                |
| **Type**       | **Functional**                                               |
| **Steps**      | 1) Send query parameter with FIELDS type having list <br/> 2) Verify REST server should retuen with 400(Bad request) status code.<br/>|


## 3.4 Reboot/Reload/Upgrade Test Cases
NA


## 4 Reference Links
HLD -> https://github.com/project-arlo/SONiC/blob/488855c9e026b8bc75d312c8bbe3ba3e21ca52d3/doc/mgmt/SONiC_QueryParameterForGet_HLD.md
