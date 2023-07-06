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
 - Device models
 - NBI
 
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
    email: oscar.gonzalezdedios@telefonica.com

  -
    fullname: Victor Lopez
    org: Nokia
    email: victor.lopez@nokia.com

normative:

informative:


--- abstract

This document defines guidelines for a data model structure to reuse device models in the North-Bound Interface of the SDN controllers. The aim is to facilitate the reutilization of the device models in network scenarios.


--- middle

# Introduction

Network operators require an effective management and configuration of network elements across their infrastructure. By implementing network-wide management and configuration practices, operators gain centralized control and visibility over their network elements, enabling them to streamline operations, monitor performance, and quickly respond to network events. Moreover, network-wide management allows for the enforcement of standardized policies and configurations, ensuring consistent behavior and reducing the risk of errors or misconfigurations that can lead to service disruptions. Additionally, it enables operators to implement proactive measures, such as performance optimization, load balancing, and security policies, across the entire network, fostering a more secure and efficient infrastructure.

The availability of reusing device models play a crucial role in network management. Multiple organizations within the operator, such as network engineering, operations, and planning teams, can benefit from accessing these device models. These models serve as a common language for understanding and configuring network elements, ensuring consistency and interoperability across different teams and systems. Using the models from different teams is a key requirement for network operators.

The Internet Engineering Task Force (IETF) has made significant strides in defining device models to manage network element capabilities. These device models, often represented using YANG data modeling language, provide a structured, standardized approach to configure, monitor, and manage various network devices and their features. By leveraging YANG models, network operators can effectively manage the network element functionalities. These models not only simplify network management but also promote interoperability between different vendors and platforms, fostering a more efficient and robust networking ecosystem.


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
