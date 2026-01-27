---
title: Operate a Physical (Hardware) Network
description: >
  A comprehensive guide to preparing the physical network environment.
  This section covers the necessary configurations for physical switches
  (enabling OpenFlow and sFlow) and details the system and Python dependencies required to deploy the SDN Controller.
date: 2025-12-24
weight: 3
---


**Pre-flight Checks:**

1. **Source Code:** Ensure `NDTwin-Kernel` is in your `~/Desktop` (or adjust paths below).
2. **Compilation:** Ensure `ndtwin_kernel` exists in `build/bin/` (from [Installation Manual](../../../NDTwin%20Installation%20Manual/NDTwin%20Kernel/Operate%20an%20Emulated%20(Software)%20Network/)).
3. **Topology Script:** Ensure `testbed_topo.py` exists (from [Installation Manual](../../../NDTwin%20Installation%20Manual/NDTwin%20Kernel/Operate%20an%20Emulated%20(Software)%20Network/)).

**Startup Order is Critical:** Please execute Terminals 1 through 2 in the **exact order** listed below.

### Terminal 1: Ryu Controller

* **Purpose:** Starts the SDN Logic.
* **Environment:** `ryu-env` (Python 3.8).

```bash
conda activate ryu-env
# 'intelligent_router.py' is our custom controller app
ryu-manager intelligent_router.py ryu.app.rest_topology ryu.app.ofctl_rest --ofp-tcp-listen-port 6633 --observe-link

```

**Note:** After lanuching, wait seconds to let the customized Ryu app connect to all network devices, finish host discovery and path installation (you will see "all-destination paths installed" message).
Only then start launching NDTwin (backend/GUI), otherwise NDTwin may query Ryu before the topology is fully detected.
![Alt text](/images/all_destination_flow_entries_installed_on_testbed.png)




### Terminal 2: NDTwin Kernel

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
![Alt text](/images/ndtwin_lanuching_on_testbed.png)


### Generating Traffic (Validation)

To test if the system is working, you can generate traffic.

1. **Start an iperf3 server on Host 1 (192.168.2.180):**
```bash
iperf3 -s
```


2. **Run a client on Host 2:**
```bash
iperf3 -c 192.168.2.180 -t 300

```


3. **Verify detected flow data (NDTwin API):**
Use the following command to query NDTwin and confirm whether flow data has been successfully captured/recorded by the system:
```bash
curl -X GET http://localhost:8000/ndt/get_detected_flow_data
```

**Expected:** The API returns a JSON response containing the currently detected flow records (or an empty list if no flows have been captured yet).

![Alt text](/images/ndtwin_api_call_succeeded_on_testbed.png)

See more NDTwin API docs in [NDTwin API](../../NDTwin%20Developer%20Manual/NDTwin%20Application/NDTwin%20Kernel%20API.md).


---



