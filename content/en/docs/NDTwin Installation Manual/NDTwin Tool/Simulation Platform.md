---
title: Simulation Platform Manager
description: >
date: 2017-01-05
weight: 30
---

Here are the **Installation Manual** and **User Manual** specifically for the **Simulation Platform**.

---

# Installation Manual: Simulation Subsystem

**Scope:** simulation platform, Power Simulator (Algorithm), and NFS Configuration.

## 1. System Requirements

* **Operating System:** Linux Ubuntu 22.04.X ~ 24.04.X
* **Language Standard:** C++23
* **Compiler:** g++

## 2. Dependencies

The Simulation subsystem requires specific libraries for networking, logging, and cryptographic hashing.

### 2.1 Install Packages

Execute the following commands:

```bash
# Core Utilities and JSON
sudo apt install nlohmann-json3-dev
sudo apt install libboost-all-dev
sudo apt install libboost-process-dev

# Logging
sudo apt install libspdlog-dev
sudo apt install libfmt-dev

# Cryptography (Required for Flow Rule Hashing in Simulator)
sudo apt install libssl-dev

```


## 3. NFS Configuration

The simulation platform must share a file system with the NDT (Network Digital Twin).

### 3.1 Server-Side Setup (On NDT Machine)

1. **Create Directory:**
```bash
sudo mkdir -p /srv/nfs/sim
sudo chown nobody:nogroup /srv/nfs/sim
sudo chmod 777 /srv/nfs/sim

```


2. **Configure Exports:**
Edit `/etc/exports` to include the simulation platform's IP:
```txt
/srv/nfs/sim <Sim_Server_IP>(rw,sync,no_subtree_check,all_squash)

```


3. **Restart NFS:**
```bash
sudo systemctl restart nfs-kernel-server

```



### 3.2 Client-Side Setup (On simulation platform Machine)

1. **Install Client:**
```bash
sudo apt install nfs-common

```


2. **Create Mount Point:**
```bash
sudo mkdir -p /mnt/nfs/sim

```


*Note: If you change this path, you must update the source code configuration to match.*

## 4. Compilation & Deployment

### 4.1 Build Flags

* **Logging:** Compile with `-DSPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_TRACE` to ensure debug logs function correctly when the `-l debug` flag is used.

### 4.2 Simulator Registration (Crucial Step)

The simulation platform does not compile the simulator logic directly into itself; it runs it as an external binary. You must "register" the Power Simulator by placing the compiled binary in a specific directory.

1. **Compile** the Power Simulator code.
2. **Deploy** the binary to the following path structure inside the simulation platform directory:
**Path:** `NDT-Simulation-Server/registered/power_sim/1.0/`
**Filename:** `executable`
**Command Example:**
```bash
# Assuming you are in the source root
cp ./build/power_sim ./registered/power_sim/1.0/executable

```



### 4.3 NDT Integration Check

For the simulation platform to receive tasks, the NDT must know its address.

* **Action:** Update `main.cpp` in the **NDT** source code:
```cpp
std::string SIM_SERVER_URL = "http://<YOUR_SIM_IP>:8003/submit";

```