---
title: Application Examples
description: Overview of the deployed network applications including Energy Saving and Traffic Engineering modules.
date: 2017-01-05
weight: 5
---

## Energy-Saving-App
![Status](https://img.shields.io/badge/status-active-success) ![Version](https://img.shields.io/badge/version-1.0.0-blue) ![License](https://img.shields.io/badge/license-MIT-green)

The **Energy-Saving-App** focuses on Green Networking strategies. It dynamically monitors link utilization and topology status to power down unnecessary ports or links during off-peak hours, significantly reducing operational expenditure (OPEX).

### Key Features
* **Dynamic Sleep Mode**: Automatically toggles interface power states based on real-time traffic thresholds.
* **Topology Awareness**: Calculates the Minimum Spanning Tree (MST) to ensure connectivity is maintained while redundant links are disabled.

### Tech Stack
| Component | Specification |
| :--- | :--- |
| **Language** | Python |

[View Source Code →](#)

---

## Traffic-Engineering-App
![Status](https://img.shields.io/badge/status-active-success) ![Version](https://img.shields.io/badge/version-1.0.0-blue) ![License](https://img.shields.io/badge/license-MIT-green)

The **Traffic-Engineering-App** is designed to solve network congestion and optimize bandwidth utilization. By leveraging global network views, it intelligently reroutes traffic flows to balance loads across multiple paths.

### Key Features
* **Load Balancing**: Implements ECMP (Equal-Cost Multi-Path) routing to distribute high-throughput flows.
* **Congestion Avoidance**: Proactively detects bottleneck links and recalculates paths before packet loss occurs.
* **QoS Guarantees**: Prioritizes critical traffic (e.g., VoIP, Video) over best-effort traffic during high load.

### Tech Stack
| Component | Specification |
| :--- | :--- |
| **Language** | C++ |
[View Source Code →](#)

---

> **Note**: For installation instructions and API documentation, please refer to the specific `README.md` in the respective repositories.