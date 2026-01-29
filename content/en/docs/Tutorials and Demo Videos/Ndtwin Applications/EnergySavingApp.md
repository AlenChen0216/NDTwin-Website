---
title: Energy-Saving App
description: >
  This energy-saving application will dynamically power off a switch when the average bandwidth utilization of its links drops below a low-watermark threshold and will power it on again when its average link utilization exceeds a high-watermark threshold. For a network whose topology is like that of a datacenter network with many redundnt switches to increase the total capacity of the network and reliability, this energy-saving application can dynamically and iteratively power off many under-utilized switches to greatly reduce the energy consumption of the network without degrading the Quality of Service (QoS) of the network.  
date: 2017-01-05
weight: 1
---

## Step1: Simulation Platform Manager

* **Path:** `Simulation-Platform-Manager`
* **Note:** Backend server for the simulation environment.

1. Navigate to the directory:
```bash
cd ~Simulation-Platform-Manager

```


2. Compile (Skip if already compiled):
```bash
make all
```


3. Execute the server:
```bash
sudo ./simulation_platform_manager

```

![Alt text](/images/simulation-platform-manager_launching_succeed.png)



---

## Step2: Energy Saving App

* **Path:** `Energy-Saving-App`
* **Note:** Energy calculation module.

1. Navigate to the directory:
```bash
cd ~/Energy-Saving-App

```


2. Compile (Skip if already compiled):
```bash
make all

```


3. Execute the application:
```bash
sudo ./energy_saving_app

```

![Alt text](/images/energy-saving-app_lanuching_succeed.png)
