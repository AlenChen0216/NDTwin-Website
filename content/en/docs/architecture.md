---
title: System Architecture
linkTitle: System Architecture
weight: 2
description: >
  Explain the three-layer architecture of NDTwin and the functional modules in its kernel.
---

{{% pageinfo %}}
The NDTwin architecture comprises three layers. From the top to the bottom, they are application, kernel, and network, respectively.
{{% /pageinfo %}}

<div class="text-center">
  <img src="/images/NdtArcht.png" class="img-fluid" alt="NDTwin Architecture Diagram" style="max-width: 90%; margin: 2rem 0;">
</div>

## Application Layer 

The application layer consists of various applications developed on the NDTwin platform. Currently, NDTwin provides the **Traffic-engineering App** and **Energy-saving App**. More applications will be added by the NDTwin development team in the future. Additionally, any one can develop his/her own applications from the NDTwin open source project.

Each NDTwin application is a separate program running independently, and it communicates with the NDTwin kernel via **RESTful APIs** to get services and receive notifications from the kernel. This design provides many advantages as follows:

* **Easy Integration**: NDTwin applications can be independently developed and written in different programming languages.  
* **High Performance**: Multiple NDTwin applications can execute in parallel over multiple CPU cores or on different machines to fully utilize the available processing power. 
* **Fault Isolation**: A faulty or buggy NDTwin application will not fail the operations of the NDTwin kernel or other NDTwin applications.

 

## Kernel Layer 

The middle layer is the heart of the system, implemented in high-performance C++. It acts as a bridge, translating high-level intents into low-level network operations.

### Core Functions

#### Flow Information & Bandwidth Collector
This component acts as a high-speed telemetry engine using **sFlow technology**. It runs a non-blocking UDP listener to process two types of samples:
* **Flow Data**: Extracts 5-tuple information (Src/Dst IP, Port, Protocol) to identify traffic paths and detect "Elephant Flows."
* **Link Data**: Uses sFlow counter samples to calculate real-time link bandwidth utilization and "leftover" capacity.

#### Topology & Flow Monitor
This module maintains an in-memory graph representation of the network using the **Boost Graph Library**.
* **Synchronization**: It polls the **Ryu Controller** to detect topology changes (Link Recovery/Failure or Switch Join/Leave).
* **Pathfinding**: It provides algorithms (like DFS/BFS) to calculate all valid paths between hosts, essential for redundancy analysis.

#### Flow Routing Manager
Utilizes SDN capabilities to manage routing entries on OpenFlow switches. It allows for dynamic installation, modification, and deletion of routing rules to support traffic rerouting scenarios based on the decisions made by the Monitor or upper-layer Apps.

#### Device Configuration & Power Manager
Manages the physical state of network devices beyond standard OpenFlow capabilities.
* **Configuration**: Retrieves device info via **SNMP**.
* **Power Control**: Controls switch power status (On/Off) using APIs from intelligent smart plugs, enabling energy-saving experiments.

#### Application Registration & Coordination
Manages the lifecycle of applications running on NDT. It includes a conflict resolution mechanism to coordinate actions between different applications, preventing conflicting network policies (e.g., one app trying to power off a switch while another routes traffic through it).

## Network Layer 

The network layer represents the target network that is operated and managed by NDTwin:

* **Control Plane**: NDTwin uses an SDN Controller (e.g., Ryu) to control the swithes in real time. To be controlled by NDTwin, the switches need to support the OpenFlow protocol. 
* **Data Plane**: NDTwin can correctly operate the following two types of networks:
    * **Emulated Network**: which is constructed by Mininet with Open vSwitch (OVS). 
    * **Physical Network**: which is composed of hardware switches that support OpenFlow (e.g., Brocade, HPE).

