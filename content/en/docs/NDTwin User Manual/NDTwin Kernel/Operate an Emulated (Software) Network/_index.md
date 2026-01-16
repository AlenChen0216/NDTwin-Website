---
title: Operate an Emulated (Software) Network
description: >
  Choose your preferred method to set up the Mininet simulation environment.
  We provide both a pre-configured VM for quick testing and a manual installation guide for native Linux systems.
date: 2025-12-24
weight: 1
---

# Mininet Setup Guide

Welcome to the **Mininet Setup** section. Mininet serves as the **Data Plane** for our Network Digital Twin (NDT) system, responsible for simulating the network topology, switches, and hosts.

To accommodate different user needs, we provide two distinct ways to set up this environment. Please choose the guide that best fits your situation:

## Which Guide Should I Choose?

### Option 1: Quick Start (Recommended)
👉 **Go to:** [VM-Linux Based Execution Environment]({{< ref "VM-Linux Based Excution Environment.md" >}})

* **Best for:** Users who want to experience the NDT system immediately without spending time on installation and debugging.
* **What you get:** A pre-configured Ubuntu Virtual Machine image with all environments (Ryu, Mininet, NDT Kernel) ready to run.
* **Prerequisites:** None (just download and run).
* **Pros:** Zero setup time, guaranteed compatibility.

### Option 2: Build from Scratch
👉 **Go to:** [Native-Linux Execution Environment]({{< ref "Native-Linux Excution Environment.md" >}})

* **Best for:** Developers who need to integrate NDT into an existing system, want to modify the source code, or need to understand the underlying dependencies.
* **What you get:** Step-by-step instructions to install Ryu, Mininet, C++ libraries, and Python packages from scratch.
* **Prerequisites:** A machine running Ubuntu (20.04/22.04 recommended).
* **Pros:** Full control over the environment and versions.