---
title: Install All Required Components on a Linux Server
description: > 
  A comprehensive guide for manually installing the NDTwin system on a native Linux environment.
  This section covers system dependencies, building the NDTwin Kernel with Ninja, configuring Python environments, and running the full system.
date: 2025-12-24
weight: 2
---



**Pre-flight Checks:**

1. **Source Code:** Ensure `NDTwin-Kernel` is in your `~/Desktop` (or adjust paths below).
2. **Compilation:** Ensure `ndtwin_kernel` exists in `build/bin/` (from [Installation Manual](../../../NDTwin%20Installation%20Manual/NDTwin%20Kernel/Operate%20an%20Emulated%20(Software)%20Network/)).
3. **Topology Script:** Ensure `testbed_topo.py` exists (from [Installation Manual](../../../NDTwin%20Installation%20Manual/NDTwin%20Kernel/Operate%20an%20Emulated%20(Software)%20Network/)).

**Startup Order is Critical:** Please execute Terminals 1 through 3 in the **exact order** listed below.

### Terminal 1: Ryu Controller

* **Purpose:** Starts the SDN Logic.
* **Environment:** `ryu-env` (Python 3.8).

```bash
conda activate ryu-env
# 'intelligent_router.py' is our custom controller app
ryu-manager intelligent_router.py ryu.app.rest_topology ryu.app.ofctl_rest --ofp-tcp-listen-port 6633 --observe-link

```

### Terminal 2: Mininet Topology

* **Purpose:** Creates the virtual network and configures sFlow.
* **Environment:** System Native (Root).

```bash
cd ~/Desktop/NDTwin-Kernel/
# Start the custom topology script
sudo python3 testbed_topo.py
```

**Note:** After the topology starts, wait ~60 seconds to let the customized Ryu app finish host discovery and path installation (you will see "all-destination paths installed" message).
Only then start launching NDTwin (backend/GUI), otherwise NDTwin may query Ryu before the topology is fully detected.
![Alt text](/images/all-destination-flow-entries_installed.png)

**Note:** If you want to restart Mininet and run the topology again, clean up the previous Mininet state first:
```bash
sudo mn -c
```


### Terminal 3: NDTwin Kernel

* **Purpose:** Starts the NDTwin Kernel.
* **Environment:** System Native (Root).

```bash
cd ~/Desktop/NDTwin-Kernel/build

# Set API Key
# If you do not have a valid OpenAI API key, you can input any random string 
# (e.g., "12345") to bypass the check. The system will run, but LLM features will be disabled.
export OPENAI_API_KEY="any-random-string-here"

# Execute with sudo, preserving the environment variable (-E)
sudo -E bin/ndtwin_kernel --loglevel info
```
![Alt text](/images/ndtwin_launching.png)


### Generating Traffic (Validation)

To test if the system is working, you can generate traffic inside the **Mininet CLI** (Terminal 2).

1. **Start an iperf3 server on Host 1:**
```bash
mininet> h1 iperf3 -s &

```


2. **Run a client on Host 2:**
```bash
mininet> h2 iperf3 -c h1 -t 300

```


3. **Verify detected flow data (NDTwin API):**
Use the following command to query NDTwin and confirm whether flow data has been successfully captured/recorded by the system:
```bash
curl -X GET http://localhost:8000/ndt/get_detected_flow_data
```

**Expected:** The API returns a JSON response containing the currently detected flow records (or an empty list if no flows have been captured yet).

![Alt text](/images/ndtwin_api_demo.png)

See more NDTwin API docs in [this link](../../../NDTwin%20Developer%20Manual/NDTwin%20Application/NDTwin%20Kernel%20API.md).


---

### Safe Shutdown Procedure

When finishing your experiment, please close the system in **reverse order** and perform cleanup to avoid errors in future runs:

1. In **Terminal 2 (Mininet)**, type `exit` to quit the CLI.
2. The script will automatically remove the IP alias.
3. Run a final cleanup command in Terminal 2:
```bash
sudo mn -c

```


4. Close all other terminal windows.

---

