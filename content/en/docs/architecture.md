---
title: System Architecture
linkTitle: System Architecture
weight: 2
description: >
  Explain the architecture, applications, tools, kernel, and network of NDTwin
---

{{% pageinfo %}}
The NDTwin architecture comprises four components: applications, tools, kernel, and network, respectively.
{{% /pageinfo %}}

<div class="text-center">
  <img src="/images/NdtArcht.png" class="img-fluid" alt="NDTwin Architecture Diagram" style="max-width: 90%; margin: 2rem 0;">
</div>

## Applications

The application component consists of various applications developed on the NDTwin platform. An NDTwin application is developed for achieving a specific optimization goal such as load-balancing or anomaly detection. Currently, NDTwin provides the **Traffic-engineering App** and **Energy-saving App**. More applications will be added by the NDTwin development team in the future. Additionally, anyone can develop his/her own applications by using the NDTwin open source project.

Each NDTwin application is a separate program running independently, and it communicates with the NDTwin kernel via **RESTful APIs** to get services from the kernel or request the kernel to control specified network switches. An NDTwin application can provide its RESTful APIs to the kernel. Doing so enables the kernel to asynchronously send notifications, data, or requests to it. 

This RESTful APIs-based design provides many advantages as follows:

* **Easy Integration**: NDTwin applications can be independently developed and written in different programming languages. They can be implemented as single-threaded or multi-threaded programs to best fit their needs.  
* **High Performance**: Since each NDTwin application runs as an independent process, multiple NDTwin applications can run simultaneously over the multiple CPU cores of a server to increase their aggregate processing throughput. For those NDTwin applications that are CPU-bound, they can simultaneously run on different servers to further boost their aggregate processing throughput.  
* **Fault Isolation**: Since each NDTwin application runs as a different process, a faulty or buggy NDTwin application will not crash, block, or slow down the operations of the NDTwin kernel or other NDTwin applications.

 ## Tools 

Like an NDTwin application, an NDTwin tool is a process that uses RESTful APIs to communicate with the kernel bidirectionally. While the goal of an NDTwin application is to optimally control the network, the goal of an NDTwin tool is to support the NDTwin kernel or the NDTwin user. Similar to NDTwin applications, NDTwin tools can all run on a single server or separately run on different servers to achieve more computational and storage resources.    

The tools that are currently included in NDTwin are listed as follows:

* **Web GUI**: This tool provides Web-based GUI to the NDTwin user. The NDTwin user can use any web browser to run up this tool to easily view the real-time states of the network and flows or control the network in real time. This tool supports an LLM-powered intent-based network management interface.    
* **Network Traffic Visualizer**: This tool has a GUI by which the NDTwin user can "see" how the packets of flows are traversing over the network in real time.  
* **Network State Recorder**: This tool, once enabled, will continuously record the states of network switches and flows into files. The Web GUI tool and the network traffic visualizer can open these files for non-real-time checks and playback, respectively. 
* **Network Traffic Generator**: This tool set can be used to automatically generate many flows and launch the sending and receiving programs of these flows on the hosts of the network without human efforts. It is very useful to test the functions and evaluate the performance and effectiveness of an NDTwin application under development.     
* **Simulation Platform Server**: This server is a "super" server that will launch a pre-installed simulator program each time when it receives a simulation request that is issued by an NDTwin application and forwarded to it by the kernel. Because each received simulation request is executed by a launched simulator process, multiple simulations can run in parallel over multiple CPU cores to quickly find the best simulation result. 
* **On-line LLM (e.g., ChatGPT)**: This tool refers to an on-line Large Language Model (LLM) website. The Web GUI tool supports intent-based network management by intelligently prompting the on-line LLM (e.g., ChatGPT). 
* **Local LLM (fine-tuning)**: Because using the services provided by an on-line LLM website incurs token costs and add delays to the prompting, we are trying to fine-tune an open source LLM model to solve these problems.  

## Kernel  

The kernel is the heart of NDTwin. It is implemented as a high-performance C++ program that runs as a multi-threaded process for achieving high throughput and low  latency. 

The kernel acts as the digital twin of the network and thus has real-time states of network switches and flows. It provides a set of RESTful APIs by which any other process (e.g., an NDTwin application or an NDTwin tool) can get real-time information from the kernel or request the kernel to control specific network switches. The kernel can also call the RESTful APIs provided by an NDTwin application (or an NDTwin tool) to actively notify it of a specific situation or asynchronously transfer data/results to it.

  
The core modules that are currently included in the kernel are explained as follows:

