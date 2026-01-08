---
title: NDTwin Simulation Platform
description: >
 NDTwin Simulation Platform
date: 2026-01-04
weight: 4
---

# NDTwin Simulation Subsystem Documentation

## 1. Overview

The **NDTwin Simulation Subsystem** is a distributed, high-throughput framework designed to evaluate network "what-if" scenarios (such as energy-saving topology changes) in parallel.

It separates the **control logic** (App), the **routing logic** (Request Manager), and the **computation logic** (Simulation Server) into distinct, asynchronous components. This design allows the NDT to run computationally intensive simulations (like Max-Min Fairness calculations) without blocking the real-time network controller.

---

## 2. System Architecture

The system implements a **three-tier architecture** supported by a shared **Network File System (NFS)** for heavy data exchange.

### A. The Energy-Saving Application (`App`)

* **Role:** The client and orchestrator.
* **Paper Context:** Corresponds to the "Energy-saving App" that detects low-load thresholds (e.g., link utilization < 30%) and generates candidate power-off plans.
* **Implementation Details:**
* **Input Generation:** Generates simulation cases (JSON/TXT) containing network state (Flows, Topology).
* **NFS I/O:** Writes inputs directly to `abs_input_file_path` on the shared NFS mount to avoid HTTP overhead.
* **Callback Server:** Runs a local `HttpSession` server to listen for simulation results asynchronously.



### B. The Request Manager (`Request Manager`)

* **Role:** The reverse proxy / load balancer.
* **Paper Context:** Corresponds to the "Simulation request and reply manager."
* **Implementation Details:**
* **Decoupling:** Decouples the App from the Sim Server. The App does not need to know the IP/Port of the physical simulation workers.
* **Asynchronous Routing:** Uses `boost::asio::strand` to handle concurrent requests without race conditions. It forwards requests to the Sim Server and routes callbacks back to the specific App instance based on `app_id`.



### C. The Simulation Server (`Sim Server`)

* **Role:** The compute worker.
* **Paper Context:** Corresponds to the "Simulation Server" that runs cases in parallel.
* **Implementation Details:**
* **Process Isolation:** Uses `boost::process` to spawn external binary simulators (e.g., `power_sim`) as child processes.
* **Parallelism:** Can execute multiple simulation cases simultaneously (utilizing multi-core CPUs) without blocking the main I/O thread.
* **Life-cycle Management:** Monitors process exit codes (`on_exit`) and triggers the completion workflow.



---

## 3. The Simulation Workflow

The workflow minimizes latency for control messages while maximizing throughput for data transfer.

### Phase 1: Preparation (Data Plane)

Before submitting a request, the **App** prepares the data.

* **Logic:** The App calculates candidate plans based on F1-F6 strategies (see Section 4).
* **Data Transport:** Instead of sending megabytes of flow table data via HTTP, the App writes the input file to the NFS.
```cpp
// App.cpp logic
fs::path input_file_path = abs_input_file_path(simulator, version, case_id, input_filename);
std::ofstream out(input_file_path, std::ios::binary); // Write to NFS

```



### Phase 2: Submission (Control Plane)

The **App** sends a lightweight JSON signal to the **Request Manager**.

* **Payload:**
```json
{
  "simulator": "power_sim",
  "version": "1.0",
  "app_id": "101",
  "case_id": "case_1",
  "input_filename": "input.json"
}

```


* **Routing:** The Request Manager identifies the target Simulation Server and forwards the packet.

### Phase 3: Execution

The **Simulation Server** receives the instruction.

* **Execution:** It invokes the specific simulator binary requested (e.g., `power_sim`).
* **Non-Blocking:** The `run_simulator` function uses `boost::process::async_pipe` to ensure the server remains responsive to new requests while the simulation calculates results.
```cpp
// Sim_server.cpp
auto process = std::make_shared<bp::child>(command, bp::std_out > stdout, ioc);

```



### Phase 4: Analysis & Callback

* **Completion:** When the simulator process exits, the Sim Server verifies the exit code.
* **Output:** The simulator binary has already written the result to the output file on NFS.
* **Notification:** The Sim Server sends a callback HTTP POST to the Request Manager, which routes it back to the App.

---

## 4. Simulation Strategies (Logic)

While the C++ code provided handles the *execution framework*, the actual logic runs inside the `power_sim` binary. The NDTwin framework is designed to evaluate plans based on the six factors identified in the research:

1. **F1 (Switch Power):** Prioritizing high-draw switches for maximum savings.
2. **F2 (Traffic Load):** Avoiding "hotspot" switches to prevent congestion.
3. **F3 (Flow Count):** Minimizing the number of users affected by rerouting.
4. **F4 (Flow Entry Limit):** Ensuring the new routing plan does not exceed hardware TCAM limits (e.g., 121 entries/batch on Brocade ICX7250).
5. **F5 (Scope of Change):** Minimizing the number of switches requiring updates.
6. **F6 (Throughput):** Using Max-Min Fairness models to ensure elephant flows maintain QoE.

The **Simulation Server** passes the `case_id` and input paths to the binary, which calculates a score based on these factors and writes the result to NFS.

---

## 5. Technical Implementation Highlights

### Efficient Data Handling (NFS)

The system employs a "reference-by-path" pattern.

* **Problem:** JSON/HTTP is inefficient for large matrices of flow data.
* **Solution:** The HTTP payload only carries the *path* to the data. The `Sim Server` reads the actual data from the mounted NFS directory at `input_file_path`.
* **Safety:** The code includes `safe_system(mount_nfs_command())` and signal handlers (`SIGINT`, `SIGTERM`) to ensure file systems are mounted/unmounted correctly.

### Robust Asynchronous Networking

Built on `Boost.Asio`, the system is fully non-blocking.

* **Strands:** `net::strand` is used extensively in `HttpSession` to serialize handlers. This prevents race conditions in the multi-threaded environment without the performance penalty of coarse-grained mutex locking.
* **Keep-Alive:** The HTTP implementation supports persistent connections, reducing TCP handshake overhead during batch simulations (e.g., evaluating 26 different power-off plans in rapid succession).

### Error Propagation

* If a simulation binary crashes or fails (non-zero exit code), the `Sim Server` captures this event in `bp::on_exit`.
* A `success: false` flag is returned in the JSON callback, allowing the App to discard that plan and attempt the next best candidate.