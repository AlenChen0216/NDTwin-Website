---
title: Network Traffic Visualizer
description: >
date: 
weight: 5
---
Based on the first part of your provided document (the JavaFX Desktop Application), here is the **User Guide for Deployment and Startup** in English.

### NDTwin Desktop GUI Deployment & Startup Guide

**Version:** NDTwin Visualization (JavaFX)
**Prerequisites:** Java 21 (JDK), Maven 3.6+

---

#### 1. Environment Setup

Before running the desktop application, ensure your system meets the requirements.

**Step 1: Verify Java Version**
Open your terminal and check if Java 21 is installed.

```bash
java -version

```

*Output should indicate version 21.*

**Step 2: Verify Maven Version**
Check if Maven is installed for building the project.

```bash
mvn -version

```

---

#### 2. Compilation (Building the App)

You need to compile the source code into an executable format.

**Step 1: Navigate to Project Directory**

```bash
cd "NDTwin real-time animation gui"

```

**Step 2: Clean and Compile**
Run the following Maven command to clean old builds and compile the new one:

```bash
mvn clean compile

```

**Step 3: Package (Optional but Recommended)**
To create a full executable JAR file:

```bash
mvn clean package

```

---

#### 3. Execution (Running the App)

There are two ways to start the application.

**Method 1: Using the Startup Script (Recommended)**
The provided script handles environment variables automatically.

1. **Grant permission (if needed):**
```bash
chmod +x start.sh

```


2. **Run with Default Settings:**
```bash
./run_debug.sh

```


3. **Run with Custom API URL:**
If your NDT API server is not on localhost, specify the URL:
```bash
NDT_API_URL="http://your-server-ip:8000" ./start.sh

```



**Method 2: Manual Configuration**
If you are running it manually or via an IDE, you may need to set the environment variable first.

```bash
export NDT_API_URL="http://your-server-ip:8000"
# Then run via Maven or Java command

```

*Note: The default URL is `http://localhost:8000` if not specified.*

---

#### 4. Troubleshooting

* **Network Error:** Ensure your computer can reach the NDT API server.
* **JavaFX Error:** If you see "missing JavaFX runtime components," ensure you are using a JDK that includes JavaFX, or that `pom.xml` dependencies are correctly downloaded by Maven.