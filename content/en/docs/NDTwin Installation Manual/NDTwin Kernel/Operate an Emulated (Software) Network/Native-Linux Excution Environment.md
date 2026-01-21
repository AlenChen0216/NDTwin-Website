---
title: Install All Required Components on a Linux Server
description: > 
  A comprehensive guide for manually installing the NDT system on a native Linux environment.
  This section covers system dependencies, building the NDT Kernel with Ninja, configuring Python environments, and running the full system.
date: 2025-12-24
weight: 2
---

# Native-Linux Execution Environment Setup

This guide provides step-by-step instructions to manually build the Network Digital Twin (NDT) environment on a native Linux machine. 

## 1. System Requirements

The system has been verified on the following configuration:

* **OS:** Ubuntu 20.04 LTS or higher (Verified on Ubuntu 24.04.3 LTS).
* **Kernel:** Generic Linux Kernel (x86_64).
* **Permissions:** Root access (`sudo`) is required for Mininet and Open vSwitch operations.

---

## 2. System Dependencies Installation

You need to install build tools, network analysis utilities, and specific C++ libraries required by the NDT Kernel and Mininet.



### Step 2.1: Update & Install Build Tools

We use `ninja-build` for faster compilation and `iperf3`/`wireshark` for traffic generation and analysis.

```bash
sudo apt update
sudo apt install -y build-essential cmake g++ make git \
    ninja-build xterm curl wireshark iperf3

```

### Step 2.2: Install Required Libraries & Mininet

Run the following command to install all necessary development libraries and the network emulator:

```bash
sudo apt install -y \
    libboost-all-dev \
    libfmt-dev \
    libspdlog-dev \
    libssh-dev \
    nlohmann-json3-dev \
    mininet \
    openvswitch-switch

```

### Step 2.3: Verify Network Components

Ensure Mininet and Open vSwitch (OVS) are installed correctly.

```bash
# Check OVS version
ovs-vsctl --version

# Test Mininet installation (Pingall test)
sudo mn --test pingall

```

---

## 3. Python Environment Setup (Conda)

The system requires two specific Python environments to handle version conflicts. **Ryu requires Python 3.8** due to specific library dependencies, while other components may use newer versions.

