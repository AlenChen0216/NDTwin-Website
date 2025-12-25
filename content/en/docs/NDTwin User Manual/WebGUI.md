---
title: Web GUI
description: >
date: 
weight: 6
---

### NDTwin GUI Deployment & Startup Guide

**Version:** NDTwin GUI
**Prerequisites:** Ubuntu, Docker, Docker Compose

---

#### 1. Environment Setup

Before running the GUI, you must install Docker and Docker Compose.

**Step 1: Install Docker and Dependencies**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```

**Step 2: Configure Permissions**
Add your current user to the docker group so you don't need to use `sudo` for every docker command.

```bash
sudo groupadd docker
sudo usermod -aG docker $USER

```

*Note: Please logout and login again for the changes to take effect.*

**Step 3: Install Standalone Docker Compose (If needed)**

```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

```

**Step 4: Start Docker Service**

```bash
sudo systemctl start docker

```

---

#### 2. Configuration & Deployment

**Step 1: Unzip the Project**
Unzip the provided package and enter the directory.

```bash
unzip NDT_GUI_v4.0.0.zip
cd NDT

```

**Step 2: Configure API Connection**

You must point the GUI to your running backend API (Mininet or Physical Testbed).

1. Open the `.env` file in the root directory.
2. Modify the `VITE_NDT_API_BASE_URL` variable to match your API server's IP address.

*Example (if Mininet is running on 192.168.10.189):*

```env
VITE_NDT_API_BASE_URL=http://192.168.10.189:8000

```

**Step 3: Run the Deployment Script**
This script will build the Docker images and start the containers.

```bash
sudo chmod 755 deploy.sh
./deploy.sh

```

---

#### 3. Accessing the Application

Once deployment is complete, the GUI will be running on your local machine.

* **URL:** `http://localhost:3000`
* **Default Username:** `admin`
* **Default Password:** `admin1234`

---

#### 4. Managing the Server

**To Stop the Server:**

```bash
docker-compose down

```

**To Restart the Server (No Rebuild):**
If you haven't changed the IP configuration, simply run:

```bash
docker-compose up -d

```

**To Reconfigure API IP (Rebuild Required):**
If you modify the `VITE_NDT_API_BASE_URL` in the `.env` file (e.g., switching from Mininet to Testbed), you must rebuild the frontend:

```bash
docker-compose build frontend
docker-compose up -d

```