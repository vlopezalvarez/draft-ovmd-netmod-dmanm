---
title: "An Approach to Expose 'Device Models'-as-'Network Models'"
abbrev: "DMaNM"
category: info

docname: draft-ovmd-netmod-dmanm-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Network Modeling"
keyword:
 - Device models
 - Network models

venue:
  group: "Network Modeling"
  type: "Working Group"
  mail: "netmod@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/netmod/"
  github: "vlopezalvarez/vlopezalvarez-draft-ovmd-netmod-dmanm"
  latest: "https://vlopezalvarez.github.io/vlopezalvarez-draft-ovmd-netmod-dmanm/draft-ovmd-netmod-dmanm.html"

author:
 -
    fullname: Oscar Gonzalez de Dios
    organization: Telefonica
    email: "oscar.gonzalezdedios@telefonica.com"
 -
    fullname: Victor Lopez
    organization: Nokia
    email: "victor.lopez@nokia.com"
 -
    fullname: Mohamed Boucadair
    organization: Orange
    email: "mohamed.boucadair@orange.com"
 -
    fullname: Daniele Ceccarelli
    organization: Cisco
    email: "dceccare@cisco.com"

normative:

informative:


--- abstract

This document describes an approach for exposing Device Models as Network Models (DMaNM). In particular, this document provides guidance for structuring a data model to facilitate the reuse of device models within the customer-facing interface of Software-Defined Networking (SDN) controllers. The objective of this approach is to enhance the reusability of device models in various network scenarios and ease the mapping between network/service models with device models  and ensure lossless mapping between the various layers.

--- middle

# Introduction

Network operators need to efficiently manage network elements (including, embedded service functions) throughout their infrastructure. By implementing network-wide management and configuration practices, operators can achieve centralized control and better visibility over their network elements. Doing so enables them to streamline operations, monitor performance, and promptly respond to network events. Moreover, network-wide management facilitates the enforcement of standardized policies and configurations, thus, ensuring consistent behavior and minimizing the risk of errors or misconfigurations that may result in service disruptions. Additionally, that approach enables operators to implement proactive actions such as performance optimization, efficient load balancing, and security policies across the entire network, fostering a more secure and efficient infrastructure.

The ability to reuse device models may play a crucial role in network management. Multiple teams within an organization, such as network engineering, operations, and planning, can benefit from leveraging and using these device models. These models may serve as a common "language" for understanding and managing network services and elements, ensuring consistency and interoperability across different teams and systems. The utilization of models from various teams is a key requirement for network operators.

The IETF has made remarkable progress in defining device models to manage network element capabilities. These device models, often represented using YANG data modeling language {{!RFC7950}}, provide a structured, standardized approach to manage various network devices and their features. By leveraging YANG models, network operators can effectively manage the network element functionalities. These models not only streamline network management but also promote interoperability between different vendors and platforms, fostering a more efficient and robust networking ecosystem.

Some examples device models which can be to manage specific network functions are listed below:

* "ietf-routing-policy" {{?RFC8349}}: This YANG model defines a generic data model for managing routing policies that can be applied to various routing protocols. The model provides a framework for creating, modifying, and applying routing policies, which allows defining how routes are selected, filtered, and modified. The "ietf-routing-policy" model covers features like policy definition, policy attachment, route filters, and route actions.

* "ietf-bgp-policy" {{?I-D.ietf-idr-bgp-model}}: This YANG model defines a data model for the Border Gateway Protocol (BGP). The "ietf-bgp-policy" model is designed to configure and manage BGP routers and sessions, as well as provide a representation of BGP operational state data. The model covers BGP-specific features such as peer configuration, address families, route filters, and route actions. The model is intended to work alongside the "ietf-routing-policy" model to manage BGP routing policies.

* "ietf-access-list" {{?RFC8519}}: This YANG model provides a data model for configuring and managing network access control lists (ACLs). This model provides a generic structure for representing ACLs, along with the ability to define rules for permitting, denying, or assigning a specific action to matching packets.

* "ietf-nat" {{?RFC8512}} defines a YANG module for the Network Address Translation (NAT) function. Various translation flavors are covered: Network Address Translation from IPv4 to IPv4 (NAT44), Network Address and Protocol Translation from IPv6 Clients to IPv4 Servers (NAT64), customer-side translator (CLAT), Stateless IP/ICMP Translation (SIIT), Explicit Address Mappings (EAM) for SIIT, IPv6-to-IPv6 Network Prefix Translation (NPTv6), and Destination NAT.

