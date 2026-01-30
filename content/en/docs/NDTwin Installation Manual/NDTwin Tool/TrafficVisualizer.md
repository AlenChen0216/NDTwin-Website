---
title: Network Traffic Visualizer
description: >
date: 
weight: 5
---


# Network Traffic Visualizer Installation Guide

## 1. System Requirements

- OS: Windows, macOS, or Linux
- Java: JDK 21
- Build tool: Apache Maven 3.6+ (3.13+ recommended)
- Network: Reachable NDT API endpoint

## 2. Project Structure

```
Network-Traffic-Visualizer/
├── src/
│   ├── main/
│   │   ├── java/org/example/demo2/
│   │   └── resources/org/example/demo2/
├── images/
├── pom.xml
├── mvnw
├── network_traffic_visualizer.sh
└── settings.json
```

## 3. Compilation & Installation

### 3.0 Get the Source Code

Clone the project from GitHub:

```bash
git clone https://github.com/ndtwin-lab/Network-Traffic-Visualizer.git
cd Network-Traffic-Visualizer
```
### 3.1 Verify Environment

Ensure Java and Maven are correctly installed:

```bash
java -version  # Must be Java 21
mvn -version   # Must be Maven 3.6+
```

### 3.2 Build the Project

Navigate to the project root and run:

```bash
# Clean and compile
mvn clean compile

# Or build a full package
mvn clean package
```

## 4. Configuration

The app reads the API endpoint from `NDT_API_URL`.

Default:

```
http://localhost:8000
```

Set a custom API URL:

```bash
export NDT_API_URL="http://your-server:8000"
```

## 5. Run

If the script is not executable:

```bash
chmod +x ./network_traffic_visualizer.sh
```

Start the app:

```bash
./network_traffic_visualizer.sh
```

## 6. Troubleshooting

- **API connection errors:** Check `NDT_API_URL` and network access.
- **JavaFX errors:** Ensure JDK 21 is installed and Maven dependencies are downloaded.

## 7. Ubuntu Desktop App Shortcut

You can create a desktop launcher so the app opens with a single click.

### 7.1 Create a Launcher

Create a `.desktop` file:

```bash
mkdir -p ~/.local/share/applications
cat > ~/.local/share/applications/network-traffic-visualizer.desktop <<'EOF'
[Desktop Entry]
Type=Application
Name=Network Traffic Visualizer
Exec=/bin/bash -lc "cd /path/to/Network Traffic Visualizer && ./network_traffic_visualizer.sh"
Icon=/path/to/NDTwin_traffic_animation_v3.0.2/images/NDTwin.jpg
Terminal=false
Categories=Network;Development;
EOF
```

Replace `/path/to/Network Traffic Visualizer` with your actual project path.

Make it executable:

```bash
chmod +x ~/.local/share/applications/network-traffic-visualizer.desktop
```

### 7.2 Launch

Open the app grid and search for **Network Traffic Visualizer**, then pin it if needed.