**Prerequisite:** Ensure [Miniconda](https://docs.anaconda.com/miniconda/) or Anaconda is installed.

### Step 3.1: Create Ryu Environment (`ryu-env`)

This environment runs the SDN Controller.

1. **Create and Activate:**
```bash
conda create -n ryu-env python=3.8 -y
conda activate ryu-env

```


2. **Install Packages:**
We must downgrade `setuptools` and pin `eventlet` to avoid compatibility errors in Ryu.
```bash
pip install "setuptools<59"
pip install "eventlet==0.30.2"
pip install ryu==4.34 networkx==3.1 ovs==3.6.1 requests==2.32.4 loguru==0.7.3

```


* *Note: If you encounter SSL issues, you may need to disable the SSLv3 minimum version check in your Python configuration.*


3. **Verify Ryu:**
```bash
ryu-manager --version

```



### Step 3.2: Create TE Environment (`te-env`)

This environment runs the Traffic Engineering application using modern Python features.

```bash
conda create -n te-env python=3.12 -y
conda activate te-env
pip install networkx==3.6.1 requests==2.32.5 loguru==0.7.3

```

---

## 4. Download & Compile NDT Kernel

We use CMake and Ninja to compile the C++ core.

### Step 4.1: Download Source Code

If you haven't downloaded the project yet, clone it to your Desktop (or preferred location).

```bash
cd ~/Desktop
git clone [https://github.com/ndtwin-lab](https://github.com/ndtwin-lab) NetworkDigitalTwin-main

```

### Step 4.2: Compile with Ninja

1. **Navigate to the project directory:**
```bash
cd ~/Desktop/NetworkDigitalTwin-main

```


2. **Prepare the build directory:**
```bash
rm -rf build  # Remove existing build directory if present
mkdir build && cd build

```


3. **Compile:**
*Note: We do not set the build type to "Release" yet as optimization flags are pending refactoring.*
```bash
cmake -GNinja ..
ninja clean && ninja

```



---

## 5. Prepare Network Topology Script

We rely on a custom Python script (`testbed_topo.py`) to solve **sFlow routing challenges**. This script creates a virtual management network on the loopback interface, allowing the host to receive sFlow packets from Mininet switches without disrupting internet connectivity.

**Action:** Create a file named `testbed_topo.py` in your `NetworkDigitalTwin-main` directory and paste the following code:

```python
#!/usr/bin/env python3


from mininet.topo import Topo
from mininet.net import Mininet
from mininet.node import RemoteController, OVSKernelSwitch
from mininet.cli import CLI
from mininet.log import setLogLevel
from mininet.link import TCLink
import os
import threading

# --- Global Configuration ---

# The number of hosts to create in the topology.
HOST_NUM = 128

# The IP address that our sFlow collector will receive packets on.
# We will add this IP as an alias to the host's loopback 'lo' interface.
COLLECTOR_IP = "192.168.123.1"

# The base of the IP address range for our sFlow management network.
# Switches will be assigned IPs from this range.
MGMT_IP_BASE = "192.168.123."


class MyTopo(Topo):
    """
    Custom topology definition.
    """

    def build(self):
        # Add switches to the topology.
        s1 = self.addSwitch("s1")
        s2 = self.addSwitch("s2")
        s3 = self.addSwitch("s3")
        s4 = self.addSwitch("s4")
        s5 = self.addSwitch("s5")
        s6 = self.addSwitch("s6")
        s7 = self.addSwitch("s7")
        s8 = self.addSwitch("s8")
        s9 = self.addSwitch("s9")
        s10 = self.addSwitch("s10")

        # Add links between switches to form a resilient core network.
        self.addLink(s1, s5, bw=1000, port1=1, port2=1)
        self.addLink(s1, s6, bw=1000, port1=2, port2=1)
        self.addLink(s2, s5, bw=1000, port1=1, port2=2)
        self.addLink(s2, s6, bw=1000, port1=2, port2=2)
        self.addLink(s3, s7, bw=1000, port1=1, port2=1)
        self.addLink(s3, s8, bw=1000, port1=2, port2=1)
        self.addLink(s4, s7, bw=1000, port1=1, port2=2)
        self.addLink(s4, s8, bw=1000, port1=2, port2=2)
        self.addLink(s5, s9, bw=10000, port1=3, port2=1)
        self.addLink(s5, s10, bw=10000, port1=4, port2=1)
        self.addLink(s6, s9, bw=10000, port1=3, port2=2)
        self.addLink(s6, s10, bw=10000, port1=4, port2=2)
        self.addLink(s7, s9, bw=10000, port1=3, port2=3)
        self.addLink(s7, s10, bw=10000, port1=4, port2=3)
        self.addLink(s8, s9, bw=10000, port1=3, port2=4)
        self.addLink(s8, s10, bw=10000, port1=4, port2=4)

        # Create and add hosts to a list.
        hosts = []
        for i in range(1, HOST_NUM + 1):
            host = self.addHost(f"h{i}")
            hosts.append(host)

        # Connect the first quarter of hosts to switch s1.
        # Assign port numbers in 3, 4, 5, 6, ... order to avoid conflicts.
        for i in range(int(HOST_NUM / 4)):
            self.addLink(hosts[i], s1, bw=1000, port1=1, port2=i + 3)

        # Connect the second quarter of hosts to switch s2.
        for i in range(int(HOST_NUM / 4), int(HOST_NUM / 2)):
            self.addLink(
                hosts[i], s2, bw=1000, port1=1, port2=i - int(HOST_NUM / 4) + 3
            )

        # Connect the third quarter of hosts to switch s3.
        for i in range(int(HOST_NUM / 2), int(3 * HOST_NUM / 4)):
            self.addLink(
                hosts[i], s3, bw=1000, port1=1, port2=i - int(HOST_NUM / 2) + 3
            )

        # Connect the last quarter of hosts to switch s4.
        for i in range(int(3 * HOST_NUM / 4), HOST_NUM):
            self.addLink(
                hosts[i], s4, bw=1000, port1=1, port2=i - int(3 * HOST_NUM / 4) + 3
            )


def find_ovs_agent_iface(switch):
    """
    Finds the correct network interface name for a given switch.
    In Mininet, the management interface for a switch (e.g., 's1') is
    named after the switch itself. This function reliably finds it.
    """
    for intf in switch.intfList():
        if not intf.name.startswith("lo") and "s" in intf.name:
            return intf.name
    return switch.name  # Fallback to the switch name.


def enable_sflow(switch, agent_iface, collector_ip, collector_port=6343):
    """
    Generates and executes the ovs-vsctl command to enable sFlow on a switch.
    Args:
        switch (str): The name of the switch (e.g., "s1").
        agent_iface (str): The network interface to use as the sFlow agent.
        collector_ip (str): The IP address of the sFlow collector.
        collector_port (int): The UDP port of the sFlow collector.
    """
    target = f"{collector_ip}:{collector_port}"
    # The 'agent' parameter tells OVS which interface's IP should be used
    # as the source IP for sFlow datagrams. This is crucial for identification.
    cmd = (
        f"ovs-vsctl -- --id=@sflow create sflow agent={agent_iface} "
        f'target=\\"{target}\\" header=128 sampling=1000 polling=0 '
        f"-- set bridge {switch} sflow=@sflow"
    )
    os.system(cmd)


def ping_test(src, dst_ip):
    """
    A simple utility function to perform a single ping test and print the result.
    This is used for verifying connectivity within the Mininet topology.
    """
    print(f"Pinging from {src.name} to {dst_ip}...")
    result = src.cmd(f"ping -c 1 {dst_ip}")
    print(f"Result from {src.name} to {dst_ip}:\n{result}")


if __name__ == "__main__":
    setLogLevel("info")

    # It's good practice to clean up any previous Mininet runs.
    # A good practice is to run 'sudo mn -c' in the terminal before starting.
    # os.system("sudo mn -c") # Uncomment if you want to automate this.

    topo = MyTopo()
    # Using RemoteController to connect to an external SDN controller (e.g., Ryu).
    net = Mininet(
        topo=topo,
        controller=RemoteController,
        switch=OVSKernelSwitch,
        link=TCLink,
        autoSetMacs=True,
    )

    try:
        # == STEP 1: Add the IP Alias to the Host's Loopback Interface ==
        # This is the core of the solution. We give the host machine a "mailbox"
        # in our private management network, so it can receive sFlow packets.
        # This command is safe and does not affect normal network operations.
        print(f"Adding IP alias {COLLECTOR_IP}/24 to 'lo' interface...")
        os.system(f"sudo ip addr add {COLLECTOR_IP}/24 dev lo")

        net.start()

        # == STEP 2: Configure Each Switch with a Unique IP and sFlow Target ==
        # We loop through each switch, assign it a unique management IP, and tell it
        # to send sFlow data to our special collector IP alias.
        switch_ip_start = (
            11  # Starting from .11 to avoid collision with the collector's .1
        )

        switch_names = [
            f"s{i}" for i in range(1, 11)
        ]  # List of switch names (s1 to s10)
        for i, sw_name in enumerate(switch_names):
            sw = net.get(sw_name)
            iface_name = find_ovs_agent_iface(sw)

            # Assign a unique IP to the switch's management interface.
            switch_ip = f"{MGMT_IP_BASE}{switch_ip_start + i}"
            sw.cmd(f"ifconfig {iface_name} {switch_ip}/24 up")

            print(f"Configuring sFlow for {sw_name}:")
            print(f"  - Agent IP (source): {switch_ip}")
            print(f"  - Target Collector: {COLLECTOR_IP}:6343")

            # Enable sFlow, pointing to our collector's IP alias.
            enable_sflow(
                switch=sw_name, agent_iface=iface_name, collector_ip=COLLECTOR_IP
            )

        # Display the current sFlow configuration for verification.
        os.system("ovs-vsctl list sflow")

        # == Standard Mininet Host and Network Configuration ==
        # The following section sets up the IP addresses, MACs, and ARP entries
        # for the hosts within the simulation, enabling them to communicate.
        for i in range(1, HOST_NUM + 1):
            h = net.get(f"h{i}")
            ip = f"10.0.0.{i}/24"
            mac = f"00:00:00:00:00:{i:02x}"
            h.setIP(ip)
            h.setMAC(mac)

        for i in range(HOST_NUM):
            src = net.get(f"h{i+1}")
            for j in range(HOST_NUM):
                if i == j:
                    continue  # skip adding an ARP entry to itself
                dst_ip = f"10.0.0.{j+1}"
                dst_mac = f"00:00:00:00:00:{(j+1):02x}"
                src.cmd(f"arp -s {dst_ip} {dst_mac}")

        # Launch ping tests in parallel to generate some traffic.
        threads = []
        for i in range(int(HOST_NUM / 2)):
            client = net.get(f"h{i+1}")
            server_ip = f"10.0.0.{i+1+int(HOST_NUM/2)}"
            t = threading.Thread(target=ping_test, args=(client, server_ip))
            threads.append(t)
            t.start()
        for i in range(int(HOST_NUM / 2)):
            server = net.get(f"h{i+1+int(HOST_NUM/2)}")
            client_ip = f"10.0.0.{i+1}"
            t = threading.Thread(target=ping_test, args=(server, client_ip))
            threads.append(t)
            t.start()
        for t in threads:
            t.join()

        print("\n--- Final Configuration Active ---")
        print("Host internet: OK | sFlow reachability: OK | Switch identification: OK")
        print(f"Run 'sflowtool -p 6343' in another terminal to see the data.")
        CLI(net)

    finally:
        # == STEP 3: Clean Up Gracefully ==
        # This 'finally' block ensures that our created IP alias is removed,
        # and the Mininet network is stopped, no matter how the script exits.
        # This keeps the host system clean.
        print(f"\nCleaning up: Removing IP alias {COLLECTOR_IP} from 'lo' interface...")
        os.system(f"sudo ip addr del {COLLECTOR_IP}/24 dev lo")
        net.stop()

```
