---
title: Use the Demo VM for a Quick Start
description: >
  A quick start guide designed for the pre-configured Ubuntu VM image.
  All dependencies (Ryu, Mininet, NDT Kernel) are pre-installed.
  Users can skip installation steps and directly run the system.
date: 2025-12-24
weight: 1
---

# Ubuntu VM Image Installation Manual

## About this Environment

This environment is a pre-configured **Ubuntu** virtual machine designed to help you get started with **NDTwin** immediately. It eliminates the need for manual setup by providing a ready-to-use system with all necessary dependencies pre-installed and configured.

Included in this image:
* **Operating System:** Ubuntu (Pre-configured)
* **Core Components:** NDT Kernel, Ryu SDN Controller, Mininet
* **Dependencies:** All required Python libraries, system tools, and network configurations.

### 1. Download the Image
You can obtain the latest version of the VM image (`.ova` or `.iso`) from our official **Download** page.

[**> Go to Download Page**](/workspaces/NDTwin-Website/content/en/docs/Download/_index.md)

### 2. Prerequisites
To run this image, you will need virtualization software installed on your host machine. We recommend **VMware** for the best compatibility:
* **Windows/Linux:** VMware Workstation Player (Free) or VMware Workstation Pro.
* **macOS:** VMware Fusion.

### 3. Installation Guide (VMware)
Once you have downloaded the image file, follow these brief steps to import it:

1.  **Open VMware:** Launch your VMware Workstation or Fusion application.
2.  **Import the Virtual Machine:**
    * Click on **"Open a Virtual Machine"** (or `File` > `Open`).
    * Locate and select the downloaded `.ova` file.
3.  **Configure Settings:**
    * Choose a name for the new virtual machine and a storage path.
    * Click **Import**.
    * *Note: If prompted about OVF specification compliance, click "Retry" or "Relax" to proceed.*
4.  **Launch:** Once the import is complete, select the new VM from the list and click **"Power on this virtual machine"**.

### 4. Default Login Credentials
After the system boots up, use the following credentials to log in:

| Account Type | Username | Password |
| :--- | :--- | :--- |
| **System User** | `ndtwin` | `ndtwin` |
| **Root (Sudo)** | `root` | `ndtwin` |

> **Note:** For security reasons, we strongly recommend changing these passwords immediately after your first login using the `passwd` command.
---

