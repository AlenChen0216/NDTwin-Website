---
title: Network Traffic Generator
description: >
date: 2017-01-05
weight: 20
---

# Network Traffic Generator — User Manual

- [How to use NTG in Mininet](#how-to-use-ntg-in-mininet)
- [How to use NTG in Hardware Testbed](#how-to-use-ntg-in-hardware)

### How to Use

Our NTG support **path complete** and **syntax complete**. Thus, you can use `tab` and `arrow keys` to type the command.
![complete1](complete1.png)
![complete2](complete2.png)
![complete3](complete3.png)
![complete4](complete4.png)
![complete5](complete5.png)

If your'e configuration file and command are correct, NTG will start generate flows as below:
![success1](success1.png)
However, If you have syntax error or configuration file error, it will show some error words as below:
![error1](./error1.png)
![error1](./error2.png)

### Notice

- Our NTG do not support experiment interrupt. Thus, if you want to interrupt one running experiment, it will immediately shut down the NTG as below:
  
![interrupt1](./interrupt1.png)

## How to use NTG in Mininet

### Pre-request

- You must have installed `Mininet`, `Ryu`, and `NDTwin`.
- You must have downloaded `NTG` and move those files and directories into folders with Mininet topology file written in `python`.
- You must modify the `NTG.yaml`'s `host_file` into `./setting/Mininet.yaml ` and parameters in `./setting/Mininet.yaml`.

### Demonstrating Mininet topology

```python
from mininet.topo import Topo
from mininet.net import Mininet
from mininet.cli import CLI
from mininet.node import RemoteController

class MyTopo(Topo):
  def build(self):
    s1 = self.addSwitch("s1")
    h1 = self.addHost("h1")
    h2 = self.addHost("h2")

    self.addLink(h1,s1)
    self.addLink(h2,s1)

if __name__ == "__main__":
  topo = MyTopo()
  net = Mininet(
        topo=topo,
        controller=RemoteController)
  net.start()
  CLI(net)
```

### Start Up Process

1. Modify the topology `python` code to import `interactive_commands.py` and replace Mininet's CLI to our NTG's CLI.

```python
from mininet.topo import Topo
from mininet.net import Mininet
#from mininet.cli import CLI
from mininet.node import RemoteController
from interactive_commands import interactive_command_mode

class MyTopo(Topo):
  def build(self):
    s1 = self.addSwitch("s1")
    h1 = self.addHost("h1")
    h2 = self.addHost("h2")

    self.addLink(h1,s1)
    self.addLink(h2,s1)

if __name__ == "__main__":
  topo = MyTopo()
  net = Mininet(
        topo=topo,
        controller=RemoteController)
  net.start()
  #CLI(net)
  interactive_command_mode(net)
```

2. Start the Ryu Controller.

```bash
ryu-manager intelligent_router.py ryu.app.rest_topology ryu.app.ofctl_rest --ofp-tcp-listen-port 6633 --observe-link
```

![ryu](./ryu.png)

3. Start the topology.

```bash
sudo python ./topo.py
```

![mininet](./mininet.png)

4. Since NTG need some topology information from NDTwin, you need to wait for the Ryu Controller to install all flow rules into switches.

![mininet1](./mininet1.png)

5. Start the NDTwin.

```bash
sudo bin/ndt_main
```
![ndtwin](./ndtwin.png)

6. Now, you can start using NTG

![mininet2](./mininet2.png)

## How to use NTG in Hardware

### Pre-request

- You must have installed `Ryu`, and `NDTwin`.
- You must have downloaded `NTG`.
- You must modify the `NTG.yaml`'s `host_file` into `./setting/Hardware.yaml ` and parameters in `./setting/Hardware.yaml`.
- For hardware testbed, we use **master and worker** architecture to generate flows. Thus, you need to prepare some machines running in **Linux** and install python libraries as below and move `worker_node.py` into those machines:

  - `fastapi`
  - `uvicorn` (used to start the API server)
  - `pydantic`
  - `loguru`
  - `orjson` (required by `ORJSONResponse`)

  ```bash
  pip install --upgrade pip
  pip install fastapi "uvicorn[standard]" pydantic loguru orjson
  ```

  Also, you need to make sure NTG can connect to those worker nodes.

### Start Up Process

1. Start the Ryu Controller

```bash
ryu-manager intelligent_router.py ryu.app.rest_topology ryu.app.ofctl_rest --ofp-tcp-listen-port 6633 --observe-link
```

2. Start the NDTwin

```bash
sudo bin/ndt_main
```

3. Start the NTG

```bash
python interactive_commands.py
```

4. Manually Start worker node API servers on machines if the worker nodes do not start up correctly.

```bash
uvicorn worker_node:app --host 0.0.0.0 --port 8000
```

5. Now, you can use `NTG` to generate flows.

## Tips

- Ensure all `data.worker_node_server` URLs in `Hardware.yaml` are reachable and the servers are running.
- Keep `flow_distance_probability` and `flow_type_probability` values normalized (sum to 1.0) for each section.
- Validate parameter names and formats in `flow_parameters` to match the selected types.
- For multiple consecutive experiments, the tool **resets internal state after completion**.
- For one experiment, it will be **ended only when all of flows, exclude flows with unlimited duration, are fininshed**.

## Troubleshooting

- If flows do not start: 
  - Confirm API servers are up, ports opened, and the `interactive_commands.py` process can reach them.
  - It may due to the CPU resources are not enough for you're flow configurations. Please lower the `flow numbers` or parameters to fix the question.
- If Nornir inventory errors occur: double-check that `groups` is a YAML list and host keys/fields are correctly indented.
- If `uvicorn` fails to start: verify your virtualenv and ensure `uvicorn` is installed (`pip install uvicorn fastapi`).

