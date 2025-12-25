---
title: System Architecture
linkTitle: System Architecture
weight: 2
description: >
  Explain the three-layer architecture of NDTwin: from the Applications, through the Kernel, down to the network infrastructure.
---

{{% pageinfo %}}
The NDTwin architecture comprises three distinct layers designed to bridge the gap between physical networks and software applications. It decouples high-level network management logic from the low-level complexities of OpenFlow and telemetry.
{{% /pageinfo %}}

<div class="text-center">
  <img src="/images/NdtArcht.png" class="img-fluid" alt="NDTwin Architecture Diagram" style="max-width: 90%; margin: 2rem 0;">
</div>

## 1. Application Layer (Top)

The top layer consists of specialized applications developed on the NDT platform, such as **Traffic Engineering Apps** or **Energy-saving Apps**.

* **Role**: These applications utilizes the data and control capabilities provided by NDT to perform high-level network management tasks without needing to interact directly with OpenFlow switches.
* **Interaction**: Applications communicate with the NDT Core via **RESTful APIs**, allowing for loose coupling and easy integration of third-party tools (e.g., LLM agents).

## 2. NDT Core Layer (Middle)

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

## 3. Infrastructure Layer (Bottom)

The bottom layer represents the target network environment. NDTwin supports a hybrid deployment model:

* **Control Plane**: Managed by an **SDN Controller** (Ryu) using the standard OpenFlow 1.3 protocol.
* **Data Plane**:
    * **Emulated Network**: Uses **Mininet** with Open vSwitch (OVS). NDTwin includes specific logic to map Mininet interface indices to OpenFlow ports.
    * **Physical Network**: Supports hardware OpenFlow switches (e.g., Brocade, HPE) via a physical testbed mode.

## Extensibility

NDTwin is designed for integration. **Third-party tools** (such as external Simulation Servers, Data Analyzers, or Chatbots) interact with the architecture primarily through the RESTful API layer. This design allows researchers to extend NDTwin's capabilities without modifying the C++ kernel source code.