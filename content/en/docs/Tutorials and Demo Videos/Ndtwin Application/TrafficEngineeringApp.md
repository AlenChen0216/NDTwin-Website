---
title: Traffic-Engineering App
description: >
  This traffic-enginerring application will dynamically perform load-balancing among the output ports of any Equal-Cost Multi-Path (ECMP) group in the network.  In the ECMP scheme, flows are dispatched to the output ports of an ECMP group based on the hash function used by ECMP and the five-tuple infomation (i.e., the source and destination IP addresses, the source and destination port numbers, and the protocol type) in their packet headers. When the load dispatched to the output ports of many ECMP groups is imbalanced in the network, this application can significantly improve the  utilization of the network.  
date: 2017-01-05
weight: 2
---

---
## Traffic Engineering App (TE App)

* **Path:** `Traffic Engineering App`
* **Environment:** `te-env` (Python 3.12)
* **Note:** Uses `$(which python)` to force sudo to use the Conda environment's Python instead of the system Python.

1. Navigate to the directory:
```bash
cd ~/Desktop/Traffic Engineering App

```


2. Activate the environment:
```bash
conda activate te-env

```


3. Execute the application:
```bash
sudo $(which python) TE.py

```



---
