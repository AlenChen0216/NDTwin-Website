---
title: Simulation Platform
description: >
    This workflow describes a scalable, parallel simulation system designed to optimize network decisions through a coordinated process. It relies on a shared file system for data exchange and a distributed architecture for processing.
date: 2017-01-05
weight: 30
---


## 1. Execution Guide

### 1.1 Startup Sequence

The Simulation Server relies on the network topology being ready. Follow this strict order:

1. Ryu Controller
2. Mininet
3. NDT (Network Digital Twin)
4. **Simulation Server** (Start this now)

### 1.2 Running the Server

Open a terminal in the Simulation Server directory:

```bash
cd ~/NDT-Simulation-Server
sudo ./server

```

* **Why sudo?** The server requires root privileges to mount the NFS directory `/mnt/nfs/sim` during operation.
* **Confirmation:** Wait for the "Server started" message.

## 2. Developer Guide

### 2.1 Code Structure Overview

| Path | Description |
| --- | --- |
| `include/sim/max_min_fairness.hpp` | **Algorithm Core.** Defines the bandwidth allocation logic. |
| `src/sim/max_min_fairness.cpp` | Implementation of Max-Min Fairness. |
| `include/common/SFlowType.hpp` | **Data Structures.** Contains Flow definitions. |
| `registered/power_sim/` | **Binaries.** Where the server looks for executable simulators. |

### 2.2 The Max-Min Fairness Algorithm

* **Location:** `src/sim/max_min_fairness.cpp`
* **Function:** When the simulation proposes new routing rules, this algorithm calculates how much bandwidth each flow will receive.
* **Logic:** Currently, the algorithm assumes all flows are **greedy** (they will take as much bandwidth as possible).
* **Customization:** The code is structured to allow for specific bandwidth demands (QoS) in the future. If you need to simulate flows with fixed bandwidth limits, modify the parameters here.

### 2.3 Synchronization with NDT

The Simulation Subsystem shares many data structures with the NDT project (specifically in `GraphTypes.hpp` and `SFlowType.hpp`).

* **Warning:** If the NDT source code updates its graph or flow definitions, you **must** copy those updates to this project to prevent data parsing errors (JSON mismatch).
* **Exception:** Do not overwrite the custom JSON conversion functions in `SFlowType.hpp` (`from_json` for `FlowChange`/`FlowDiff`) as these are specific to the Power Simulator.
