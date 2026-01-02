---
title: Operate a Physical (Hardware) Network
description: >
  A comprehensive guide to preparing the physical network environment.
  This section covers the necessary configurations for physical switches
  (enabling OpenFlow and sFlow) and details the system and Python dependencies required to deploy the SDN Controller.
date: 2025-12-24
weight: 3
---

Here is the complete **Installation & Setup Handbook** in English. You can save this as `README.md` or `INSTALL.md` in your project root to help other developers set up the environment correctly.

---

# NDTwin / SDN Monitor Project - Setup Guide

This guide details the installation of system dependencies, C++ libraries, and the specific Python environment required to build and run the SDN Monitoring modules (`FlowLinkUsageCollector`, `TopologyAndFlowMonitor`).

## ⚠️ Critical Requirement: Python Version

**You must use Python 3.8.**

This project relies on the **Ryu SDN Controller**, which depends on the `eventlet` library.

* **Do NOT use Python 3.10 or newer.**
* Newer Python versions (3.10+) removed `collections.Iterable`, causing Ryu to crash immediately with `AttributeError`.

---

## 1. System Requirements

* **OS:** Linux (Ubuntu 20.04 LTS or 22.04 LTS recommended).
* **Compiler:** GCC or Clang with **C++17** support (required for `std::filesystem`, `std::shared_mutex`).
* **Privileges:** `sudo` access is required to install packages and interact with Open vSwitch (OVS).

---

## 2. Install C++ Build Dependencies

Install the necessary compilers and development libraries used in the C++ source code (Boost Graph Library, SPDLog, JSON, OpenSSL).

```bash
sudo apt update
sudo apt install -y build-essential cmake

# 1. Boost C++ Libraries (Used for Graph structures)
sudo apt install -y libboost-all-dev

# 2. SPDLog (High-performance logging)
sudo apt install -y libspdlog-dev

# 3. nlohmann/json (JSON parsing for REST API)
sudo apt install -y nlohmann-json3-dev

# 4. OpenSSL (Used for SHA256 hashing)
sudo apt install -y libssl-dev

# 5. Curl (CLI tool used by the C++ code to fetch topology)
sudo apt install -y curl

```

---

## 3. Install Runtime Dependencies (SDN Tools)

The C++ code interacts directly with system-level network tools via shell commands (e.g., `popen("sudo ovs-vsctl ...")`).

```bash
# 1. Open vSwitch (OVS)
# Required for "ovs-vsctl" commands to map interfaces to ports.
sudo apt install -y openvswitch-switch

# Ensure the OVS service is running
sudo service openvswitch-switch start

# 2. Mininet (Optional/Mode Dependent)
# Required if running in utils::MININET mode.
sudo apt install -y mininet

```

---

## 4. Python Environment Setup (Ryu Controller)

As stated above, **Python 3.8 is mandatory**. We recommend using `Conda` or `venv` to isolate the environment.

### Option A: Using Conda (Recommended)

```bash
# 1. Create a Python 3.8 environment
conda create -n sdn_env python=3.8 -y

# 2. Activate the environment
conda activate sdn_env

# 3. Install Python dependencies (Ryu, etc.)
pip install -r requirements.txt

```

### Option B: Using venv (Standard)

```bash
# 1. Install Python 3.8 (if not already on your system)
sudo apt install -y python3.8 python3.8-venv

# 2. Create the virtual environment
python3.8 -m venv venv

# 3. Activate the environment
source venv/bin/activate

# 4. Install Python dependencies
pip install -r requirements.txt

```

---

## 5. Build & Run Instructions

### Building the C++ Project

Assuming a standard CMake project structure:

```bash
mkdir build && cd build
cmake ..
make -j$(nproc)

```



### Troubleshooting

**Issue:** `AttributeError: module 'collections' has no attribute 'Iterable'` when starting Ryu.

* **Cause:** You are running Python 3.10+.
* **Fix:** Switch to the Python 3.8 environment created in Step 4.

**Issue:** `popen() failed` or `Permission denied` in logs.

* **Cause:** The application was run without `sudo`, or the user does not have permission to run `ovs-vsctl`.
* **Fix:** Run the application with `sudo`.