---
title: Simulation Platform Manager
description: >
    This workflow describes a scalable, parallel simulation system designed to optimize network decisions through a coordinated process. It relies on a shared file system for data exchange and a distributed architecture for processing.
date: 2017-01-05
weight: 30
---


## 1. Execution Guide

### 1.1 Startup Sequence

The Simulation Server relies on the network topology being ready. Follow this strict order:

1. Ryu Controller
2. Mininet (if you are using emulated network)
3. NDTwin Kernel
4. **Simulation Platform Manager** 

### 1.2 Running the Server

Open a terminal in the Simulation Platform Manager directory:

```bash
cd ~/Simulation-Platform-Manager
sudo ./simulation_platform_manager

```

* **Why sudo?** The server requires root privileges to mount the NFS directory `/mnt/nfs/sim` during operation.
* **Confirmation:** Wait for the "Server started" message.


See more design explanation at [Simulation Platform Manager](../../NDTwin%20Developer%20Manual/NDTwin%20Application/Simulation%20Platform/index.md)
