---
title: Network Traffic Visualizer
description: >
date: 
weight: 5
---


### 1. System Requirements

#### 1.1 Development Environment

* **Operating System:** Cross-platform (Windows, macOS, Linux)
* **Java Runtime:** JDK 21 (OpenJDK or Oracle JDK)
* **Build Tool:** Apache Maven 3.13.0 or higher
* **Network:** Active internet/network connection to access the NDT API.

#### 1.2 Technology Stack

* **Language:** Java 21
* **UI Framework:** JavaFX 21.0.2
* **Dependencies:**
* `org.openjfx` (Controls, FXML)
* `com.google.code.gson` / `jackson-databind` (JSON Processing)
* `org.apache.httpcomponents` (HTTP Client)
* `org.controlsfx` (UI Enhancements)



### 2. Project Structure

The project follows a standard Maven directory structure:

```
NDTwin real-time animation gui/
├── src/
│   ├── main/
│   │   ├── java/org/example/demo2/     # Java Source Code
│   │   └── resources/org/example/demo2/ # FXML & Assets
├── images/                             # Image Resources
├── pom.xml                             # Maven Configuration
├── start.sh                            # Startup Script
└── README_ENV.md                       # Environment Documentation

```

### 3. Compilation & Installation

#### 3.1 Verify Environment

Ensure Java and Maven are correctly installed:

```bash
java -version  # Must be Java 21
mvn -version   # Must be Maven 3.6+

```

#### 3.2 Build the Project

Navigate to the project root and run:

```bash
# Clean and compile
mvn clean compile

# Or build a full package
mvn clean package

```

### 4. Configuration & Execution

#### 4.1 Environment Variables

The application relies on the `NDT_API_URL` to fetch data.

* **Default:** `http://localhost:8000` (if not set).
* **Setting the variable:**
```bash
export NDT_API_URL="http://your-server:8000"

```



#### 4.2 Running the Application

You can run the application using the provided shell script or via Maven.

**Method 1: Using the Startup Script**

```bash
# Run with default settings
./run_debug.sh

# Run with a custom API URL
NDT_API_URL="http://192.168.1.100:8000" ./start.sh

```

### 5. Developer Reference: Core Architecture

#### 5.1 Key Class Map

| Feature | Primary Class | Location |
| --- | --- | --- |
| **Main Entry** | `NetworkTopologyApp.java` | `src/main/java/org/example/demo2/` |
| **Drawing Core** | `TopologyCanvas.java` | Handles drawing nodes, links, and flows. |
| **Data Fetching** | `NDTApiClient.java` | Fetches `GraphData` and `DetectedFlowData`. |
| **Flow Control** | `FlowFilter.java` | Manages flow visibility checkboxes. |
| **Dialogs** | `InfoDialog.java` | Handles Node Info and Port Connection windows. |

#### 5.2 Data Flow

1. **API Client:** `NDTApiClient` fetches JSON data.
2. **App Logic:** `NetworkTopologyApp` converts API data into internal models (`Node`, `Link`, `Flow`).
3. **Rendering:** `TopologyCanvas` updates the visual elements at 60 FPS (or triggered events).

#### 5.3 Maintenance Guide

* **Adding Dependencies:** Update `pom.xml`.
* **API Changes:** If the JSON response format changes, update the data models in `NDTApiClient.java` and `GraphData.java`.
* **Styling:** UI styles are defined in `dialog.css`.

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