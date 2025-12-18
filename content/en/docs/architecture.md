---
title: "System Architecture"
linkTitle: "Architecture"
weight: 2
description: "Overview of the NDTwin three-layer architecture and core components."
---

## HIddwwdwqdq

## Overview

The NDTwin architecture comprises three distinct layers, designed to bridge the gap between physical networks and software applications.

<div class="alert alert-light border">
    <img src="/images/architecture.png" class="img-fluid d-block mx-auto" alt="NDT Architecture Diagram">
    <p class="text-center text-muted mt-2"><small>Figure 1: The NDTwin System Architecture</small></p>
</div>

---

## 1. Application Layer (Top)
The top layer consists of applications developed on the NDT platform (e.g., Energy-saving Apps). 
- **Interaction:** These applications communicate with the NDT Core via **RESTful APIs**.
- **Role:** They utilize the data and control capabilities provided by NDT to perform high-level network management tasks.

---

## 2. NDT Core Layer (Middle)
The middle layer is the heart of the system. It contains several core functions responsible for monitoring, management, and simulation relay.

### Core Functions

* **Flow Information & Bandwidth Collector** Collects real-time traffic data using **sFlow sampling**.
    * **Flow Data:** Sending rates, 5-tuple information (Src/Dst IP, Src/Dst Port, Protocol), and flow paths.
    * **Link Data:** Link utilization rates via sFlow counter samples.

* **Topology & Flow Monitor** Leverages the **Ryu Controller** for real-time topology detection.
    * **Event Handling:** Triggers events immediately upon link status changes (Link Recovery/Failure) or switch updates (Join/Leave).
    * **Mechanism:** The Ryu controller informs NDT via custom event handlers, ensuring the digital twin always reflects the current physical topology.

* **Flow Routing Manager** Utilizes SDN capabilities to manage routing entries on OpenFlow switches.
    * **Capability:** Allows for dynamic installation, modification, and deletion of routing rules to support traffic rerouting.

* **Application Registration & Coordination** Manages the lifecycle of applications running on NDT.
    * **Conflict Resolution:** Coordinates actions between different applications to prevent conflicting network policies.

* **Simulation Request & Reply Manager** Acts as a relay between applications and the backend simulation server.

* **Device Configuration & Power Manager** Manages the physical state of network devices.
    * **Configuration:** Retrieves device info via **SNMP**.
    * **Power Control:** Controls switch power status (On/Off) using APIs from intelligent smart plugs.

* **Controller & Event Handler** The main entry point for external components.
    * **Function:** Handles requests from applications, the SDN controller, and third-party tools.

---

## 3. Infrastructure Layer (Bottom)
The bottom layer represents the physical or emulated network target.
- **Control Plane:** Managed by an **SDN Controller** (Software Defined Network) using the **OpenFlow** protocol.
- **Data Plane:** Consists of OpenFlow-enabled switches and links that NDT aims to model.

---

## Extensibility
**Third-party tools** and loosely coupled functions interact with the NDT architecture primarily through **RESTful APIs**, allowing for flexible integration and expansion of the platform's capabilities.