---
title: Overview
linkTitle: Overview
description: Learn how NDTwin facilitates OpenFlow/SDN network simulation and development.
weight: 1
---

{{% pageinfo %}}
**NDTwin** is a digital twin platform designed specifically for **SDN (Software-Defined Networking)**. It utilizes the OpenFlow protocol and sFlow technology to enable real-time monitoring and simulation of network behavior.
{{% /pageinfo %}}

Debugging in traditional OpenFlow development workflows is often a challenge. Developers struggle to visualize how flow entries installed by the controller actually affect packet forwarding in real-time. NDTwin provides a high-fidelity, visualized environment that allows you to verify Control Plane logic instantly.

## What is NDTwin?

NDTwin (Network Digital Twin) is a comprehensive development platform that integrates **OpenFlow switches**, the **Ryu Controller**, and **sFlow monitoring**.

Its core objective is to solve the "visibility" problem in SDN development. Once the Ryu controller deploys rules, NDTwin utilizes sFlow sampling to report link status in real-time, transforming complex network topology data and flow table behaviors into intuitive graphical insights.

## Why use NDTwin?

If your team is developing OpenFlow-based network applications (e.g., dynamic routing, load balancing, or energy-saving mechanisms), NDTwin offers the following advantages:

### ✅ Key Benefits
- **Native OpenFlow Support**: Fully compatible with OpenFlow 1.0/1.3 standards. Seamlessly integrates with standard SDN controllers like Ryu or ONOS.
- **Real-time Flow Visualization**: Say goodbye to parsing text output from `ovs-ofctl dump-flows`. We provide a graphical interface to display Flow Rules and packet match counters instantly.
- **Precise Traffic Monitoring**: Integrated sFlow technology provides accurate 5-tuple sampling and real-time bandwidth utilization analysis for every link.
- **Dynamic Topology Awareness**: The system automatically detects Link Failures or new switch connections, triggering alerts to help verify rerouting algorithms.

### 🎯 Use Cases
- **SDN Algorithm Verification**: Validate if your new routing algorithms or QoS policies work as expected.
- **Network Education**: Help students visualize and understand the OpenFlow Match-Action mechanism.
- **Energy-Saving Applications**: Simulate network behavior and connectivity when specific switch ports are powered down.

## System Architecture

<div class="alert alert-light border">
    <img src="/images/NdtArcht.png" class="img-fluid d-block mx-auto" alt="NDTwin Architecture" style="max-height: 400px;">
    <p class="text-center text-muted mt-2"><small>Figure 1: NDTwin System Architecture (OpenFlow & sFlow)</small></p>
</div>

## Where should I go next?

Ready to get started? Check out the following resources:

- [**System Architecture**](/docs/architecture/): Dive deep into the three-layer architecture of NDTwin.
- [**Getting Started**](/docs/getting-started/): Set up your first OpenFlow experimental environment in minutes.
- [**Examples**](/docs/examples/): Browse our sample code for Ryu controller integration.