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

This document defines guidelines for a data model structure to reuse device models in the North-Bound Interface of the SDN controllers. The aim is to facilitate the reutilization of the device models in network scenarios.

--- middle

# Introduction

Network operators require an effective management and configuration of network elements across their infrastructure. By implementing network-wide management and configuration practices, operators gain centralized control and visibility over their network elements, enabling them to streamline operations, monitor performance, and quickly respond to network events. Moreover, network-wide management allows for the enforcement of standardized policies and configurations, ensuring consistent behavior and reducing the risk of errors or misconfigurations that can lead to service disruptions. Additionally, it enables operators to implement proactive measures, such as performance optimization, load balancing, and security policies, across the entire network, fostering a more secure and efficient infrastructure.

The availability of reusing device models play a crucial role in network management. Multiple organizations within the operator, such as network engineering, operations, and planning teams, can benefit from accessing these device models. These models serve as a common language for understanding and configuring network elements, ensuring consistency and interoperability across different teams and systems. Using the models from different teams is a key requirement for network operators.

The Internet Engineering Task Force (IETF) has made significant strides in defining device models to manage network element capabilities. These device models, often represented using YANG data modeling language, provide a structured, standardized approach to configure, monitor, and manage various network devices and their features. By leveraging YANG models, network operators can effectively manage the network element functionalities. These models not only simplify network management but also promote interoperability between different vendors and platforms, fostering a more efficient and robust networking ecosystem.

Some examples of these network models are:

* ietf-routing-policy {{!RFC8349}}: This YANG model defines a generic data model for managing routing policies that can be applied to various routing protocols. The model provides a framework for creating, modifying, and applying routing policies, which allows defining how routes are selected, filtered, and modified. The ietf-routing-policy model covers features like policy definition, policy attachment, route filters, and route actions.

* ietf-bgp-policy {{!draft-ietf-idr-bgp-model}}: This YANG model defines a data model for the Border Gateway Protocol (BGP). The ietf-bgp-policy model is designed to configure and manage BGP routers and sessions, as well as provide a representation of BGP operational state data. The model covers BGP-specific features such as peer configuration, address families, route filters, and route actions. The model is intended to work alongside the ietf-routing-policy model to manage BGP routing policies.

* ietf-access-list {{!RFC 8519}}: This YANG model provides a data model for configuring and managing network access control lists (ACLs). This model provides a generic structure for representing ACLs, along with the ability to define rules for permitting, denying, or assigning a specific action to matching packets. 

Software-Defined Networking (SDN) controllers facilitate seamless communication and coordination between high-level management systems and the underlying network infrastructure. This arrangement enables efficient translation of network-wide policies and objectives, defined by the Operations Support Systems (OSS), into granular, device-specific configurations and commands for the network elements. A similar concept applies for orchestration scenarios like in network slicing. Consequently, SDN controllers are typically placed as intermidiate entities between the OSS and the network elements. {{#SDNsce}} represents this scenario.

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

{{SDNsce}} illustrates the hierarchical relationship between the OSS, SDN controller and the network elements. Typically, the OSS serves as the central management system responsible for overseeing the entire network. Similarly, an orchestrator acts with a similar role in scenarios like network slicing. The SDN controller, positioned in the middle, acts as an intermediary, facilitating communication and coordination between the OSS and the network elements. The network elements at the bottom are directly controlled and configured by the SDN controller. This archicture enables efficient translation of high-level network policies into device-specific configurations, ultimately streamlining network management and decoupling the systems from the network evolution.

Device models like ietf-routing-policy {{!RFC8349}}, ietf-bgp-policy {{I-D.ietf-idr-bgp-model}} or ietf-access-list {{!RFC8519}} were defined to be applicable in the South-Bound Interface (SBI)of the SDN controller. Therefore, they do not have network concepts to make them applicable in the North-Bound Interface (NBI) of the SDN controller.

The work in IETF can be directed to create network models for each device model or create a generic reusable structure for each model. This document proposes a reusable structure following the second approach.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
