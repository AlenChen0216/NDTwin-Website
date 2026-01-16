---
title: Use the Demo VM for a Quick Start
description: >
  A quick start guide designed for the pre-configured Ubuntu VM image.
  All dependencies (Ryu, Mininet, NDT Kernel) are pre-installed.
  Users can skip installation steps and directly run the system.
date: 2025-12-24
weight: 1
---

**Pre-flight Checks:**

* Please prepare **3 separate Terminal windows**.
* **Startup Order is Critical:** Please execute Terminals 1 through 3 in the exact order listed below.

---

## Terminal 1: Ryu Controller (SDN Controller)

* **Path:** `Desktop`
* **Environment:** `ryu-env` (Python 3.8)

1. Navigate to the directory:
```bash
cd ~/Desktop/

```


2. Activate the environment:
```bash
conda activate ryu-env

```


3. Execute the controller:
```bash
ryu-manager intelligent_router.py ryu.app.rest_topology ryu.app.ofctl_rest --ofp-tcp-listen-port 6633 --observe-link

```



---

## Terminal 2: Mininet (Virtual Network Topology)

* **Path:** `Desktop`
* **Environment:** System Native (Root)

1. Navigate to the directory:
```bash
cd ~/Desktop/

```


2. Clean up old network artifacts (**Crucial step**):
```bash
sudo mn -c

```


3. Start the topology:
```bash
sudo python3 my_topo.py

```



---

## Terminal 3: NDT Kernel (Digital Twin Core)

* **Path:** `NetworkDigitalTwin-main/build`
* **Note:** Requires API Key. Use `-E` to preserve environment variables for sudo.

1. Navigate to the build directory:
```bash
cd ~/Desktop/NetworkDigitalTwin-main/build

```


2. Set API Key (Skip if already configured in shell profile):
```bash
export OPENAI_API_KEY="sk-proj-xxxxxxxxxxxxxxxx"

```


3. Execute the main program:
```bash
sudo -E bin/ndt_main --loglevel info

```

---

## Safe Shutdown Procedure

When finishing your experiment, please close the system in **reverse order** and perform cleanup to avoid errors in future runs:

1. In **Terminal 2 (Mininet)**, type `exit` to quit the CLI.
2. In **Terminal 2**, run the cleanup command:
```bash
sudo mn -c

```
3. Close all other terminal windows.


## Next Steps & Advanced Features

Once the core system is running, you can explore additional features using the instructions below:

### 1. Web GUI Interface 
For a graphical interface to monitor the network status.

* **Action:** Open your web browser (Firefox/Chrome).
* **URL:** Navigate to the following address:
    ```text
    http://localhost:3000/
    ```

---

### 2. Real-time Visualization 
To view real-time traffic flow animations using the JavaFX desktop application.

* **Command:**
    ```bash
    cd "/home/ndtwin/Desktop/NDTwin traffic animation/NDTwin_traffic_animation_v5.5.0"
    ./run_debug.sh
    ```

---

### 3. Network Traffic Generation 
To generate massive amounts of traffic for stress testing.

* To do list

---

### 4. Third-Party Applications 

To run external modules. Please open **new terminal windows** for these applications.

#### Option A: Energy Saving App
This application requires two components running simultaneously.

* **Step 1: Start Simulation Server**
    ```bash
    cd "/home/ndtwin/Desktop/Energy Saving App/NDT-Simulation-Server-master/"
    sudo ./server
    ```

* **Step 2: Start Power App (New Terminal)**
    ```bash
    cd "/home/ndtwin/Desktop/Energy Saving App/NDT-Power-master/"
    sudo ./power_app
    ```

#### Option B: Traffic Engineering App

* **Command:**
    ```bash
    cd "/home/ndtwin/Desktop/Traffic Engineering App/"
    conda activate te-env
    sudo python3 TE.py
    ```