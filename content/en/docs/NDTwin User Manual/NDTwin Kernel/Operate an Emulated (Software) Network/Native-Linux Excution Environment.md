---
title: Install All Required Components on a Linux Server
description: > 
  A comprehensive guide for manually installing the NDT system on a native Linux environment.
  This section covers system dependencies, building the NDT Core with Ninja, configuring Python environments, and running the full system.
date: 2025-12-24
weight: 2
---



**Pre-flight Checks:**

1. **Source Code:** Ensure `NetworkDigitalTwin-main` is in your `~/Desktop` (or adjust paths below).
2. **Compilation:** Ensure `ndt_main` exists in `build/bin/` (from Section 4).
3. **Topology Script:** Ensure `testbed_topo.py` exists (from Section 5).

**Startup Order is Critical:** Please execute Terminals 1 through 3 in the **exact order** listed below.

### Terminal 1: Ryu Controller

* **Purpose:** Starts the SDN Logic.
* **Environment:** `ryu-env` (Python 3.8).

```bash
cd ~/Desktop/NetworkDigitalTwin-main
conda activate ryu-env
# 'intelligent_router.py' is our custom controller app
ryu-manager intelligent_router.py ryu.app.rest_topology ryu.app.ofctl_rest --ofp-tcp-listen-port 6633 --observe-link

```

### Terminal 2: Mininet Topology

* **Purpose:** Creates the virtual network and configures sFlow.
* **Environment:** System Native (Root).

```bash
cd ~/Desktop/NetworkDigitalTwin-main
# Clean up old network artifacts (Crucial step)
sudo mn -c
# Start the custom topology script
sudo python3 testbed_topo.py

```

### Terminal 3: NDT Core

* **Purpose:** Starts the Digital Twin Engine.
* **Environment:** System Native (Root).

```bash
cd ~/Desktop/NetworkDigitalTwin-main/build

# Set API Key
# If you do not have a valid OpenAI API key, you can input any random string 
# (e.g., "12345") to bypass the check. The system will run, but LLM features will be disabled.
export OPENAI_API_KEY="any-random-string-here"

# Execute with sudo, preserving the environment variable (-E)
sudo -E bin/ndt_main --loglevel info

```

---

## 7. Generating Traffic (Validation)

To test if the system is working, you can generate traffic inside the **Mininet CLI** (Terminal 2).

1. **Start an iperf3 server on Host 1:**
```bash
mininet> h1 iperf3 -s &

```


2. **Run a client on Host 2:**
```bash
mininet> h2 iperf3 -c h1

```


3. **Observe:**
* **Terminal 1 (Ryu):** Should show link discovery logs.
* **Terminal 3 (NDT):** Logs should reflect flow detection and bandwidth usage.



---

## 8. Safe Shutdown Procedure

When finishing your experiment, please close the system in **reverse order** and perform cleanup to avoid errors in future runs:

1. In **Terminal 2 (Mininet)**, type `exit` to quit the CLI.
2. The script will automatically remove the IP alias.
3. Run a final cleanup command in Terminal 2:
```bash
sudo mn -c

```


4. Close all other terminal windows.

---

