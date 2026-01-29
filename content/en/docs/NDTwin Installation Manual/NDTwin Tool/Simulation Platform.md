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

> **Deployment Note:** In production, **NDTwin-Kernel**, **Energy-Saving-App**, and **Simulation-Platform-Manager** can run on **different machines** (e.g., Kernel on the NDTwin server, Simulation Platform Manager on a multi-core simulation server, and the App on any client host).  
> **In this demo, we run all three components on the same machine** for simplicity.


## 1. System Requirements

* **Operating System:** Linux Ubuntu 22.04.X ~ 24.04.X
* **Language Standard:** C++23
* **Compiler:** g++

## 2. Dependencies

The Simulation subsystem requires specific libraries for networking, logging, and cryptographic hashing.

### 2.1. Get the Source Code

Clone the example simulator and the Simulation Platform Manager:

```bash
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

# Logging
sudo apt install libspdlog-dev
sudo apt install libfmt-dev

# Cryptography (Required for Flow Rule Hashing in Simulator)
sudo apt install libssl-dev

```


## 3. NFS Configuration

The Simulation Platform must share a file system with the NDTwin.

### 3.1 Server-Side Setup (On NDTwin Machine)


1. **Install NFS Server:**
```bash
sudo apt update
sudo apt install -y nfs-kernel-server
sudo systemctl enable --now nfs-kernel-server
```

2. **Create Directory:**
```bash
sudo mkdir -p /srv/nfs/sim
sudo chown nobody:nogroup /srv/nfs/sim
sudo chmod 777 /srv/nfs/sim

```


3. **Configure Exports:**
Edit `/etc/exports` to include the Simulation Platform's IP:
```txt
/srv/nfs/sim <Sim_Server_IP>(rw,sync,no_subtree_check,all_squash)

```

> **Note:** If the **Simulation Platform Manager** and **NDTwin Kernel** run on the **same machine**, you can set `<Sim_Server_IP>` to `localhost` (or `127.0.0.1`).



4. **Restart NFS:**
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


## 4. Configure Energy-Saving-App and Simulation-Platform-Manager settings (before build)

### 4.1 Configure Energy-Saving-App settings 

Before compiling, edit the example settings file and set your IP/port values:

1. Open: `Energy-Saving-App/include/app/settings.hpp.example`
2. Update fields such as:
   - `app_ip`, `app_port`
   - `ndt_ip`, `ndt_port`
   - `request_manager_ip`, `request_manager_port`
   - `nfs_server_ip`, `nfs_mnt_dir`, etc.
3. Save it as `Energy-Saving-App/include/app/settings.hpp`

> If your Makefile supports auto-generation, you can also run `make all` once to create `include/app/settings.hpp` from the example, then edit the generated file.

### 4.2 Configure Simulation-Platform-Manager settings 

Before compiling, edit the example settings file:

1. Open: `Simulation-Platform-Manager/include/settings/sim_server.hpp.example`
2. Update fields such as:
   - `request_manager_ip`, `request_manager_port`, `request_manager_target`
   - `sim_server_port`, `sim_server_target`
   - `nfs_server_ip`, `nfs_server_dir`, `nfs_mnt_dir`
3. Save it as `Simulation-Platform-Manager/include/settings/sim_server.hpp`

> If your Makefile supports auto-generation, you can run `make all` once to create `include/settings/sim_server.hpp` from the example, then edit it.


> **Same-machine demo tip:** If **NDTwin-Kernel**, **Energy-Saving-App**, and **Simulation-Platform-Manager** run on the **same machine**, you can set all `*_ip` fields (e.g., `app_ip`, `ndt_ip`, `request_manager_ip`, `nfs_server_ip`) to `localhost` (or `127.0.0.1`) and keep the ports as configured.




## 5. Compilation & Deployment

### 5.1 Build Flags

* **Logging:** Compile with `-DSPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_TRACE` to ensure debug logs function correctly when the `-l debug` flag is used.

### 5.2 Simulator Registration and Build Energy-Saving App (Crucial Step)

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
> cd Energy-Saving-App
> make all
> ```
>
> This will generate `./energy_saving_simulator` and copy it to:
> `../Simulation-Platform-Manager/registered/energy_saving_simulator/1.0/executable`.

### 5.3 Build Simulation Platform Manager
```bash
cd Simulation-Platform-Manager
make all
```


### 5.4 NDTwin Integration Check

For the Simulation Platform to receive tasks, the NDTwin must know its address.

* **Action:** Update `setting/AppConfig` in the **NDTwin-Kernel** source code:
```cpp
std::string SIM_SERVER_URL = "http://<YOUR_SIM_IP>:8003/submit";

```

> **Note:** If the **Simulation Platform Manager** and **NDTwin Kernel** run on the **same machine**, you can set `<YOUR_SIM_IP>` to `localhost` (or `127.0.0.1`).

* **Recompile NDTwin-Kernel:** After updating `setting/AppConfig`, rebuild and restart the NDTwin-Kernel.

> See the NDTwin-Kernel build steps in the [Installation Manual](../../NDTwin%20Installation%20Manual/NDTwin%20Kernel/_index.md).



---

## 6. Launch Guide

For step-by-step instructions on how to launch the **NDTwin-Kernel**, **Simulation-Platform-Manager**, and **Energy-Saving-App**, see:

- **User Manual:** [User Manual](../../NDTwin%20User%20Manual/)
- **Demo Video:** [Demo Video](../../Tutorials%20and%20Demo%20Videos/_index.md)
