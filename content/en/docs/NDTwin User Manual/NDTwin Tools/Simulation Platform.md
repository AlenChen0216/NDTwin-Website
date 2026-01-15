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