Software-Defined Networking (SDN) {{!RFC7149}}{{!RFC7426}} controllers facilitate seamless communication and coordination between high-level management systems and the underlying network infrastructure. This arrangement enables efficient translation of network-wide policies and objectives, defined by the Operations Support Systems (OSS), into granular, device-specific configurations and commands for the network elements. A similar concept applies for orchestration scenarios like in Network Slicing {{!RFC9543}}. SDN controllers are typically placed as intermediate entities between the OSS and the network elements. {{SDNsce}} represents such scenario, where an SDN controller exposes its customer-facing interface to the OSS or orchestration layer.

~~~~
        +-----+  +------+
        | OSS |  | Orch |
        +-----+  +------+
           ^        ^
           |        | Customer-Facing
           v        v    Interface
     +-------------------+
     | SDN Controller(s) |
     +-------------------+
      ^      ^       ^
      |      |       | Network-Facing
      v      v       v    Interface
  +-----+ +-----+ +-----+
  | NE1 | | NE2 | | NE3 |
  +-----+ +-----+ +-----+
~~~~
{: #SDNsce title='An Example of SDN Scenario' artwork-align="center"}

{{SDNsce}} illustrates the hierarchical relationship between the OSS, SDN controllers, and the network elements. In this example, the OSS acts as the central management system responsible for overseeing the entire network. An orchestrator acts with a similar role in scenarios like Network Slicing. An SDN controller, positioned in the middle, acts as an intermediary, facilitating communication, and coordination between the OSS and the network elements. At the bottom, the network elements are directly controlled and configured by an SDN controller. This architecture enables efficient translation of high-level network policies into device-specific configurations, ultimately streamlining network management and decoupling the systems from the network evolution.

Device models were defined to be applicable in the network-facing interface of an SDN controller. As a result, these models do not inherently expose/capture the necessary network concepts to make them directly applicable in the customer-facing interface of the SDN controller.

Within the scope of the IETF, efforts can be focused on two approaches when it comes to creating network models for device models. The first approach involves creating network models specific to each device model, while the second approach entails developing a generic and reusable structure for all models. This document puts forth a proposal for a reusable structure, aligning with the latter approach.

The YANG data model defined in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

# Terminology

## Terminology and Notations

The meanings of the symbols in the YANG tree diagrams are defined in {{!RFC8340}}.

The document uses the following terms from {{!RFC8309}} and {{!RFC8969}}:

Service Model:
: Describes a service and the parameters of the service in a portable way that can be used uniformly and independent of the equipment and operating environment.
: Examples of service models are the L3VPN Service Model (L3SM) {{!RFC8299}} and the L2VPN Service Model (L2SM) {{!RFC8466}}.

Network Model:
: Describes a network-level abstraction (or a subset of aspects of a network infrastructure), including devices and their subsystems, and relevant protocols operating at the link and network layers across multiple devices. This model corresponds to the network configuration model discussed in {{!RFC8309}}. : It can be used by a network operator to allocate resources (e.g., tunnel resource, topology resource) for the service or schedule resources to meet the service requirements defined in a service model.
: Examples of network models are the L3VPN Network Model (L3NM) {{!RFC9182}} or the L2VPN Network Model (L2NM) {{!RFC9291}}.

Device Model:
: Refers to the Network Element YANG data model described in {{!RFC8199}} or the device configuration model discussed in {{!RFC8309}}.
: Device models are also used to refer to model a function embedded in a device (e.g., Access Control Lists (ACLs) {{!RFC8519}}).

## Requirements Language

{::boilerplate bcp14-tagged}

## Prefix in Data Node Names

Names of data nodes and other data model objects are prefixed using the standard prefix associated with the corresponding YANG imported modules, as shown in {{tab-prefixes}}.

| Prefix  | Yang Module            | Reference    |
| ------  | ---------------------- | ------------ |
| grp     | ietf-grp-ntw-elements  | RFCXXX       |
{: #tab-prefixes title="Prefixes and corresponding YANG modules"}

> RFC Editor Note:
> : Please replace XXXX with the RFC number assigned to this document. Please remove this note in that case.

# Sample Use Cases

##  "Device Config"-as-a-Service

"Device Config"-as-a-Service involves the use of device models by an OSS to configure network elements through the SDN controller. In this scenario, the SDN controller acts as an intermediary between the OSS and the network elements, providing a configuration service for each network element.

By leveraging device models, the OSS gains a common representation of the network elements' capabilities and configuration parameters. These device models define the desired configuration for specific functions, such as ACLs or routing policies. The OSS utilizes these device models to define the desired configuration for each network element.

Through an SDN controller, the OSS can send the configuration instructions based on the device models to the respective network elements. The SDN controller translates and applies the configuration, ensuring consistency and correctness across the network. Moreover, the mediation of an SDN controller facilitates the access to the network elements from several users, applications or OSS minimizing the impact on the device.

## Profiles

By leveraging device models, network operators can design profile that represent configurations for specific network functionalities, protocols, or services. These templates serve as reusable building blocks, encapsulating best practices, and ensuring consistency in network configuration and management. Once a profile is created, it can be applied to one or multiple network elements, either individually or in groups, depending on the specific network requirements.

This approach not only simplifies the configuration process but also reduces the likelihood of errors and misconfigurations, ultimately improving network stability and performance. Moreover, this process facilitates the lifecycle of the configurations enabling updating the profiles and, later, the network elements in a consistent manner across multiple network elements as a network-wide operation.

# Guidelines to Use Device Models in the Customer-facing Interface of SDN Controllers

This section outlines two key concepts for the guidelines on utilizing device models in the Customer-facing Interface of SDN controllers: (1) groups of network elements and (2) a YANG structure for extending device models to enable network-wide utilization.

## Groups of Network Elements

The management of groups of network elements is a requirement to cover the previous use cases. It is intended to have a YANG model to tag a group of network elements under the same identifier, so the operator can apply a given configuration to a set of devices. This document defines a module called "ietf-grp-ntw-elements", which provides a structured approach to represent and manage groups of network elements, enabling efficient network management.

The "ietf-grp-ntw-elements" module defines a YANG model for representing a group of network elements. Within the module, there is a list called "grp-ntw-element" that includes the groups of network elements. Each group is uniquely identified by the "grp-ne-id" leaf, which has a string data type and represents the group's identifier. The "grp-ntw-element" list has a nested list called "ntw-elements" to specify the individual network elements within each group. The "ntw-element" list has a key of "ne-id" to uniquely identify each network element. The "ne-id" leaf represents the identifier of each network element and has a string data type.

{{ietf-grp-ntw-elements-tree-st}} represents the tree of the proposed YANG model.

~~~~
module: ietf-grp-ntw-elements
  +--rw grp-ntw-elements* [grp-ne-id]
     +--rw grp-ne-id       string
     +--rw ntw-element* [ne-id]
        +--rw ne-id    string
~~~~
{: #ietf-grp-ntw-elements-tree-st title="Group of Network Elements" artwork-align="center"}

## YANG Structure for Extending the Models

The document proposes a structure to enable the reutilization of the device models in network scenarios. The objective is to create a YANG model with the device model and providing a nested structure to store deployment information for network elements associated with each instance in the list. The guideline is to follow the next structure:

* An import of the device model.

* A container named deployment that includes:

+ A list called "ntw-element" with the following elements:

+ A leaf named "ne-id" of type string that serves as the network element identifier (which is the key).

+ A leaf named "devmod-alias" of type string that serves as the device module alias for the deployment.

+ A list called "grp-ntw-elements" with the following elements:

   - A leaf named "grp-ne-id" of type string that serves as the network element identifier (which is the key).

   - A leaf named "devmod-alias" of type string that serves as the device module alias for the deployment.

Let us assume that there is a device model called "foo.yang". The tree of the "foo.yang" model is shown in {{foo-tree-st}}.

~~~~
module: foo
  +--rw foo?   empty
~~~~
{: #foo-tree-st title="Foo Tree Structure" artwork-align="center"}

This document proposes the creation of a module that consists of a list of instances from the "foo.yang" model. To iterate through the list, a key named "devmod-name" must be defined, which will be a string. Additionally, the new model will import "foo.yang". Lastly, a container called "deployment" will encompass a list of network elements, with each element identified by the "ne-id" and having a "devmod-alias" as an alias for the "devmod-name" configuration in the network element.

An example of the proposed structure is shown in {{foo-ntwdev-tree-st}}.

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
{: #foo-ntwdev-tree-st title="Usage Example for 'foo' Module" artwork-align="center"}

# DMaNM YANG Model

## Groups of Network Elements

~~~~~~~~~~
<CODE BEGINS> file "ietf-grp-ntw-elements@2024-07-05.yang"

module ietf-grp-ntw-elements {
  yang-version 1.1;
  namespace "urn:ietf:params:xml:ns:yang:ietf-grp-ntw-elements";
  prefix "grp";

  organization
    "IETF NETMOD Working Group";
  contact
    "WG Web:  <https://datatracker.ietf.org/wg/netmod/>
    WG List:  <mailto:netmod@ietf.org>

    Editor:   Oscar Gonzalez de Dios
              <mailto:oscar.gonzalezdedios@telefonica.com>
    Editor:   Victor Lopez
              <mailto:victor.lopez@nokia.com>
    Editor:   Mohamed Boucadair
              <mailto:mohamed.boucadair@orange.com>
    Editor:   Daniele Ceccarelli
              <mailto:dceccare@cisco.com>";

  description "YANG model for group of network elements.";

  revision "2023-10-23" {
    description "Initial revision.";
    reference
      "RFC XXXX: An Approach to Expose 'Device Models'
      -as-'Network Models'";
  }

  revision "2024-07-05" {
    description "Updated naming for ietf-grp-ntw-elements.";
    reference
      "RFC XXXX: An Approach to Expose 'Device Models'
      -as-'Network Models'";
  }

  list grp-ntw-elements {
    key "grp-ne-id";
    description
      "List of groups of network elements.";

    leaf grp-ne-id {
      type string;
      description
         "Group of network element identifier.";
    }

    list ntw-element {
      key "ne-id";
      description
         "List of network elements.";

      leaf ne-id {
        type string;
        description
         "Network element identifier.";
      }
    }
  }
}
<CODE ENDS>
~~~~~~~~~~

## Usage Example: Applying the Guidelines to The 'foo' Module"

~~~~~~~~~~
module foo-ntwdev {
  namespace "urn:example:foo-ntwdev";
  prefix "netdevfoo";

  import foo {
    prefix "foo";
  }

  organization "Example Organization";
  contact "example@example.com";
  description "YANG model for foo-dev.";
  revision "2023-10-23" {
    description "Initial revision.";
    reference "RFC XXXX: YANG Model for foo-dev";
  }

  revision "2024-07-05" {
    description "Updated list of grp-ntw-elements.";
    reference "RFC XXXX: YANG Model for foo-dev";
  }

  leaf foo {
    type leafref {
      path "/foo:foo";
    }
    description "Reference to foo leaf from foo.yang";
  }

  container deployment {
    description "Deployment container.";

    list ntw-element {
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
~~~~~~~~~~


# Security Considerations

The YANG module specified in this document defines schema for data that is designed to be accessed via network management protocols such as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}. The lowest NETCONF layer is the secure transport layer, and the mandatory-to-implement secure transport is Secure Shell (SSH) {{!RFC6242}}. The lowest RESTCONF layer is HTTPS, and the mandatory-to-implement secure transport is TLS {{!RFC8446}}.

The Network Configuration Access Control Model (NACM) {{!RFC8341}} provides the means to restrict access for particular NETCONF or RESTCONF users to a preconfigured subset of all available NETCONF or RESTCONF protocol operations and content.

There are a number of data nodes defined in this YANG module that are writable/creatable/deletable (i.e., config true, which is the default). These data nodes may be considered sensitive or vulnerable in some network environments. Write operations (e.g., edit-config) and delete operations to these data nodes without proper protection or authentication can have a negative effect on network operations. These are the subtrees and data nodes and their sensitivity/ vulnerability in the "xxx" module:

 * TBC
 * TBC

Some of the readable data nodes in this YANG module may be considered sensitive or vulnerable in some network environments. It is thus important to control read access (e.g., via get, get-config, or notification) to these data nodes. These are the subtrees and data nodes and their sensitivity/vulnerability in the "ixxx" module:

 * TBC
 * TBC

# IANA Considerations

IANA is requested to register the following URI in the "ns" subregistry within the "IETF XML Registry"  {{!RFC3688}}:

   URI:  urn:ietf:params:xml:ns:yang:grp-ntw-elements
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.

IANA is requested to register the following YANG module in the "YANG Module Names" registry  {{!RFC6020}} within the "YANG Parameters" registry group.

   Name:  xxx
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:grp-ntw-elements
   Prefix:  grp
   Reference:  RFC XXXX



# Implementation Status

This section will be used to track the status of the implementations of the model. It is aimed at being removed if the document becomes RFC.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
