---
title: "Guidelines to use device models in the North-Bound Interface of SDN controllers"
abbrev: "Device model NBI controller"
category: info

docname: draft-ogondio-opsawg-device-models-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Operations and Management Area Working Group"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "Operations and Management Area Working Group"
  type: "Working Group"
  mail: "opsawg@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/opsawg/"
  github: "vlopezalvarez/draft-ogondio-opsawg-device-models"
  latest: "https://vlopezalvarez.github.io/draft-ogondio-opsawg-device-models/draft-ogondio-opsawg-device-models.html"

author:
 -
    fullname: Oscar Gonzalez de Dios
    organization: Telefonica
    email: "oscar.gonzalezdedios@telefonica.com"
 -
    fullname: Victor Lopez
    organization: Nokia
    email: "victor.lopez@nokia.com"

normative:

informative:


--- abstract

This document establishes guidelines for structuring a data model to facilitate the reuse of device models within the North-Bound Interface of SDN controllers. The objective is to enhance the reutilization of device models in various network scenarios.

--- middle

# Introduction

Network operators need to efficiently manage and configure network elements throughout their infrastructure. By implementing network-wide management and configuration practices, operators can achieve centralized control and visibility over their network elements. This enables them to streamline operations, monitor performance, and promptly respond to network events. Moreover, network-wide management facilitates the enforcement of standardized policies and configurations, ensuring consistent behavior and minimizing the risk of errors or misconfigurations that may result in service disruptions. Additionally, it enables operators to implement proactive actions, such as performance optimization, load balancing, and security policies across the entire network, fostering a more secure and efficient infrastructure.

The ability of reusing device models play a crucial role in network management. Multiple teams within the operator, such as network engineering, operations, and planning, can benefit from accessing these device models. These models serve as a common language for understanding and configuring network elements, ensuring consistency and interoperability across different teams and systems. The utilization of models from various teams is a key requirement for network operators.

The Internet Engineering Task Force (IETF) has made remarkable progress in defining device models to manage network element capabilities. These device models, often represented using YANG data modeling language, provide a structured, standardized approach to configure, monitor, and manage various network devices and their features. By leveraging YANG models, network operators can effectively manage the network element functionalities. These models not only streamline network management but also promote interoperability between different vendors and platforms, fostering a more efficient and robust networking ecosystem.

Some examples of these network models are:

* ietf-routing-policy {{!RFC8349}}: This YANG model defines a generic data model for managing routing policies that can be applied to various routing protocols. The model provides a framework for creating, modifying, and applying routing policies, which allows defining how routes are selected, filtered, and modified. The "ietf-routing-policy" model covers features like policy definition, policy attachment, route filters, and route actions.

* ietf-bgp-policy {{!I-D.ietf-idr-bgp-model}}: This YANG model defines a data model for the Border Gateway Protocol (BGP). The "ietf-bgp-policy" model is designed to configure and manage BGP routers and sessions, as well as provide a representation of BGP operational state data. The model covers BGP-specific features such as peer configuration, address families, route filters, and route actions. The model is intended to work alongside the "ietf-routing-policy" model to manage BGP routing policies.

* ietf-access-list {{!RFC8519}}: This YANG model provides a data model for configuring and managing network access control lists (ACLs). This model provides a generic structure for representing ACLs, along with the ability to define rules for permitting, denying, or assigning a specific action to matching packets.

Software-Defined Networking (SDN) controllers facilitate seamless communication and coordination between high-level management systems and the underlying network infrastructure. This arrangement enables efficient translation of network-wide policies and objectives, defined by the Operations Support Systems (OSS), into granular, device-specific configurations and commands for the network elements. A similar concept applies for orchestration scenarios like in network slicing. Consequently, SDN controllers are typically placed as intermediate entities between the OSS and the network elements. {{SDNsce}} represents this scenario, where the SDN controller exposes its North-Bound Interface (NBI) to the OSS or orchestration layer.

~~~~
        +-----+  +------+
        | OSS |  | Orch |
        +-----+  +------+
           |        | NBI
           v        v
     +----------------+
     | SDN Controller |
     +----------------+
      |      |       | SBI
      v      v       v
  +-----+ +-----+ +-----+
  | NE1 | | NE2 | | NE3 |
  +-----+ +-----+ +-----+