* **Flow Information and Link Bandwidth Usage Collector**:  This module acts as a high-speed telemetry engine using the sFlow standard. It runs a non-blocking UDP listener to process two types of sFlow samples: (1) Flow Data: It extracts 5-tuple information (Src/Dst IP, Src/Dst Port, Protocol) in sampled packet headers to detect flows and calculate their current sending rates. (2) Link Data: It uses link counter samples to calculate real-time link bandwidth usage and the unused bandwidth on a link.
* **Topology and Flow Monitor**: This module maintains an in-memory graph representation of the network. Whenever the SDN controller detects topology changes (e.g., Link Recovery/Failure or Switch Join/Leave), it will call the kernel API to notify this module. With these immediate notifications, this module keeps the in-memory network topology up to date at any time.
* **Flow Routing Manager**: This module utilizes SDN capabilities to manage routing entries on OpenFlow switches. It allows for dynamic installation, modification, and deletion of routing rules to support traffic rerouting scenarios based on the decisions made by NDTwin applications. 
* **Device Configuration and Power Manager**: This module manages the physical states of network switches beyond standard OpenFlow capabilities: (1) For Configuration: This module retrieves switch configuration information via SNMP and SSH, (2) For Power Control: This module controls switch power status (On/Off) using APIs from intelligent smart plugs, enabling energy-saving applications.
* **Application Registration and Coordination Manager**: Each NDTwin application process should register with this module to get its unique run-time ID. This ID can be included in the simulation requests issued by an NDTwin application to the kernel. Based on this ID, the kernel can asynchronously send back the simulation replies to the correct NDTwin application process. The Simulation Platform Server also uses this ID to create a separate folder in which all simulation input and output files associated with a specific NDTwin application process are stored. By providing lock APIs, this module coordinates the actions among different NDTwin applications to prevent conflicting network policies from being executed (e.g., one NDTwin application tries to power off a switch to save energy consumption while another NDTwin application is using the same switch for traffic engineering). 
* **Simulation Request and Reply Manager**: This module forwards the simulation requests issued by NDTwin applications to the Simulation Platform Server for execution. When the Simulation Platform Server completes a simulation and sends the simulation reply to the kernel, this module will forward the reply to the NDTwin application that is expecting this reply.
* **Controller and Other Events Handler**: This module uses asynchronous I/O mechanism to efficiently handle all events from the SDN controller, NDTwin applications, and NDTwin tools. Each event is a RESTful API request issued by one of these external processes and will be processed by a thread that is created on the fly to maximize the processing throughput and minimize the processing latency.
* **Intent to Tasks Translator**: The Web GUI tool supports the intent-based network management interface by which the NDTwin user can use natural language to express their intents to manage the network. This module works with the Web GUI tool and will prompt an on-line LLM (e.g., ChatGPT) to translate the input intents to the corresponding tasks. To perform these translated tasks, this module will call the internal functions of related modules in the kernel.      
* **Data Cache Manager**: This module caches the states of network (e.g., the routing entries on a switch or the network topology) that change infrequently. When NDTwin applications or NDTwin tools issue requests to the kernel to retrieve the data, this module will provide the cached data without issuing commands to network switches to refetch the data. Instead, this module will issue refetch commands to network switches only when the data has changed on the switches. This design enables the kernel to provide the requested data to NDTwin applications and NDTwin tools very quickly and prevents network switches from overloading with frequent requests.       

## Network  

The network represents the target network that is operated and managed by NDTwin:

* **Control Plane**: NDTwin uses an SDN Controller (e.g., Ryu) to control the switches in real time. To be controlled by NDTwin, the switches need to support the OpenFlow protocol. 
* **Data Plane**: NDTwin can correctly operate the following two types of networks:
    * **Emulated Network**: which is constructed by Mininet with Open vSwitch (OVS). 
    * **Physical Network**: which is composed of hardware switches that support OpenFlow (e.g., Brocade, HPE).

## Resources Consideration

Due to the design of NDTwin, all NDTwin application processes, tool processes, the kernel process, and the SDN controller process can run together on a server. This configuration enables the NDTwin user to use just one server to run up NDTwin.

When using NDTwin to operate a very large network with a huge number of flows, if the computational and storage resources of a server are insufficient for NDTwin to perform optimally, the NDTwin applications, tools, kernel, and SDN controller can run simultaneously on different servers if needed.  