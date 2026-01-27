---
title: Energy-Saving App
description: >
  This energy-saving application will dynamically power off a switch when the average bandwidth utilization of its links drops below a low-watermark threshold and will power it on again when its average link utilization exceeds a high-watermark threshold. For a network whose topology is like that of a datacenter network with many redundnt switches to increase the total capacity of the network and reliability, this energy-saving application can dynamically and iteratively power off many under-utilized switches to greatly reduce the energy consumption of the network without degrading the Quality of Service (QoS) of the network.  
date: 2017-01-05
weight: 1
---

## Step1: Simulation Server

* **Path:** `Energy-Saving App/NDT-Simulation-Server-master`
* **Note:** Backend server for the simulation environment.

1. Navigate to the directory:
```bash
cd ~/Desktop/Energy Saving App/NDT-Simulation-Server-master

```


2. Compile (Skip if already compiled):
```bash
make

```


3. Execute the server:
```bash
sudo ./server

```



---

## Step2: Power Saving App

* **Path:** `Energy Saving App/NDT-Power-master`
* **Note:** Energy calculation module.

1. Navigate to the directory:
```bash
cd ~/Desktop/Energy Saving App/NDT-Power-master

```


2. Compile (Skip if already compiled):
```bash
make

```


3. Execute the application:
```bash
sudo ./power

```
