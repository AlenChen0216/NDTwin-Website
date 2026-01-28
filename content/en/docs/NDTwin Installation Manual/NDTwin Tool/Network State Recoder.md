---
title: Network State Recoder
description: >
date: 2017-01-05
weight: 8
---

# Network State Recorder (NSR) - Installation & Configuration Manual

Network State Recorder (NSR) is a tool that periodically fetches network state data from NDTwin and stores it in JSON files, which are then compressed into ZIP archives.

## 1. Prerequisites & Setup

Before running NSR, ensure the required libraries is installed, environment is configured correctly, and scripts have the necessary execution permissions.

### Requirements

- **Python**: 3.8 or higher
- **NDTwin Server**: Running and accessible
- **Ryu**: For setting flow rule to switches
- **Python Dependencies**:
  - `nornir` - Network automation framework
  - `loguru` - Logging library
  - `orjson` - Fast JSON library
  - `requests` - HTTP library

```bash
pip install nornir loguru orjson requests
```

### Script Permissions

NSR relies on shell scripts for startup and shutdown. You must grant them execution privileges:

```bash
chmod +x start_network_state_recorder.sh stop_network_state_recorder.sh
```

### API Dependency

NSR requires connectivity to the NDTwin API. Ensure the NDTwin server is running and the following endpoints are accessible:

| Endpoint | Description |
| --- | --- |
| `/ndt/get_detected_flow_data` | Network flow detection data |
| `/ndt/get_graph_data` | Network topology/graph data |

## 2. Configuration

NSR uses YAML configuration files located in the project directory.

### Main Configuration (`NSR.yaml`)

This file configures the internal Nornir framework settings. In most cases, the default values are sufficient.

```yaml
---
inventory:
  plugin: SimpleInventory
  options:
    host_file: "./setting/recorder_setting.yaml"

runner:
  plugin: threaded
  options:
    num_workers: 1

logging:
  enabled: false

```

### Recorder Settings (`setting/recorder_setting.yaml`)

This is the primary configuration file for NSR behavior (intervals, target server, logging).

```yaml
---
Recorder:
  data:
    ndtwin_kernel: "http://127.0.0.1:8000"
    request_interval: 5    # Data fetch interval in seconds (integer, >= 1)
    storage_interval: 2    # File rotation interval in minutes (integer, >= 1)
    log_level: "DEBUG"     # Logging level

```

#### Configuration Parameters

| Parameter | Type | Default | Description |
| --- | --- | --- | --- |
| `ndtwin_kernel` | string | `http://127.0.0.1:8000` | URL of the NDTwin kernel |
| `request_interval` | integer | `5` | Frequency to fetch data from NDTwin (seconds) |
| `storage_interval` | integer | `2` | Frequency to rotate and compress JSON files (minutes) |
| `log_level` | string | `DEBUG` | Minimum logging level (TRACE, DEBUG, INFO, WARNING, ERROR) |

#### Log Levels

* `TRACE`: Most detailed logging
* `DEBUG`: Debugging information (Default)
* `INFO`: General operational messages
* `WARNING`: Warning messages
* `ERROR`: Error messages only

---

