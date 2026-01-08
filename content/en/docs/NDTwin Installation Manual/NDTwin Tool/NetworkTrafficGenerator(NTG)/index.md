---
title: Network Traffic Generator
description: >
date: 2017-01-05
weight: 7
---
# Network Traffic Generator — Installation Manual

![](working.png)

This guide explains how to setup and use commands in our **Network Traffic Generator(NTG)** to generate traffic flows in intervals with specific parameters. In this manual, we demonstrate things as below :

- [Required environment and libraries for NTG](#required-environment-and-libraries-for-ntg)
- [NTG configuration](#network-traffic-generator-configuration)
- [Flow generation configuration](#flow-command-configuration)
- [Support commands in NTG](#support-commands-in-ntg)


## Required environment and libraries for NTG

- **Linux** system is needed for all machines.
- Python 3.8+ recommended.

Install Python + pip:

Ubuntu/Debian:

```bash
sudo apt update
sudo apt install -y python3 python3-pip
python3 --version
pip3 --version
```

Libraries used by NTG controller-side scripts (`interactive_commands.py` + `Utilis/*.py`):

- `loguru`
- `prompt_toolkit`
- `nornir`, `nornir-utils`
- `pyyaml`
- `numpy`, `pandas`
- `paramiko`
- `requests`

Install them:

```bash
pip install --upgrade pip
pip install loguru prompt_toolkit nornir nornir-utils pyyaml numpy pandas paramiko requests
```

If your environment uses Conda/virtualenv, activate it before installing packages.

## Files Overview

![](files.png)

- `interactive_commands.py`: Interactive CLI that accepts commands.
- `Utilis/`: Contains some related util functions.
- `setting/Hardware.yaml`: Nornir host inventory (testbed controller + worker nodes + on-site host IPs).
- `setting/API_server_startup.yaml`: Nornir group file (startup/shutdown commands for worker-node API servers).
- `setting/Mininet.yaml`: Mininet runtime mode and local ndtwin server (basic metadata).
- `NTG.yaml`: Nornir configuration pointing to the inventory files above.
- `flow_template.json` & `dist_template.json`: Example configuration for intervals and flow generation parameters.

## Network Traffic Generator Configuration
Ensure the following files exist and match your environment.

`NTG.yaml`
```yaml
inventory:
  plugin: SimpleInventory
  options:
    host_file: "./setting/Hardware.yaml"
    group_file: "./setting/API_server_startup.yaml"

runner:
  plugin: threaded
  options:
    num_workers: 5

logging:
  enabled: false
```
Notes:
- If you want to use `Mininet`, please change the path of `host_file` to `./setting/Mininet.yaml`

`setting/API_server_startup.yaml`
```yaml
worker_node_servers:
  data:
    startup_commands:
      - "source ~/ntg/bin/activate && cd Desktop/NTG && pwd && nohup uvicorn worker_node:app --host 0.0.0.0 --port 8000 > uvi.log 2>&1 &"
    shutdown_commands:
      - "killall -9 uvicorn"
```
Notes:
- `startup_commands` should start your FastAPI server (e.g., `uvicorn worker_node:app ...`) on each worker node.
- Adjust virtualenv path and ports as needed.

`setting/Hardware.yaml` (abbreviated; tailor to your hosts)
```yaml
Hardware_Testbed:
  hostname: "hardware_testbed"
  data:
    ndtwin_server: "http://10.10.xx.xx:8000"
    recycle_interval: 10

worker_node1:
  hostname: 10.10.xx.xx
  username: "server1"
  password: "xxooxx"
  port: 22
  groups:
    - worker_node_servers
  data:
    worker_node_server: "http://10.10.xx.xx:8000"
    on_site_hosts:
      h1: "192.168.yy.yy"
      # ... continue for h2–hzz

# Repeat similarly for worker_node2–worker_node4 with their ranges
```

Notes:
- `ndtwin_server` is the location of our NDTwin controller.
- `recycle_interval` is the interval for asking whether `worker_node_server` have finished some iperf3 and recycle the used ports.
- `groups` must be a YAML list (e.g., `- worker_node_servers`).
- `data.worker_node_server` must be reachable from the machine running `interactive_commands.py`.
- `data.on_site_hosts` maps logical host names (e.g., h1) to IP addresses. If you define lots of hosts in one worker node, please list all of them.
- Our NTG will distribute the role of client/server in iperf using the logical host names from the **NDTwin Controller**. Thus, please make sure the logical host name is the same in **NDTwin Controller** and **NTG**.

`setting/Mininet.yaml`

```yaml
Mininet_Testbed:
  hostname: "mininet_testbed"
  data:
    ndtwin_server: "http://127.0.0.1:8000"
    mode: "cli"
```

Notes:

- `mode` can be `cli` (Mininet's CLI) or `custom_command` (our generator's cli)
- **When using it, please make sure that Mininet is on you're machine.**

## Flow Generation Configuration

### `flow` Command Configuration

`flow_template.json` defines intervals and, for each interval, the flow mix and parameters. Key sections:

- `traffic_generator(iperf_or_iperf3)`: `iperf3` or `iperf` (tcp/udp supported)
- `intervals`: Array of interval objects. Each interval may include `varied_traffic` or `fixed_traffic` or both.

Common fields inside `varied_traffic`:

- `flow_arrival_rate(flow/sec)`: Average flow arrivals per second during the interval.
- `flow_distance_probability`: Probability distribution over `near/middle/far` path selection.
- `flow_type_probability`: Probability distribution over flow types (tcp/udp, limited/unlimited size/rate/duration).
- `flow_parameters`: Per-type parameter map defining `size(bytes)`, `rate(bits)`, and/or `duration(sec)` when applicable.

Common fields inside `fixed_traffic`:

- `fixed_flow_number`: Exact number of flows to generate in the interval.
- `flow_distance_probability`, `flow_type_probability`, `flow_parameters`: Same semantics as above.

Parameter format rules:

- `size(bytes)`: integer or suffixed with `K`, `M`, `G` (e.g., `10K`, `2M`)
  - Defining how many bytes you want to send.
- `rate(bits)`: integer or suffixed with `K`, `M`, `G` (e.g., `37.4M` allowed where implementation supports decimals)
  - Defining the sending rate of flows.
- `duration(sec)`: positive integer seconds. 
  - Defining how long will the flow keep alive.
- Provide only the parameters that make sense for the chosen type:
  - limited size → requires `size(bytes)`
  - limited rate → requires `rate(bits)`
  - limited duration → requires `duration(sec)`
  - udp types include `-u` internally; tcp types do not.

Supported flow types for `varied_traffic`:

- `unlimited_size_unlimited_rate_unlimited_duration_tcp`
- `unlimited_size_unlimited_rate_limited_duration_tcp`
- `limited_size_unlimited_rate_tcp`
- `unlimited_size_limited_rate_unlimited_duration_tcp`
- `unlimited_size_limited_rate_limited_duration_tcp`
- `limited_size_limited_rate_tcp`
- `unlimited_size_unlimited_rate_unlimited_duration_udp`
- `unlimited_size_unlimited_rate_limited_duration_udp`
- `limited_size_unlimited_rate_udp`
- `unlimited_size_limited_rate_unlimited_duration_udp`
- `unlimited_size_limited_rate_limited_duration_udp`
- `limited_size_limited_rate_udp`

Supported flow types for `fixed_traffic` :

- `limited_size_unlimited_rate_tcp`
- `limited_size_limited_rate_tcp`
- `unlimited_size_limited_rate_unlimited_duration_udp`
- `unlimited_size_limited_rate_limited_duration_tcp`

Note: for each section (`varied_traffic` or `fixed_traffic`), the keys in `flow_type_probability` and `flow_parameters` must be chosen from the corresponding list above.

Example snippet (already present in `flow_template.json`):

```json
{
  "traffic_generator(iperf_or_iperf3)": "iperf3",
  "intervals": [
    {
      "interval_duration(d/h/m/s)": "3s",
      "varied_traffic": {
        "flow_arrival_rate(flow/sec)": 5,
        "flow_distance_probability": {"near": 0.0, "middle": 0.5, "far": 0.5},
        "flow_type_probability": {
          "limited_size_limited_rate_tcp": 0.1,
          "limited_size_unlimited_rate_udp": 0.9
        },
        "flow_parameters": {
          "limited_size_limited_rate_tcp": {"size(bytes)": "2M", "rate(bits)": "30M"},
          "limited_size_unlimited_rate_udp": {"size(bytes)": "4K"}
        }
      },
      "fixed_traffic":{
        "fixed_flow_number": 8,
        "flow_distance_probability": {"near": 0.0, "middle": 0.3, "far": 0.7},
        "flow_type_probability": {
          "limited_size_limited_rate_tcp": 0.2,
          "limited_size_unlimited_rate_udp": 0.8
        },
        "flow_parameters": {
          "limited_size_limited_rate_tcp": {"size(bytes)": "5K", "rate(bits)": "5M"},
          "limited_size_unlimited_rate_udp": {"size(bytes)": "4K"}
        }
      }
    }
  ]
}
```

#### Notice that the `limited_duration` in `fixed_traffic` will use `interval_duration(d/h/m/s)` as the default duration setting. If you would like to use the `interval_duration(d/h/m/s)` as the flow duration, then just make it empty as below

```json
{
    "interval_duration(d/h/m/s)": "10s",
    "fixed_traffic": {
        "fixed_flow_number": 20,
        "flow_distance_probability": {
            "near": 0.0,
            "middle": 0.5,
            "far": 0.5
        },
        "flow_type_probability": {
            "unlimited_size_limited_rate_limited_duration_tcp": 1.0
        },
        "flow_parameters": {
            "unlimited_size_limited_rate_limited_duration_tcp": {
                "rate(bits)": "37.4M"
            }
        }
    }
}
```

### `dist` Command Configuration

`dist_template.json` is the same as `flow_template.json` but the parameter of flows are come from distribution files.

The distribution file is the measured network flow properties from data centers or ISP. For using `dist` command, you need to generate 3 kinds of distribution files:

1. `distribution_flow_duration.csv` : the distribution of flow's life span.
2. `distribution_flow_sending_rate.csv` : the distribution of flow's sending rate
3. `distribution_flow_size.csv` : the distribution of flow's sent file size.

Each of the distribution files are used to replace the `duration(sec)`, `rate(bits)`, `size(bytes)` parameter of `flow_template.json`.

The distribution files is `csv` formate and the content likes below:

```csv
bin_midpoint,probability
1024,0.3
2048,0.5
4096,0.2
```

- `bin_midpoint`: The representative value for that bin (e.g., average flow size in bytes).
- `probability`: The probability of a flow having the characteristic of this bin. The sum of probabilities should be 1.0.

Example snippet (already present in `dist_template.json`):

```json
{
    "traffic_generator(iperf_or_iperf3)": "iperf3",
    "flow_size_csv": "./distribution_flow_size.csv",
    "flow_duration_csv": "./distribution_flow_duration.csv",
    "flow_sending_rate_csv": "./distribution_flow_sending_rate.csv",
    "intervals": [
        {
            "interval_duration(d/h/m/s)": "2s",
            "fixed_traffic": {
                "fixed_flow_number": 5,
                "flow_distance_probability": {
                    "near": 0.0,
                    "middle": 0.5,
                    "far": 0.5
                },
                "flow_type_probability": {
                    "unlimited_size_limited_rate_limited_duration_tcp": 1.0
                }
            }
        },
        {
            "interval_duration(d/h/m/s)": "3s",
            "varied_traffic": {
                "flow_arrival_rate(flow/sec)": 2,
                "flow_distance_probability": {
                    "near": 0.0,
                    "middle": 0.3,
                    "far": 0.7
                },
                "flow_type_probability": {
                    "unlimited_size_unlimited_rate_limited_duration_tcp": 0.3,
                    "limited_size_unlimited_rate_tcp": 0.3,
                    "unlimited_size_limited_rate_limited_duration_tcp": 0.4
                }
            }
        }
    ]
}
```

## Support Commands in NTG

### Commands

Our NTG support commands as below :

- `flow` : generate flows with defined parameters
- `dist` : flows' parameters come from distribution files.
- `exit` : exit the NTG.

**Notice that when using `flow` and `dist` command, you must add `--config flow_template.json` to specify which flow configuration files to use in one experiment.**
