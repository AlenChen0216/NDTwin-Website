---
title: Network State Recoder
description: > 
    Network State Recorder (NSR) is a tool that periodically fetches network state data from NDTwin and stores it in JSON files, which are then compressed into ZIP archives for efficient storage. The recorded data can be used for the Visualizer and Web-GUI to replay network states over time.
date: 2017-01-05
weight: 30
---


# Network State Recorder (NSR) - User Operations Manual

## 1. Features Overview

* **Periodic Data Collection**: Automatically fetches flow and graph data from NDTwin.
* **Efficient Storage**: Compresses JSON data into ZIP archives.
* **Multi-threaded**: Concurrent fetching, writing, and compression.
* **Graceful Shutdown**: Handles SIGINT/SIGTERM for clean data saving.

## 2. Usage Guide

### Starting NSR

#### Option 1: Background Mode (Recommended)

Use this for long-term data collection. It runs NSR in the background using `nohup`.

```bash
./start_NSR.sh

```

#### Option 2: Foreground Mode (Debugging)

Use this to monitor real-time logs in the terminal.

```bash
python3 NSR.py

```

### Checking Status

To verify if NSR is currently running:

```bash
pgrep -f NSR.py

```

*If a process ID (PID) is returned, NSR is running.*

### Stopping NSR

#### Option 1: Using the Close Script

Gracefully terminates the process.

```bash
./close_NSR.sh

```

*Note: If permissions deny execution, try: `sudo ./close_NSR.sh*`

#### Option 2: Manual Termination

* **Foreground:** Press `Ctrl+C`.
* **Background:**
```bash
kill -15 $(pgrep -f NSR.py)

```



## 3. Output Data & Logs

### Data Location & Format

All data is stored in the `./recorded_info/` directory.

**Naming Convention:**

* Raw: `YYYY_MM_DD_HH-MM-SS_<datatype>.json`
* Compressed: `YYYY_MM_DD_HH-MM-SS_<datatype>_json.zip`

**Data Types:**

* `flowinfo`: Network flow information.
* `graphinfo`: Network graph/topology information.

### JSON Structure

Each file contains newline-delimited JSON records.

**Common Header:**

```json
{"timestamp": 1704067200000, ...}

```

**Flow Info Format (`flowinfo`):**

```json
{"timestamp": 1704067200000, "flowinfo":{[...]}}

```

**Graph Info Format (`graphinfo`):**

```json
{"timestamp": 1704067200000, "edges":{[...]}, "nodes":[{...}]}

```

### Logging

NSR logs are immediately written to the `./logs/` folder (not displayed in the terminal during background execution).

**Log File Format:** `logs/NSR_YYYY-MM-DD.log`

**Real-time Monitoring:**

```bash
tail -f logs/NSR_$(date +%Y-%m-%d).log

```

## 4. Troubleshooting

| Issue | Possible Cause | Solution |
| --- | --- | --- |
| **"NDTwin server is not reachable"** | Server down or wrong URL | Check `ndtwin_server` in `recorder_setting.yaml` and verify connectivity via `curl`. |
| **"No Recorder setting found"** | Config missing | Ensure `setting/recorder_setting.yaml` exists and YAML syntax is correct. |
| **Permission Denied** | Script not executable | Run `chmod +x *.sh` or use `sudo`. |
| **Cannot Stop NSR** | Permission restrictions | Use `sudo ./close_NSR.sh`. |
| **High Disk Usage** | Logs/Data growing too fast | Increase `request_interval` or decrease `storage_interval` in config. |

---