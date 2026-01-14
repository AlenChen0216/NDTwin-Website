---
title: Network Traffic Visualizer
description: >
date: 
weight: 5
---
Here is the translation of your report, separated into a **User Manual** and an **Installation & Technical Manual** as requested.

---

## NDTwin Real-Time Animation GUI - User Manual

### 1. Introduction

The NDTwin Real-Time Animation GUI is a visualization tool designed to display network topology, traffic flows, and node status in real-time. It connects to the NDT API to fetch data and renders it using a dynamic JavaFX interface.

### 2. Interface Overview

The main interface consists of two primary areas:

1. **Topology Canvas:** The main display area showing network nodes (switches/hosts), links (cables), and moving traffic animations.
2. **Side Bar / Info Panel:** Displays detailed statistics, control options, and specific information about selected elements.

### 3. Key Features & How to Use

#### 3.1 Network Topology Visualization

* **Real-Time Updates:** The system automatically fetches data from the API every second. Nodes and links will appear or disappear dynamically based on the network state.
* **Fat-Tree Layout:** The application automatically organizes nodes into a Fat-Tree structure (Core, Aggregation, Edge, and Host layers) for better readability.
* **Node Interaction:**
* **Drag & Drop:** You can click and drag any node to a new position. The node will snap to the nearest grid point for alignment.



#### 3.2 View Controls

* **Fit to Window:** Automatically adjusts the zoom and pan levels so that the entire network topology fits within the current window size.
* **Reset Zoom:** Reverts the view to the default zoom level (100%).
* **Dark Mode:** Toggles the interface theme between Light and Dark modes to suit your lighting environment.
* *[Reference Image: Dark Mode Screenshot]*



#### 3.3 Flow Management (Flow Filter)

The Flow Filter is a powerful control system for managing network traffic visibility.

* **Visibility Control:** You can check or uncheck specific flows in the sidebar to show or hide them on the canvas.
* **Usage:** Use this to declutter the screen when analyzing specific traffic paths.
* *[Reference Image: Flow Filter Screenshot]*



#### 3.4 Path Flicker (Traffic Tracing)

* **Function:** Visualizes the specific path of a network flow by "flickering" or highlighting the links involved in that transmission.
* **Usage:** This helps in troubleshooting routing issues by visually distinguishing one flow's path from the rest of the network traffic.
* *[Reference Image: Path Flicker Screenshot]*



#### 3.5 Detailed Information & Port Connections

You can access detailed information by interacting with elements:

* **Node Information:** Click on a node to open the `InfoDialog`. This displays the node's name, type, and status.
* **Port Connections Window:**
* Inside the Node Info dialog, click the **"Ports"** button.
* This opens a separate window listing all connections for that node, formatted as `Source (Port) -> Destination` and `Destination (Port) <- Source`.
* *[Reference Image: Port Connections Screenshot]*