~~~~
{: #SDNsce title='SDN scenario' artwork-align="center"}

{{SDNsce}} illustrates the hierarchical relationship between the OSS, SDN controller and the network elements. Typically, the OSS acts as the central management system responsible for overseeing the entire network. Similarly, an orchestrator acts with a similar role in scenarios like network slicing. The SDN controller, positioned in the middle, acts as an intermediary, facilitating communication and coordination between the OSS and the network elements. At the bottom, the network elements are directly controlled and configured by the SDN controller. This archicture enables efficient translation of high-level network policies into device-specific configurations, ultimately streamlining network management and decoupling the systems from the network evolution.

Device models like "ietf-routing-policy" {{!RFC8349}}, "ietf-bgp-policy" {{I-D.ietf-idr-bgp-model}} or "ietf-access-list" {{!RFC8519}} were defined to be applicable in the South-Bound Interface (SBI) of the SDN controller. Ts a result, these models do not inherently possess the necessary network concepts to make them directly applicable in the NBI of the SDN controller.

Within the scope of the IETF, efforts can be focused on two approaches when it comes to creating network models for device models. The first approach involves creating network models specific to each device model, while the second approach entails developing a generic and reusable structure for all models. This document puts forth a proposal for a reusable structure, aligning with the latter approach.

## Terminology and Notations

TBD

## Requirements Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in  {{!RFC2119}}, {{!RFC8174}} when, and only when, they appear in all capitals, as shown here.

## Prefix in Data Node Names

  In this document, names of data nodes and other data model objects will be prefixed using the standard prefix associated with the corresponding YANG imported modules, as shown in the following table.

| Prefix  | Yang Module            | Reference    |
| ------  | ---------------------- | ------------ |
| ntwdev  | ietf-network-device    | RFCXXX       |
{: #tab-prefixes title="Prefixes and corresponding YANG modules"}

RFC Editor Note:
Please replace XXXX with the RFC number assigned to this document if the document becomes a RFC. Please remove this note in that case.

# Use cases

##  Device Config as a Service

Device Config as a Service involves the utilization of device models by an OSS to configure network elements through an SDN controller. In this scenario, the SDN controller acts as an intermediary between the OSS and the network elements, providing a configuration service for each network element.

By leveraging device models, the OSS gains a common representation of the network elements' capabilities and configuration parameters. These device models define the desired configuration for specific functionalities, such as ACLs or routing policies. The OSS utilizes these device models to define the desired configuration for each network element.

Through the SDN controller, the OSS can send the configuration instructions based on the device models to the respective network elements. The SDN controller translates and applies the configuration, ensuring consistency and correctness across the network. Moreover, the mediation of the SDN controller facilitates the access to the network elements from several users, applications or OSS minimizing the impact on the device.

## Profiles

By leveraging device models, network operators can design profile that represent configurations for specific network functionalities, protocols, or services. These templates serve as reusable building blocks, encapsulating best practices, and ensuring consistency in network configuration and management. Once a profile is created, it can be applied to one or multiple network elements, either individually or in groups, depending on the specific network requirements.

This approach not only simplifies the configuration process but also reduces the likelihood of errors and misconfigurations, ultimately improving network stability and performance. Moreover, this process facilitates the lifecycle of the configurations enabling updating the profiles and, later, the network elements in a consistent manner across multiple network elements as a network-wide operation.

# Guidelines to use device models in the NBI of SDN controllers

This section outlines two key concepts for the guidelines on utilizing device models in the NBI of SDN controllers: (1) groups of network elements and (2) a YANG structure for extending device models to enable network-wide utilization.

## Groups of network elements

The management of groups of network elements is a requirement to cover the previous use cases. It is intended to have a YANG model to tag a group of network elements under the same identifier, so the operator can apply a given configuration in a set of devices. This document defines a module called "grp-ntw-elements", which provides a structured approach to represent and manipulate groups of network elements, enabling efficient network management and configuration.

The "grp-ntw-elements" module defines a YANG model for representing a group of network elements. Within the module, there is a list called "grp-ntw-elements" that includes the groups of network elements. Each group is uniquely identified by the "grp-ne-id" leaf, which has a string data type and represents the group's identifier. The "grp-ntw-elements" list has a nested list called "ntw-elements" to specify the individual network elements within each group. The "ntw-elements" list has a key of "ne-id" to uniquely identify each network element. The "ne-id" leaf represents the identifier of each network element and has a string data type.

~~~~
module: grp-ntw-elements
  +--rw grp-ntw-elements* [grp-ne-id]
     +--rw grp-ne-id       string
     +--rw ntw-elements* [ne-id]
        +--rw ne-id    string
~~~~
{: #grp-ntw-elements-tree-st title="Group of network elements" artwork-align="center"}

## YANG structure for extending the models

The proposal of this work is to propose a structure to enable the reutilization of the device models in network scenarios. The objective is to create a YANG model with a list of instances referencing the device model and providing a nested structure to store deployment information for network elements associated with each instance in the list. The guideline is to follow the next structure in the list:

* A key called devmod-name representing the name for each entry in the list.

* A leaf named devmod-name of type string that serves as a string identifier for the list entry.

* An import of the device model.

* A container named deployment that includes:

+ A list called ntw-elements with the following elements:

+ A leaf named ne-id of type string that serves as the network element identifier (which is the key).

+ A leaf named devmod-alias of type string that serves as the device module alias for the deployment.

+ A list called grp-ntw-elements with the following elements:

+ A leaf named grp-ne-id of type string that serves as the network element identifier (which is the key).

+ A leaf named devmod-alias of type string that serves as the device module alias for the deployment.

Let us assume that there is a device model called "foo.yang". The tree of the "foo.yang" model is shown in {{foo-tree-st}}.

~~~~
module: foo
  +--rw foo?   empty
~~~~
{: #foo-tree-st title="Foo Tree Structure" artwork-align="center"}

This document proposes the creation of a module that consists of a list of instances from the "foo.yang" model. To iterate through the list, a key named "devmod-name" must be defined, which will be a string. Additionally, the new model will import "foo.yang". Lastly, a container called "deployment" will encompass a list of network elements, with each element identified by the "ne-id" and having a "devmod-alias" as an alias for the "devmod-name" configuration in the network element.

An example of the proposed structure {{foo-ntwdev-tree-st}}.

~~~~
module: foo-ntwdev
  +--rw devmod-list* [devmod-name]
     +--rw devmod-name    string
     +--rw foo?           -> /foo:foo
     +--rw deployment
        +--rw ntw-elements* [ne-id]
        |  +--rw ne-id           string
        |  +--rw devmod-alias?   string
        +--rw grp-ntw-elements* [grp-ne-id]
           +--rw grp-ne-id       string
           +--rw devmod-alias?   string
~~~~
{: #foo-ntwdev-tree-st title="Foo Tree Structure" artwork-align="center"}

## YANG Model for NBI SDN controllers scenarios

~~~~~~~~~~
<CODE BEGINS> file example-foo-ntwdev.yang
module foo-ntwdev {
  namespace "urn:example:foo-ntwdev";
  prefix "netdevfoo";

  import foo {
    prefix "foo";
  }

  organization "Example Organization";
  contact "example@example.com";
  description "YANG model for foo-dev.";
  revision "2023-03-30" {
    description "Initial revision.";
    reference "RFC XXXX: YANG Model for foo-dev";
  }

  list devmod-list {
    key "devmod-name";
    description "List of foo.yang instances 2";

    leaf devmod-name {
      type string;
      description "Name for the list.";
    }

    leaf foo {
      type leafref {
        path "/foo:foo";
      }
      description "Reference to foo leaf from foo.yang";
    }

    container deployment {
      description "Deployment container.";

      list ntw-elements {
        key "ne-id";
        description "List of network elements.";

        leaf ne-id {
          type string;
          description "Network element identifier.";
        }
        leaf devmod-alias {
          type string;
          description "Device module alias for the deployment.";
        }
      }

      list grp-ntw-elements {
        key "grp-ne-id";
        description "List of group of network elements.";

        leaf grp-ne-id {
          type string;
          description "Group of network element identifier.";
        }
        leaf devmod-alias {
          type string;
          description "Device module alias for the deployment.";
        }
      }
    }
  }
}

<CODE ENDS>
~~~~~~~~~~


# Security Considerations

The YANG module targeted in this document defines a schema for data that is designed to be accessed via network management protocols such as RESTCONF {{!RFC8040}}.  The lowest RESTCONF layer is HTTPS, and the mandatory-to-implement secure transport is TLS {{!RFC5246}}.

The NETCONF access control model {{!RFC6536}} provides the means to restrict access for particular NETCONF or RESTCONF users to a preconfigured subset of all available NETCONF or RESTCONF protocol operations and content.

There are a number of data nodes defined in this YANG module that are writable/creatable/deletable (i.e., config true, which is the default). These data nodes may be considered sensitive or vulnerable   in some network environments. Write operations (e.g., edit-config) to these data nodes without proper protection can have a negative effect on network operations.

# IANA Considerations

TBC


# Implementation Status

This section will be used to track the status of the implementations of the model. It is aimed at being removed if the document becomes RFC.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
