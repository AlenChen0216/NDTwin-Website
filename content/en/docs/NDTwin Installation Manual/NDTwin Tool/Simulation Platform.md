---
title: Simulation Platform Manager
description: >
date: 2017-01-05
weight: 30
---

## Example Project: Energy-Saving App

This installation manual uses the **Energy-Saving App** as a concrete example of how a simulator is registered and executed by the Simulation Platform Manager.

- **Energy-Saving App (example simulator):** https://github.com/ndtwin-lab/Energy-Saving-App  
- **Simulation Platform Manager:** https://github.com/ndtwin-lab/Simulation-Platform-Manager


# Installation Manual: Simulation Subsystem

**Scope:** Simulation Platform, Energy-Saving App Simulator, and NFS Configuration.

## 1. System Requirements

* **Operating System:** Linux Ubuntu 22.04.X ~ 24.04.X
* **Language Standard:** C++23
* **Compiler:** g++

## 2. Dependencies

The Simulation subsystem requires specific libraries for networking, logging, and cryptographic hashing.

### 2.1. Get the Source Code

Clone the example simulator and the Simulation Platform Manager:

```bash
# Choose a workspace
mkdir -p ~/ndtwin-sim && cd ~/ndtwin-sim

# Clone the Simulation Platform Manager
git clone https://github.com/ndtwin-lab/Simulation-Platform-Manager.git

# Clone the example simulator (Energy-Saving App)
git clone https://github.com/ndtwin-lab/Energy-Saving-App.git
```

### 2.2 Install Packages

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

The Simulation Platform must share a file system with the NDTwin.

### 3.1 Server-Side Setup (On NDTwin Machine)

1. **Create Directory:**
```bash
sudo mkdir -p /srv/nfs/sim
sudo chown nobody:nogroup /srv/nfs/sim
sudo chmod 777 /srv/nfs/sim

```


2. **Configure Exports:**
Edit `/etc/exports` to include the Simulation Platform's IP:
```txt
/srv/nfs/sim <Sim_Server_IP>(rw,sync,no_subtree_check,all_squash)

```


3. **Restart NFS:**
```bash
sudo systemctl restart nfs-kernel-server

```



### 3.2 Client-Side Setup (On Simulation Platform Machine)

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

The Simulation Platform does not compile the simulator logic directly into itself; it runs it as an external binary. You must "register" the Energy-Saving Simulator by placing the compiled binary in a specific directory.

1. **Compile** the Energy-Saving Simulator code.
2. **Deploy** the binary to the following path structure inside the Simulation Platform directory:
**Path:** `Simulation-Platform-Manager/registered/energy_saving_simulator/1.0/`
**Filename:** `executable`
**Command Example:**
```bash
# Assuming you are in the source root of Energy-Saving-App
cp ./energy_saving_simulator ../Simulation-platform-manager/registered/energy_saving_simulator/1.0/executable

```

> **Makefile option (recommended):** The Energy-Saving App repository already automates simulator registration in its **Makefile**.  
> Run the `sim` target to build the simulator and copy it into the Simulation Platform Manager’s registered folder:
>
> ```bash
> # From the source root of Energy-Saving-App
> make sim
> ```
>
> This will generate `./energy_saving_simulator` and copy it to:
> `../Simulation-Platform-Manager/registered/energy_saving_simulator/1.0/executable`.




### 4.3 NDTwin Integration Check

For the Simulation Platform to receive tasks, the NDTwin must know its address.

* **Action:** Update `setting/AppConfig` in the **NDTwin-Kernel** source code:
```cpp
std::string SIM_SERVER_URL = "http://<YOUR_SIM_IP>:8003/submit";

```