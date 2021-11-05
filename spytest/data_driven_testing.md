# Feature Name

SpyTest - Data Driven Test Development

# High Level Design Document

#### Rev 0.4

# Table of Contents
  - [Revision](#revision)
  - [About This Manual](#about-this-manual)
  - [Scope](#scope)
  - [1 Feature Overview](#1-feature-overview)
  - [2 Requirements](#2-requirements)
  - [3 Design Overview](#3-design-overview)
    - [3.1 Design Components](#31-design-components)
     - [3.1.1 Code Generator](#311-code-generator)
     - [3.1.2 Message](#312-message)
     - [3.1.3 Generic APIs](#313-generic-apis)
      - [3.1.3.1 Configuration API](#3131-configuration-api)
      - [3.1.3.2 Verification API](#3132-verification-api)
      - [3.1.3.3 Subscription API](#3133-subscription-class)
      - [3.1.3.4 RPC API](#3134-rpc-api)
      - [3.1.3.5 GNOI API](#3135-gnoi-api)
      - [3.1.3.6 get_ietf_json](#3136-get_ietf_json)
      - [3.1.3.7 get_bind](#3137-get_bind)
     - [3.1.4 SpyTest Utils](#314-spytest-utils)
  - [4 Functionality](#4-functionality)
    - [4.1 Code Generation](#41-code-generation)
    - [4.2 Subscription Support](#42-subscription-support)
      - [4.2.1 Connection Management](#421-connection-management)
      - [4.2.2 Creating Subscription](#422-creating-subscription)
      - [4.2.3 Subscribing to Multiple Message Paths](#423-subscribing-to-multiple-message-paths)
      - [4.2.4 Verifying Notifications](#424-verifying-notifications)
      - [4.2.5 Closing Subscription](#425-closing-subscription)
    - [4.3 RPC Support](#43-rpc-support)
    - [4.4 GNOI Support](#44-gnoi-support)
  - [5 Developer Steps](#5-developer-steps)
    - [5.1 Copying Relevant YANGs](#51-copying-relevant-yangs)
    - [5.2 Message Generation](#52-message-generation) 
    - [5.3 Yang Binding Generation](#53-yang-binding-generation) 
    - [5.4 Testcase Sample For Configuration](#54-testcase-sample-for-configuration)
    - [5.5 Testcase Sample For Verification](#55-testcase-sample-for-verification)
    - [5.6 Testcase Sample For RPC](#56-testcase-sample-for-rpc)
    - [5.7 Testcase Sample For Subscription](#57-testcase-sample-for-subscription)
    - [5.8 Copying Relevant Proto Definition Files](#58-copying-relevant-proto-definition-files)
    - [5.9 Proto Binding Generation](#59-proto-binding-generation)
    - [5.10 Testcase Sample For gNOI](#510-testcase-sample-for-gnoi)

# Revision

| Rev |     Date    |       Author       | Change Description                |
|:---:|:-----------:|:------------------:|-----------------------------------|
| 0.1 | 09/29/2021  |   Mohammed Faraaz  | Initial version                   |
| 0.2 | 09/30/2021  |   Sachin Holla     | Add subscription test details     |
| 0.3 | 09/30/2021  |   Balachandar Mani | Add verification test details     |
| 0.4 | 09/30/2021  |   Arun Barboza     | Add RPC and GNOI test details     |

# About this Manual

This document provides general information about the data driven testing mechanism using the generated message classes.

# Scope

This document only describes the high level design of data driven testing mechanism using the message classes.
Describing the Spytest and the topics related to the data driven testing in general are beyond the scope of this document.

# 1 Feature Overview

The data driven testing mechanism can be achieved in SpyTest using the message classes. Developer will write the test cases in terms of message classes and operate on these classes. Test UI is chosen at runtime. 
The message classes are generated from the YANG models.
The message class can be imagined as a feature representation containing knobs(fields) and the APIs to configure, deconfigure and verify these knobs.

***Below diagram describes a message and its generation.***
![Message class](images/message_class.svg)

***Below diagram describes the usage of the message in the testcase.***
![Message class in testcase](images/message_class_in_testcase.svg)

# 2 Requirements

## 2.1 Functional Requirements

1. The Message class should be auto-generated.
2. The Message class should expose knobs and APIs for configuration, deconfiguration and verification.
3. The Message class should fully automate the testing of REST and GNMI Northbounds
4. The Message class should contain stubs for Klish.
5. The Message class should be customizable i.e. developers can add non yang fields(knobs) and custom APIs.
6. The Custom names for messages and knobs(attributes) should be allowed.

# 3 Design Overview

![Data driven testing](images/data_driven_testing_design.svg)

## 3.1 Design Components

## 3.1.1 Code Generator

- At the heart of automation is the code generator which generates the message class from the YANG model. The featue uses an open source YANG Parser called Pyang for parsing YANG model.
Once the YANG model are parsed, it uses a custom built pyang plugin to generate a message class.

- Along with Message classes. The YANG bindings are also generated from YANG using a pyangbind plugin.

- For gNOI, the protobuf message python bindings are generated from the .proto service definition file using the protoc compiler.

## 3.1.2 Message

Messages are python classes containing attributes which are mapped to YANG Leaf/Leaf-list.
Along with attributes the messages also contain below Action methods
- Configure - This method will allow the configuration of a full message or a specific attribute in a message.
- UnConfigure - This method will allow the deconfiguration of a full message or a specific attribute in a message.
- Verify - This method will allow the verification of a full message or a specific attribute in a message.
- Helper methods - These methods will be used in the above action methods to generate payload, path etc.

## 3.1.3 Generic APIs

Generic APIs are not part of generated code. They are added in **apis/yang/codegen/base.py** module (This module contains a class **Base**, which is a master base class for all generated base message classes).

The role of generic APIs is to service the request from Action methods. Below are the some of task Generic APIs perform
- Building a configuration, deconfiguration, subscription and a verification request specific to the UI type.
- Executing the request and validating the response.
- Building an IETF JSON.
- Getting a pyangbind object for a specific class, attribute or a path.

Following are some of the Generic APIs

### 3.1.3.1 Configuration API

This is a generic method which will be invoked by the Action API inside the message class, this API is added to the spytest infrastructure, this method does the below things

- Generates payload for configuration request
- Builds URIs for configuration request
- Executes the request

For KLISH, it invokes the corresponding obj.configure_klish(...), which may have been implemented by the feature owner in the derived class of obj’s base class.

### 3.1.3.2 Verification API

This is a generic API which will be invoked from the message class's method to verify the REST/GNMI GET method's response.

#### 3.1.3.2.1 REST GET Verification API

This API performs the GET request using the REST interface which will be invoked by the verfiy_rest method inside the message class, this API is added to the spytest infrastructure, this method does the below things

- Invoke the get_path() method of the Message class to get the URL path.
- Perform the REST GET request on the URL path of the Message class and get its response.
- Get the pyangbind object by calling get_bind() method of the Message class.
- Call the generate_elements method of the pybindCustomIETFJSONEncoder to create the dictionary of the pyanbind object.
- Get the response payload of the GET request and compare it with pyangbind's dictionary based on the encoding type.(IETF_JSON)
- Return the status of the comparison.

#### 3.1.3.2.2 GNMI GET Verification API

This API performs the GET request using the GNMI interface which will be invoked by the verfiy_gnmi method inside the message class, this API is added to the spytest infrastructure, this method does the below things

- Invoke the get_path() method of the Message class to get the URL path.
- Perform the GNMI GET request on the URL path of the Message class and get its response
- Get the pyangbind object by calling get_bind() method of the Message class
- Call the generate_elements method of the pybindCustomIETFJSONEncoder to create the JSON dictionary of the pyanbind object.
- Get the response payload of the GET request and compare it with pyangbind's dictionary based on the encoding type.(IETF_JSON)
- Return the status of the comparison.

### 3.1.3.3 Subscription API

Subscription test APIs will use the existing gNMI request and verification APIs defined
in `apis/yang/utils/gnmi` module.

### 3.1.3.4 RPC API

Yang Rpcs define a data-model-specific operation which is invoked with the POST method. These are a collection of classes and a generic method to execute the Yang Rpc. It is part of the spytest infra, and does the following:

- The Rpc message class allows the Test Case (TC) writer to specify the input argument to the Rpc, and the expected output.
- The YangRpcService class provides the execute() method.

The execute() method:

- Generates payload from the input field of the Rpc message object
- Builds the URI from the Rpc message object
- Executes the Rpc
- If the verify option is given, it compares the returned payload from the DUT with the (generated) payload from the output in the message object
- Note: Message Classes and python bindings are generated from the corresponding Yang files.

### 3.1.3.4 gNOI API

gRPC Network Operations Interface (gNOI) defines a set of gRPC-based microservices for executing operational commands on network devices. These are a collection of classes and a generic method to execute the gNOI Rpc.
It is part of the spytest infra, and does the following:

- The GnoiService class provides an interface to the protobuf messages, services, and the execute() method.
- The GnoiRpc message class allows the TC writer to encapsulate the request message and the expected response message.

The execute() method.
- Invokes the protobuf service stub to execute the Rpc.
- If the verify option is given, it compares the message returned from the DUT with the user supplied expected response message
- Note: gNOI does not have a corresponding Yang model. Message python bindings are generated from the corresponding .proto service interface definition files(IDL).
- Note: gNOI RPC with stream message are not supported currently in the telemetry repo, hence no spytest support is provided.

### 3.1.3.5 get_ietf_json

Generates the IETF JSON for a specific class, attribute or a path.

```python
    def get_ietf_json(self, content="all", target_attr=None, target_path=None, indent=2):
        """ Generates the IETF JSON for a specific class, attribute or a path.
        """
```

### 3.1.3.6 get_bind

Generates the Pyangbind Object for a specific class, attribute or a path.

```python
    def get_bind(self,content="all", target_attr=None, target_path=None):
        """ Generates the Pyangbind Object for a specific class, attribute or a path.
        """
```

## 3.1.4 SpyTest Utils

These are existing SpyTest utilties such rest_get, rest_put, compare_payloads, etc which Generic APIs will use to service the message.

# 4 Functionality

## 4.1 Code generation

The code generator generates the following types of classes for YANG data nodes.

Two sets of classes are generated for each data node
- Base class - These classes contain actual attributes, their setter and getter methods. These classes ***SHOULD NOT BE MODIFIED or INSTANTIATED inside a test case***. 
- Derived class - These classes contain stub methods for Klish, along with operational methods such as configure, unconfigure, and verify. Developers are required to fill the stub portion of this class such as *_klish. Also, Users can edit this class to add custom attributes and methods. Once the derived classes are generated, they will not be re-generated/over-written, so as to preserve the users’ edits. ***These are the classes users will be using in their test case for writing business logic.***

The default location to place the messages is shown below
```text
    apis/yang/autogen/messages
    |
    |_<module> Example:acl, contains all Derived classes
        |_Acl.py
        |_AclSet.py    
        |_Base (directory containing Base classes)
          |_Acl.py
```

### 4.1.1 Rules

### 4.1.1.1 Generic Rules

- Only YANG models which have data nodes will be considered for auto-generation. YANG with submodules are ignored as the nodes under them will be considered as part of the main module’s generation. This is in line with other YANG tools such as the OpenAPI spec generator.
- Subtree which has no lists in it will be converted into one class. The name of the class will be the container name. If the name is not unique it will be the parent container + container name. Example for acl/.../acl-sets/acl-set/.../acl-entries/acl-entry. AclEntry will be the first choice for the class name but if it is not unique its parent container i.e. acl-set can be used to generate the name as AclSetAclEntry.  The same logic will be used for naming leafs and leaf-lists.
- Leafs with python keywords as names will be converted to a special name. For example, if the leaf name is type then it can be converted to type_ in message classes.
- Class, modules, and attributes can have custom names. Codegen will provide a JSON file under apis/yang/codegen/config.json to accept such customization.

Lets see an example of the simplified version of ACL yang with subtrees

**Sample-1**
```diff
module: openconfig-acl
+ +--rw acl
     +--rw config
     |  +--rw oc-acl-ext:counter-capability?   identityref
     +--ro state
     |  +--ro counter-capability?   identityref
     +--rw acl-sets
+    |  +--rw acl-set* [name type]
     |     +--rw name           -> ../config/name
     |     +--rw type           -> ../config/type
     |     +-- <SNIP>
     |     +--rw acl-entries
+    |        +--rw acl-entry* [sequence-id]
     |           +--rw sequence-id    -> ../config/sequence-id
     |           +-- <SNIP>
     +--rw interfaces
+    |  +--rw interface* [id]
     |     +--rw id                  -> ../config/id
     |     +-- <SNIP>
     |     +--rw ingress-acl-sets
+    |     |  +--rw ingress-acl-set* [set-name type]
     |     |     +--rw set-name       -> ../config/set-name
     |     |     +--rw type           -> ../config/type
     |     |     +-- <SNIP>
     |     +--rw egress-acl-sets
+    |     |  +--rw egress-acl-set* [set-name type]
     |     |     +--rw set-name       -> ../config/set-name
     |     |     +--rw type           -> ../config/type
     |     |     +-- <SNIP>
```

**Sample-2**
```diff
module: ietf-ptp
  +--rw ptp
+    +--rw instance-list* [instance-number]
        +--rw instance-number       uint32
        +--rw default-ds
        |  +--rw two-step-flag?     boolean
        |  +--ro clock-identity?
```

**Note** Class will be generated for all elements highlighted in Green
  
### 4.1.1.2 Base class Rules

- For all leaf and leaf-list, a private class member will be generated and for all leaf and leaf-lists along with private members, a property class decorated with special methods will also be generated.
- The subtree which has a list in it will be converted to a dict with the same key as that of the yang. If yang has multiple keys then a tuple will be used as a key. Class will expose a message to add/delete a list instance to the parent message instance.
- Each class will have a generate_bind method to generate Pyang bind object. This method will be invoked by Action APIs (REST/GNMI’s configure(), etc).
- get_bind() will be generated, which will return the binding specifically to the class instance.
```python
  get_bind(target_attr=None,
    target_path=None,
   content=["config","nonconfig","all"]
)
```

**target_path** and **target_attr** are mutually exclusive
 
If target_attr and target_path are None(default) - Full binding obj for the class is returned. 
If target_attr is an attribute name, then the object-specific to that class will be returned. for the attributes mapped to leaf/leaf-list, their corresponding value will be returned.
If target_path contains the path to leaf, the value will be returned. If it contains the path to a container like 'config', the binding for config Is returned. The Path needs to be a relative path i.e. specific to the class instance.
 
**Example**
```text
 aclSet.get_bind() => Full binding for the class aclSet
 aclSet.get_bind(target_attr="configDescription") ==> return binding specific to the attribute configDescription (/acl/acl-sets/acl-set/config/description)
```

- get_path(ui=”rest”) will be generated, which will return a Paths in a specified UI format. 

The Path will only be returned if all parent hierarchies are established, otherwise, None will be returned. Using one of the following ways
 
**Using constructor**

```python
x = acl_entry(acl_set=acl_set(name="ONE"), seq_id=10)
x.unconfigure(targetAttr=x.name)
```

**Using add_* method**

```python
y = acl_set(name="ONE")
x = acl_entry(seq_id=10)
y.add(x)
x.unconfigure()
```

### 4.1.1.1.2.1 Sample Base Class

Below is a sample generated class for XPATH ***/openconfig-acl:acl***
**note** It is not full class only partial code is shown here for the purpose of demonstration.

```python
##############################################################
##############################################################
##### THIS IS AN AUTO-GENERATED FILE PLEASE DO NOT EDIT ######
##############################################################
##############################################################

from apis.yang.codegen.base import Base
from collections import OrderedDict
from apis.yang.codegen.bindings import openconfig_acl

class AclBase(Base):
    """
    Top level enclosing container for ACL model config
and operational state data
    """
    def __init__(self,  CounterCapability):
        super(AclBase, self).__init__()
        
        # Corresponding YANG Path
        self.__yang_path = "/openconfig-acl:acl"
        
        # Corresponding YANG Path in RESTCONF format
        self.__yang_path_rest = "/openconfig-acl:acl"
        self.__yang_path_rest_dict = OrderedDict()
        
        # Corresponding YANG Path in GNMI format
        self.__yang_path_gnmi = "/openconfig-acl:acl"
        self.__yang_path_gnmi_dict = OrderedDict()

        # Invokes setter for leafs/leaf-lists
        self.CounterCapability = CounterCapability

        # ConfigCounterCapability
        self.__yang_path_rest_dict["ConfigCounterCapability"] = "/openconfig-acl:acl/config/openconfig-acl-ext:counter-capability"
        self.__yang_path_gnmi_dict["ConfigCounterCapability"] = "/openconfig-acl:acl/config/openconfig-acl-ext:counter-capability"

        # StateCounterCapability
        self.__yang_path_rest_dict["StateCounterCapability"] = "/openconfig-acl:acl/state/counter-capability"
        self.__yang_path_gnmi_dict["StateCounterCapability"] = "/openconfig-acl:acl/state/counter-capability"

        # Dict for child lists
        self.AclSet_dict = OrderedDict()

    # Getters/Setters for attributes

    @property
    def CounterCapability(self):
        """ Common getter for /openconfig-acl:acl/config/openconfig-acl-ext:counter-capability and /openconfig-acl:acl/state/counter-capability.
        System reported indication of how ACL counters are reported by the target
        """
        return {
            "ConfigCounterCapability": self.__ConfigCounterCapability,
            "StateCounterCapability":  self.__StateCounterCapability
        }
    
    @CounterCapability.setter
    def CounterCapability(self, CounterCapability=None):
        """ Common setter for /openconfig-acl:acl/config/openconfig-acl-ext:counter-capability and /openconfig-acl:acl/state/counter-capability.
        System reported indication of how ACL counters are reported by the target
        """        
        self.ConfigCounterCapability = CounterCapability
        self.StateCounterCapability = CounterCapability

    # ConfigCounterCapability
    @property
    def ConfigCounterCapability(self):
        """ /openconfig-acl:acl/config/openconfig-acl-ext:counter-capability
        System reported indication of how ACL counters are reported by the target
        """
        return self.__ConfigCounterCapability
    
    @ConfigCounterCapability.setter
    def ConfigCounterCapability(self, ConfigCounterCapability=None):
        """ /openconfig-acl:acl/config/openconfig-acl-ext:counter-capability
        System reported indication of how ACL counters are reported by the target
        """        
        self.__ConfigCounterCapability = ConfigCounterCapability   

    # StateCounterCapability
    @property
    def StateCounterCapability(self):
        """ /openconfig-acl:acl/state/counter-capability
        System reported indication of how ACL counters are reported
by the target
        """
        return self.__StateCounterCapability
    
    @StateCounterCapability.setter
    def StateCounterCapability(self, StateCounterCapability=None):
        """ /openconfig-acl:acl/state/counter-capability
        System reported indication of how ACL counters are reported
by the target
        """        
        self.__StateCounterCapability = StateCounterCapability        


    def add_AclSet(self, AclSet):
        """ Adds AclSet(/openconfig-acl:acl/acl-sets/acl-set) instance inside AclBase (/openconfig-acl:acl)"""
        self.AclSet_dict[(AclSet.AclSetName, AclSet.AclSetType)] = AclSet
        AclSet.Acl = self
    
    def del_AclSet(self, AclSet):
        """ Deletes AclSet(/openconfig-acl:acl/acl-sets/acl-set) instance from AclBase (/openconfig-acl:acl)"""
        del(self.AclSet_dict[(AclSet.AclSetName, AclSet.AclSetType)])
        AclSet.Acl = None

    def _generate_bind(self, content="all", target_attr=None, parent=None):
        """
        Generate pyangbindings for the spytest message
        """
        if content not in ["all", "config", "state"]:
            raise ValueError("Invalid content type - {}".format(content))
        acl = openconfig_acl().acl
        
        # ConfigCounterCapability
        if self.ConfigCounterCapability is not None:
            if content == "config" or content == "all":
                acl.config.counter_capability = self.ConfigCounterCapability
        if target_attr == "ConfigCounterCapability":
            return acl.config.counter_capability
        # StateCounterCapability
        if self.StateCounterCapability is not None:
            if content == "state" or content == "all":
                acl.state._set_counter_capability(self.StateCounterCapability)
        if target_attr == "StateCounterCapability":
            return acl.state.counter_capability

        if content == "all" or content == "config":
            for key in self.AclSet_dict:
                self.AclSet_dict[key]._generate_bind(content=content, parent=acl)
        
        if target_attr is not None:
            return None
        return acl
    

    def get_path(self, ui, target_attr=None):
        if target_attr is None:
            rest_template = self.__yang_path_rest
            gnmi_template = self.__yang_path_gnmi
        else:
            rest_template = self.__yang_path_rest_dict[target_attr]
            gnmi_template = self.__yang_path_gnmi_dict[target_attr]

        if ui == "rest":
            return rest_template
        else:
            return gnmi_template
```

### 4.1.1.1.2.2 Sample Base Class

Below class is generated for XPATH ***/openconfig-acl:acl/acl-sets/acl-set***

```python
##############################################################
##############################################################
##### THIS IS AN AUTO-GENERATED FILE PLEASE DO NOT EDIT ######
##############################################################
##############################################################

from apis.yang.codegen.base import Base
from collections import OrderedDict
from apis.yang.codegen.bindings.acl.acl_sets import acl_sets

class AclSetBase(Base):
    """
    List of ACL sets, each comprising of a list of ACL
entries
    """
    def __init__(self, Name, Type, Acl):
        super(AclSetBase, self).__init__()
        
        # Corresponding YANG Path
        self.__yang_path = "/openconfig-acl:acl/acl-sets/acl-set"
        
        # Corresponding YANG Path in RESTCONF format
        self.__yang_path_rest = "/openconfig-acl:acl/acl-sets/acl-set={},{}"
        self.__yang_path_rest_dict = OrderedDict()
        
        # Corresponding YANG Path in GNMI format
        self.__yang_path_gnmi = "/openconfig-acl:acl/acl-sets/acl-set[name={}][type={}]"
        self.__yang_path_gnmi_dict = OrderedDict()

        # Invokes setter for leafs/leaf-lists
        self.Name = Name
        self.Type = Type

        # AclSetName
        self.__yang_path_rest_dict["AclSetName"] = "/openconfig-acl:acl/acl-sets/acl-set={},{}/name"
        self.__yang_path_gnmi_dict["AclSetName"] = "/openconfig-acl:acl/acl-sets/acl-set[name={}][type={}]/name"

        # AclSetType
        self.__yang_path_rest_dict["AclSetType"] = "/openconfig-acl:acl/acl-sets/acl-set={},{}/type"
        self.__yang_path_gnmi_dict["AclSetType"] = "/openconfig-acl:acl/acl-sets/acl-set[name={}][type={}]/type"

        # ConfigName
        self.__yang_path_rest_dict["ConfigName"] = "/openconfig-acl:acl/acl-sets/acl-set={},{}/config/name"
        self.__yang_path_gnmi_dict["ConfigName"] = "/openconfig-acl:acl/acl-sets/acl-set[name={}][type={}]/config/name"

        # ConfigType
        self.__yang_path_rest_dict["ConfigType"] = "/openconfig-acl:acl/acl-sets/acl-set={},{}/config/type"
        self.__yang_path_gnmi_dict["ConfigType"] = "/openconfig-acl:acl/acl-sets/acl-set[name={}][type={}]/config/type"

        # StateName
        self.__yang_path_rest_dict["StateName"] = "/openconfig-acl:acl/acl-sets/acl-set={},{}/state/name"
        self.__yang_path_gnmi_dict["StateName"] = "/openconfig-acl:acl/acl-sets/acl-set[name={}][type={}]/state/name"

        # StateType
        self.__yang_path_rest_dict["StateType"] = "/openconfig-acl:acl/acl-sets/acl-set={},{}/state/type"
        self.__yang_path_gnmi_dict["StateType"] = "/openconfig-acl:acl/acl-sets/acl-set[name={}][type={}]/state/type"

        # Dict for child lists
        self.AclSetAclEntriesAclEntry_dict = OrderedDict()

        # Parent's ref
        self.Acl = Acl

    def __hash__(self):
        return hash((self.AclSetName, self.AclSetType))

    # Getters/Setters for attributes

    # Name
    @property
    def Name(self):
        """ Common Getter for __AclSetName, __ConfigName and __StateName
        Reference to the name list key
        """
        return {
            "AclSetName": self.AclSetName,
            "ConfigName": self.ConfigName,
            "StateName": self.StateName
        }
    
    @Name.setter
    def Name(self, Name=None):
        """ Common Setter for __AclSetName, __ConfigName and __StateName
        Reference to the name list key
        """        
        self.AclSetName = Name 
        self.ConfigName = Name
        self.StateName = Name 

    # Type
    @property
    def Type(self):
        """ Common Getter for __AclSetType, __ConfigType and __StateType
        Reference to the name list key
        """
        return {
            "AclSetType": self.AclSetType,
            "ConfigType": self.ConfigType,
            "StateType": self.StateType
        }
    
    @Type.setter
    def Type(self, Type=None):
        """ Common Setter for __AclSetType, __ConfigType and __StateType
        Reference to the name list key
        """        
        self.AclSetType = Type 
        self.ConfigType = Type
        self.StateType = Type 

    # AclSetName
    @property
    def AclSetName(self):
        """ /openconfig-acl:acl/acl-sets/acl-set/name
        Reference to the name list key
        """
        return self.__AclSetName
    
    @AclSetName.setter
    def AclSetName(self, AclSetName=None):
        """ /openconfig-acl:acl/acl-sets/acl-set/name
        Reference to the name list key
        """        
        self.__AclSetName = AclSetName        

    # AclSetType
    @property
    def AclSetType(self):
        """ /openconfig-acl:acl/acl-sets/acl-set/type
        Reference to the type list key
        """
        return self.__AclSetType
    
    @AclSetType.setter
    def AclSetType(self, AclSetType=None):
        """ /openconfig-acl:acl/acl-sets/acl-set/type
        Reference to the type list key
        """        
        self.__AclSetType = AclSetType        

    # ConfigName
    @property
    def ConfigName(self):
        """ /openconfig-acl:acl/acl-sets/acl-set/config/name
        The name of the access-list set
        """
        return self.__ConfigName
    
    @ConfigName.setter
    def ConfigName(self, ConfigName=None):
        """ /openconfig-acl:acl/acl-sets/acl-set/config/name
        The name of the access-list set
        """        
        self.__ConfigName = ConfigName        

    # ConfigType
    @property
    def ConfigType(self):
        """ /openconfig-acl:acl/acl-sets/acl-set/config/type
        The type determines the fields allowed in the ACL entries
belonging to the ACL set (e.g., IPv4, IPv6, etc.)
        """
        return self.__ConfigType
    
    @ConfigType.setter
    def ConfigType(self, ConfigType=None):
        """ /openconfig-acl:acl/acl-sets/acl-set/config/type
        The type determines the fields allowed in the ACL entries
belonging to the ACL set (e.g., IPv4, IPv6, etc.)
        """        
        self.__ConfigType = ConfigType      

    # StateName
    @property
    def StateName(self):
        """ /openconfig-acl:acl/acl-sets/acl-set/state/name
        The name of the access-list set
        """
        return self.__StateName
    
    @StateName.setter
    def StateName(self, StateName=None):
        """ /openconfig-acl:acl/acl-sets/acl-set/state/name
        The name of the access-list set
        """        
        self.__StateName = StateName        

    # StateType
    @property
    def StateType(self):
        """ /openconfig-acl:acl/acl-sets/acl-set/state/type
        The type determines the fields allowed in the ACL entries
belonging to the ACL set (e.g., IPv4, IPv6, etc.)
        """
        return self.__StateType
    
    @StateType.setter
    def StateType(self, StateType=None):
        """ /openconfig-acl:acl/acl-sets/acl-set/state/type
        The type determines the fields allowed in the ACL entries
belonging to the ACL set (e.g., IPv4, IPv6, etc.)
        """        
        self.__StateType = StateType        

    def add_AclSetAclEntriesAclEntry(self, AclSetAclEntriesAclEntry):
        """ Adds AclSetAclEntriesAclEntry(/openconfig-acl:acl/acl-sets/acl-set/acl-entries/acl-entry) instance inside AclSetBase (/openconfig-acl:acl/acl-sets/acl-set)"""
        self.AclSetAclEntriesAclEntry_dict[(AclSetAclEntriesAclEntry.AclEntrySequenceId)] = AclSetAclEntriesAclEntry
        AclSetAclEntriesAclEntry.AclSet = self
    
    def del_AclSetAclEntriesAclEntry(self, AclSetAclEntriesAclEntry):
        """ Deletes AclSetAclEntriesAclEntry(/openconfig-acl:acl/acl-sets/acl-set/acl-entries/acl-entry) instance from AclSetBase (/openconfig-acl:acl/acl-sets/acl-set)"""
        del(self.AclSetAclEntriesAclEntry_dict[(AclSetAclEntriesAclEntry.AclEntrySequenceId)])
        AclSetAclEntriesAclEntry.AclSet = None

    def _generate_bind(self, content="all", target_attr=None, parent=None):
        """
        Generate pyangbindings for the spytest message
        """
        if content not in ["all", "config", "state"]:
            raise ValueError("Invalid content type - {}".format(content))
        if parent is None:
            acl_set = acl_sets().acl_set.add("{} {}".format(self.AclSetName, self.AclSetType))
        else:
            acl_set = parent.acl_sets.acl_set.add("{} {}".format(self.AclSetName, self.AclSetType))
        
        # ConfigName
        if self.ConfigName is not None:
            if content == "config" or content == "all":
                acl_set.config.name = self.ConfigName
        if target_attr == "ConfigName":
            return acl_set.config.name
        # ConfigType
        if self.ConfigType is not None:
            if content == "config" or content == "all":
                acl_set.config.type = self.ConfigType
        if target_attr == "ConfigType":
            return acl_set.config.type

        # StateName
        if self.StateName is not None:
            if content == "state" or content == "all":
                acl_set.state._set_name(self.StateName)
        if target_attr == "StateName":
            return acl_set.state.name
        # StateType
        if self.StateType is not None:
            if content == "state" or content == "all":
                acl_set.state._set_type(self.StateType)
        if target_attr == "StateType":
            return acl_set.state.type

        if content == "all" or content == "config":
            for key in self.AclSetAclEntriesAclEntry_dict:
                self.AclSetAclEntriesAclEntry_dict[key]._generate_bind(content=content, parent=acl_set)
        
        if target_attr is not None:
            return None
        return acl_set
    
    def get_keys(self):
        return (str(self.AclSetName), str(self.AclSetType))

    def get_path(self, ui, target_attr=None):
        if target_attr is None:
            rest_template = self.__yang_path_rest
            gnmi_template = self.__yang_path_gnmi
        else:
            rest_template = self.__yang_path_rest_dict[target_attr]
            gnmi_template = self.__yang_path_gnmi_dict[target_attr]

        if ui == "rest":
            return rest_template.format(*self.get_keys())
        else:
            return gnmi_template.format(*self.get_keys())
```

### 4.1.1.2 Derived class Rules
- Derived classes will be generated for all base classes. The derived class is an editable class, this is the class the test case needs to be imported and used. 
- Constructor will be generated with all leafs initialized to None (only for non-key leafs). This will invoke base class constructor.

### 4.1.1.2.1 Sample Derived Class

Below class is generated for XPATH ***/openconfig-acl:acl***

```python
##############################################################
##############################################################
##### THIS IS AN AUTO-GENERATED FILE PLEASE DO NOT EDIT ######
##############################################################
##############################################################

from apis.yang.codegen.response import Response
from apis.yang.utils.common import NorthBoundApi
from apis.yang.codegen.error_constants import *

from apis.yang.codegen.messages.acl.Base.Acl import AclBase
try:
    from apis.yang.codegen.messages.acl import Acl_klish
except ImportError:
    pass

class Acl(AclBase):
    """/openconfig-acl:acl
    Top level enclosing container for ACL model config
and operational state data
    """
    def __init__(self,  CounterCapability=None):
        super(Acl, self).__init__( CounterCapability)

    def configure_klish(self, dut, operation="update", target_attr=None, target_path=None, success=True, ignore_error=False, **kwargs):
        ''' Developers will implement this '''
        if 'Acl_klish' in globals():
            try:
                return Acl_klish.configure_klish(self, dut, operation=operation, target_attr=target_attr, target_path=target_path, success=success, ignore_error=ignore_error, **kwargs)
            except AttributeError:
                pass
        return Response(NorthBoundApi.KLISH, status_code=UNIMPLEMENTED)

    def unConfigure_klish(self, dut, target_attr=None, target_path=None, success=True, ignore_error=False, **kwargs):
        ''' Developers will implement this '''
        if 'Acl_klish' in globals():
            try:
                return Acl_klish.unConfigure_klish(self, dut, target_attr=target_attr, target_path=target_path, success=success, ignore_error=ignore_error, **kwargs)
            except AttributeError:
                pass
        return Response(NorthBoundApi.KLISH, status_code=UNIMPLEMENTED)

    def verify_klish(self, dut, target_attr=None, target_path=None, success=True, ignore_error=False, error_response=None, **kwargs):
        ''' Users required to write code for klish '''
        if 'Acl_klish' in globals():
            try:
                return Acl_klish.verify_klish(self, dut, target_attr=target_attr, target_path=target_path, success=success, ignore_error=ignore_error, error_response=None, **kwargs)
            except AttributeError:
                pass
        return Response(NorthBoundApi.KLISH, status_code=UNIMPLEMENTED)
```

## 4.2 Subscription Support

Message classes will support gNMI subscription test cases similar to the existing APIs provided by
the `apis/yang/utils/gnmi` module.
Existing APIs operate on path and JSON payload.
New subscription related APIs provided by message classes will be wrappers for these existing APIs.
Developer can avoid dealing with paths and payloads to start subscriptions and verify notifications.
The message class instances used for configure/unconfigure steps can be reused in sunscription APIs.

Note that all the APIs discussed in this section automatically use "gnmi" UI.

### 4.2.1 Connection Management

The gNMI connection created by the spytest infra during init phase will be used for starting subscriptions.
Test case should not create new connection or close the shared connection by itself.

### 4.2.2 Creating Subscription

Message master base class will have a `subscribe()` method which can be used to create a subscription.
This method will accept the subscription mode, optional property name and other optional subscription
parameters as shown below.

```python
def subscribe(self, dut, mode, target_attr=None, target_path=None, timeout=None,
              target=None, origin=None, updates_only=False,
              suppress_redundant=False, sample_interaval=None):
    """Creates gNMI subscription for path self.get_path(ui="gnmi", target_attr=target_attr).
    Returns a RpcContext object which can be used for validating the notification messages.

    Parameters:
    dut             DUT name
    mode            Subscription mode -- should be one of "on_change", "sample", "target_defined",
                    "poll" or "once". Values are case insensitive.
    target_attr     An optional property name of this message class. Used to derive the subpath.
    target_path     Subpath inside this message class.
                    Only one of target_attr or target_path can be specified.
    timeout         Hard timeout for test case; in seconds. Used to break verification loop when
                    expected notifications are not received. It is recommended that developers pass
                    an appropriate timeout value based on the verification steps in the test case.
    target          A string value to be used as 'target' property of request path prefix.
    origin          A string value to be used as 'origin' property of request path prefix.
    updates_only    Boolean value for the 'updates_only' property in the request.
    suppress_redundant
                    Boolean value for the 'suppress_redundant' property in the request. Used only
                    when mode is 'sample' or 'target_defined'.
    sample_interval Number of seconds for the 'sample_interval' property in the request. Used only
                    when mode is 'sample' or 'target_defined'.
    """
```

Testcase should create a message class, fill the key attributes and invoke its subscribe() method.
Keys can be a wildcard character (`*`) too.
The subscribe() method will return a `RpcContext` object, which can be used for verifying notification messages.
`RpcContext` is a subclass of `Response` class.
Can use used to check the status of subscribe as well to verify the notifications.

Example usage:

```python
# Subscribe on_change with wildcard key
ctx = Message1(Key1="*").subscribe(dut, mode="on_change")
if not ctx.ok():
    st.report_fail("Subscription failed: " + ctx.message)
# Subscribe on_change with specific key
ctx = Message1(Key1="ABC").subscribe(dut, mode="on_change")
# Subscribe on_change for an attribute
ctx = Message1(Key1="*").subscribe(dut, mode="on_change", target_attr="Attr1")
# Subscribe on_change for a specific subpath
ctx = Message1(Key1="*").subscribe(dut, mode="on_change", target_path="state/attr1")
# Subscribe SAMPLE with 10s sample_interval and 1min timeout
ctx = Message1(Key1="*").subscribe(dut, mode="sample", timeout=60, sample_interval=10)
```

### 4.2.3 Subscribing to Multiple Message Paths

gNMI allows subscribing to multiple, unrelated paths in one rpc.
Message class's `subscribe()` functions can be used to subscribe to single path or set of
subpaths of single message object -- using `target_attr` and `target_path` options.
The `start_subscribe()` API of `apis.yang.utils.subscribe` module can be used to subscribe to
multiple unrelated paths (i.e, multiple message classes).
API signature is as explained below.

```python
def start_subscribe(dut, mode, path_infos, timeout=30, target=None, origin=None, updates_only=False,
              auto_close=True):
    """Creates gNMI subscription for paths. Returns a RpcContext object which can be used for
    validating the notification messages.

    Parameters:
    dut             DUT name
    mode            Subscription mode -- should be one of "on_change", "sample", "target_defined",
                    "poll" or "once". Values are case insensitive.
    path_infos      A PathInfo object or list of PathInfo objects indicating paths to subscribe to.
    timeout         Hard timeout for test case; in seconds.
    target          A string value to be used as 'target' property of request path prefix.
    origin          A string value to be used as 'origin' property of request path prefix.
    updates_only    Boolean value for the 'updates_only' property in the request.
    auto_close      Indicate if the rpc should be tracked for automatic cleanup via subscribe_cleanup
                    fixture. Enabled by default.
    """

class PathInfo:
    """PathInfo is a set of gNMI paths with its subscription options."""
    def __init__(self, msg_obj, target_attr=None, target_path=None,
                 sample_interval=None, suppress_redundant=False, heartbeat_interval=None):
        """Constructs a PathInfo object from a message class instance. This can represent
        one or multiple paths -- depending on the target_path, target_attr value.
        Also accepts SAMPLE subscription options -- sample_interval and suppress_redundant.
        """
```

**Note:** Message class's `subscribe()` API is sufficient for most of the functional tests.
The `start_subscribe()` can be used for advanced/focussed tests of telemetry server.

Example usage:

```python
from apis.yang.utils.subscribe import start_subscribe, PathInfo

p1 = PathInfo(Message1())
p2 = PathInfo(Message2(), target_attr="AttrX")
p3 = PathInfo(Message3(), target_path="SubpathY")
ctx = start_subscribe(dut, mode="on_change", path_infos=[p1, p2, p3])
if not ctx.ok():
    st.report_fail()
```

### 4.2.4 Verifying Notifications

The `RpcContext.verify()` function can be used to receive and inspect the notification data.
It accepts a single or a list of `Notification` objects indicating the expected data.
`Notification` is an abstract class; developer needs to use one of its subclasses `UpdateNotification` or `DeleteNotification`.
Both these notification objects are to be constructed using message class instances.
Notification objects for multiple message classes can also be passed together to the verify function.
API signatures are explained below.

```python
class Notification(ABC):
    """Abstract base class to express expected notification data"""

class UpdateNotification(Notification):
    def __init__(self, data, prefix=None, target_attr=None, target_path=None, iterations=1, interval=20):
        """Expected update notification data.
        Parameters:
        data        A message class containing the expected notification data.
                    if this message class a parent message in the class hierarchy, the parent
                    context should be filled either in this message instance itself; or
                    can be separately passed as the prefix.
        prefix      Optional message class that defines the parent context for data.
        target_attr Property name indicating the subpath inside the data class.
        target_path Subpath inside the data class.
                    Only one of target_attr or target_path can be specified.
        iterations  Expected number of notification messages containing the values in data class. 
        interval    Number of seconds between notifications when iterations > 1.
        """

class DeleteNotification(Notification):
    def __init__(self, data, prefix=None, target_attr=None, target_path=None):
        """Expecteds delete notification path.
        Parameters:
        data        A message class or string path indicating the expected delete path.
        prefix      Optional message class that defines the parent context for data.
        target_attr Property name indicating the subpath inside the data class.
        target_path Subpath inside the data class.
                    Only one of target_attr or target_path can be specified.
        """

def RpcContext(Response):
    def verify(notifications, sync=False):
        """Verify expected notification values are received. Blocks till expected values
        are received or the timeout (specified in the subscribe API) and returns True.
        Returns False as soon as it encounters an unexpected notification data.

        Parameters:
        notifications   A Notification object or a list of Notification objects indicating
                    expected notification data.
        sync        Indicates whether a sync message is expected. When True, this function
                    waits for a sync message after expected notification data is received.
                    It is an error if sync message is received when it is not expected or
                    all expected notification data are not received yet.
        """

    def poll(self, notifications):
        """Sends a gNMI poll message to the DUT and verifies the notifications.
        Should be used only when the subscription is created with mode="poll".
        Waits till all the expected notifications are received followed by a
        sync message; or a timeout.

        Parameters:
        notifications   A Notification object or a list of Notification objects indicating
                    expected notification data.
        """

    def close(self):
        """Close the subscribe rpc."""
```

The `RpcContext.verify()` function is a wrapper for the existing `verify_notifications` function
in `apis/yang/utils/gnmi` module.
Refer to the `verify_notifications` documentation for more details on the verification logic.

The `RpcContext.poll()` function sends a gNMI poll message on current subscribe rpc and verifies
the received notifications against specified `Notification` objects.

Example usage:

```python
from apis.yang.utils.subscribe import UpdateNotification, DeleteNotification

# Verify initial sync notifications with no data
if not ctx.verify(None, sync=True):
    st.report_fail()
# Verify notifications with one update
if not ctx.verify(UpdateNotification(msg1)):
    st.report_fail()
# Verify notifications with multiple updates and deletes
u1 = UpdateNotification(msg1)
u2 = UpdateNotification(msg2, target_attr="AttrX")
u3 = UpdateNotification(msg3, target_path="state/foo")
d1 = DeleteNotification(msg4)
d2 = DeleteNotification(msg5, target_attr="AttrY")
if not ctx.verify(notifications=[u1, u2, u3, d1, d2]):
    st.report_fail()
```

### 4.2.5 Closing Subscription

Subscription rpc can be closed using `RpcContext.close()` function.
But it is recommended use the common `subscribe_cleanup` fixture to automatically close the
subscription at the end of the test case.
It will track the `RpcContext` objects created in the test case closes them on exit.

Example usage:

```python
from apis.yang.utils.subscribe import subscribe_cleanup

def test_subscribe_example(subscribe_cleanup):
    ctx = SomeMessage().subscribe(dut, mode="on_change")
    if not ctx.ok():
        st.report_fail("msg", "Subscription failed: " + ctx.message)
    ....
```

## 4.3 RPC Support

The generated message classes for Yang RPC support are placed in the following directory structure for the openconfig-tam yang module. These classes should not be edited.

```text
    apis/yang/codegen/messages/
    |
    |_<module> Example:tam, contains all Derived classes
        |_TamRpc.py
        |_Base (directory containing Base classes)
          |_TamRpc.py
```


## 4.4 GNOI Support

The generated gNOI python binding message and stub classes are placed in the following directory.

```text
    apis/yang/codegen/gnoi_bindings/
    |
    |_<proto>_pb2.py Example:sonic_gnoi_pb2.py
    |_<proto>_pb2_rpc.py
```

Developers should not need to refer these. The .proto service interface definition files (IDL) should contain the information needed for writing Test Cases.


# 5 Developer Steps

## 5.1 Copying Relevant YANGs

Yang models will be placed under **brcm-spytest/apis/yang/modules**

Developers should be copying relevant yangs from sonic-mgmt-common/build/yang to brcm-spytest/apis/yang/modules directory.

When developer modifies the YANG contents **brcm-spytest/apis/yang/modules** or rebases with newer versions from **sonic-mgmt-common/build/yang**, they are required to regenerate Messages and Bindings using below steps.

Once the Messages and Bindings are regenerated, they need to be committed along with the YANG changes.

## 5.2 Message Generation

- Use **brcm-spytest/apis/yang/codegen/tools/generate_msg_class.sh** script to generate the message class

**Usage**

```text
generate_msg_class.sh [--over-write-derived-class] <YANG-1> ... <YANG-N>               
```

**Example**

```text
brcm-spytest/apis/yang/codegen/tools/generate_msg_class.sh openconfig-acl.yang extensions/openconfig-acl-ext.yang
```

***NOTE:*** Messages will be generated under brcm-spytest/apis/yang/codegen/messages

## 5.3 Yang Binding Generation

Script **brcm-spytest/apis/yang/codegen/tools/generate_bindings.sh** will generate the pyangbind
bindings for the required YANG files.
It will be automatically triggered by the *generate_msg_class.sh* script.
Runs the pyangbind generator in "split-class-dir" mode, which generates a separate python module
directory for every container and list nodes.
All binding artifacts will be generated under **brcm-spytest/apis/yang/codegen/bindings** directory.
Developer must commit these generated files to the spytest repo as-is.

Usage:

```text
generate_bindings.sh <YANG-1> ... <YANG-N>
```

## 5.4 Testcase Sample For Configuration

Sample test logic which Adds ACL and Rule

```python

from apis.yang.codegen.messages.acl.Acl import Acl
from apis.yang.codegen.messages.acl.AclSet import AclSet
from apis.yang.codegen.messages.acl.AclSetAclEntriesAclEntry import AclSetAclEntriesAclEntry

dut = None
acl=Acl(ConfigCounterCapability="INTERFACE_ONLY", StateCounterCapability="INTERFACE_ONLY")
aclSet1 = AclSet(AclSetName="MYACL1", AclSetType="ACL_IPV4")

aclSet1.configDescription = "sample"
aclSet2 = AclSet(AclSetName="MYACL2", AclSetType="ACL_IPV4", ConfigDescription="some desc")
acl_entry = AclSetAclEntriesAclEntry(AclEntrySequenceId=1, ConfigDescription="some desc")
aclSet1.add_AclSetAclEntriesAclEntry(acl_entry)
acl.add_AclSet(aclSet1)
acl.add_AclSet(aclSet2)

acl.configure(dut, target_path="/acl-sets/acl-set")
acl.configure_rest(dut, target_path="/acl-sets/acl-set")
acl_entry.unconfigure()
acl.unconfigure(dut)

```

## 5.5 Testcase Sample For Verification

```python

from apis.yang.codegen.messages.acl.Acl import Acl
from apis.yang.codegen.messages.acl.AclSet import AclSet
from apis.yang.codegen.messages.acl.AclSetAclEntriesAclEntry import AclSetAclEntriesAclEntry

dut = None
acl=Acl(ConfigCounterCapability="INTERFACE_ONLY", StateCounterCapability="INTERFACE_ONLY")
aclSet1 = AclSet(AclSetName="MYACL1", AclSetType="ACL_IPV4")

aclSet1.configDescription = "sample"
aclSet2 = AclSet(AclSetName="MYACL2", AclSetType="ACL_IPV4", ConfigDescription="faraaz2")
acl_entry = AclSetAclEntriesAclEntry(AclEntrySequenceId=1, ConfigDescription="cool")
aclSet1.add_AclSetAclEntriesAclEntry(acl_entry)
acl.add_AclSet(aclSet1)
acl.add_AclSet(aclSet2)

acl.verify_rest(dut, ui="rest")

```

## 5.6 Testcase Sample For RPC

Following is an example test case to execute and verify Yang RPC for clearing a Flowgroup Counter.

```python
from apis.yang.codegen.messages.tam.TamRpc import ClearFlowgroupCountersRpc
from apis.yang.codegen.yang_rpc_service import YangRpcService

def test_tam_rpc_example():
    """Demonstrate TAM Yang RPC"""

    st.log("[Step-1] Create Yang RPC Service .......")

    service = YangRpcService()

    st.log("[Step-2] Create TAM RPC .......")

    # Input
    rpc = ClearFlowgroupCountersRpc()
    rpc.Input.name = "TestCounter"

    # Expected Output
    rpc.Output.status = 0
    rpc.Output.status_detail = [""]

    st.log("[Step-3] Execute TAM Yang RPC .......")

    result = service.execute(data.D1, rpc, verify=True)
    if not result.ok():
        st.report_fail("msg", "ClearFlowgroupCounter RPC: Failed: " \
            + result.message)

    st.log("[Step-4] RPC Executed .......")
    st.log("result: {}".format(result.data))

    st.report_pass("test_case_passed")
```

## 5.7 Testcase Sample For Subscription

Following is an example test case to subscribe for ACL changes and verify the notifications
for ACL create and delete cases.

```python
from apis.yang.utils.subscribe import UpdateNotification, DeleteNotification, subscribe_cleanup

def test_onchange_acl_example(subscribe_cleanup):
    # Subscribe ON_CHANGE of ACL
    rpc = AclSet(Name="*", Type="*").subscribe(dut, mode="on_change")
    if not rpc.ok():
        st.report_fail("msg", "Subscribe failed: " + rpc.message)
    # There should not be any sync updates -- ACL is not configured yet
    if not rpc.verify(None, sync=True):
         st.report_fail("msg", "Not expecting any sync update")
    # Create an ACL
    acl1 = AclSet(Name="ONE", Type="ACL_IPV4", Description="Hello, world!")
    acl1.configure(dut, operation=Operation.CREATE)
    # Look for the update notification...
    if not rpc.verify(UpdateNotification(acl1)):
        st.report_fail("msg", "Invalid notification after ACL creation")
    # Change description
    acl1.Description = "Foo/bar"
    acl1.configure(dut)
    # Look for update notification for description leaf nodes only..
    if not rpc.verify(UpdateNotification(acl1, target_attr="Description")):
        st.report_fail("msg", "Invalid notification after ACL description change")
    # Delete the ACL
    acl1.unconfigure(dut)
    # Look for the delete notification...
    if not rpc.verify(DeleteNotification(acl1)):
        st.report_fail("msg", "Invalid notification after ACL delete")
    st.report_pass("test_case_passed")
```

## 5.8 Copying Relevant Proto Definition Files

Proto definition files will be placed under **brcm-spytest/apis/yang/proto/gnoi**

Developers should be copying relevant *literal asterisks*.proto from sonic-telemetry/proto/gnoi to brcm-spytest/apis/yang/proto/gnoi directory.

When developer modifies the .proto contents **brcm-spytest/apis/yang/proto/gnoi** or rebases with newer versions from **sonic-telemetry/proto/gnoi**, they are required to regenerate gNOI message bindings using below steps.

Once the gNOI message bindings are regenerated, they need to be committed along with the YANG changes.

Note: Due to some issues with importing module names with "." in them (Eg: "github.com"), please replace imports in the .proto IDL files which contain them. For example,

** replace **

```
import "github.com/gogo/protobuf/gogoproto/gogo.proto"
```

** with **

```
import "gogoproto/gogo.proto"
```

## 5.9 Proto Binding Generation

Script **brcm-spytest/apis/yang/codegen/tools/generate_gnoimsgs.sh** will generate the proto bindings for the required .proto files. It downloads some dependencies, performs a few sanity checks and runs the protoc compiler which generates the message bindings, and gRPC service stubs.

All generated artifacts will be placed under **brcm-spytest/apis/yang/codegen/gnoi_bindings** directory. Developer must commit these generated files to the spytest repo as-is.

Usage:

```text
generate_gnoimsgs.sh <PROTO-1> ... <PROTO-N>
```

## 5.10 Testcase Sample For GNOI

Following is an example test case to execute and verify gNOI RPCs for Audit Log clearing and retrieval.

```python

from apis.yang.codegen.gnoi_service import GnoiService
from apis.yang.codegen.gnoi_rpc import GnoiRpc

def test_audit_log_gnoi_example():
    """Demonstrate Clear, and Get Audit Log GNOI RPC"""

    st.log("[Step-1] Create GNOI RPC Service .......")

    service = GnoiService(proto="sonic_gnoi", name="SonicService")
    pb2 = service.pb2_module()

    st.log("[Step-2] Create Clear Audit Log RPC .......")

    # Input
    rpc = GnoiRpc(name="ClearAuditLog")
    rpc.request=pb2.ClearAuditLogRequest()

    # Expected Output
    rpc.response=pb2.ClearAuditLogResponse(output=pb2.SonicOutput())

    st.log("[Step-3] Execute Clear Audit Log GNOI RPC .......")

    result = service.execute(data.D1, rpc, verify=True)
    if not result.ok():
        st.report_fail("msg", "Clear Audit Log GNOI RPC: Failed: " \
            + result.message)

    st.log("[Step-4] RPC Executed .......")
    st.log("result: {}".format(result.data))

    st.log("[Step-5] Create Get Audit Log RPC .......")

    # Input
    rpc = GnoiRpc(name="GetAuditLog")
    rpc.request=pb2.GetAuditLogRequest(
        input=pb2.GetAuditLogRequest.Input(content_type='all'))

    # Expected Output
    rpc.response=pb2.GetAuditLogResponse(
        output=pb2.GetAuditLogResponse.AuditOutput(audit_content=[""]))

    st.log("[Step-6] Execute Get Audit Log GNOI RPC .......")

    result = service.execute(data.D1, rpc, verify=False)
    if not result.ok():
        st.report_fail("msg", "Get Audit Log GNOI RPC: Failed: " \
            + result.message)

    st.log("[Step-7] RPC Executed .......")
    st.log("result: {}".format(result.data))

    st.report_pass("test_case_passed")

```
