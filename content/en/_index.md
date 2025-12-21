---
title: NDTwin
linkTitle: NDTwin
description: Network Digital Twin - Next Generation Network Emulation Platform
menu:
  main:
    weight: 10
---

<section class="row align-items-center pb-5" style="padding-top: 4rem;">
  <div class="col-lg-6 text-left">
    <h1 class="display-4 mb-4" style="font-weight: 700;">
      <span style="font-size: 0.7em; color: #4a4a4a;"> NDTwin Network Digital Twin</span>
    </h1>
    <p class="lead mb-4">
      A novel network digital twin (NDT) open source framework designed for optimally operating and managing a network.<br><br> Its kernel continuously collects real-time network and flow states. Its apps use <strong> simulation and AI/ML technologies </strong> to: 1) evaluate/predict the outcome of many "what-if" conditions, 2) find the optimal solution to the current or a predicted situation, and 3) issue commands to network devices in real time to perform the best solution. Its tools feature a Web GUI that uses Large Language Model (LLM) to support intent-based network management and a real-time network traffic visualizer.<br><br>NDTwin operates correctly and successfully on both physical networks composed of hardware switches and emulated networks formed by Mininet. It can be used as an automatic system to optimize the operation of a production network or as an academic platform to conduct NDT-based research. Developers can use this framework to develop, test, evaluate, and deploy their innovative NDT applications.             
    </p>
    <div class="d-flex flex-wrap">
      <a class="btn btn-lg btn-primary me-3 mb-3" href="/docs/" style="background-color: #6f42c1; border-color: #6f42c1;">
        Get Started <i class="fas fa-arrow-right ms-2"></i>
      </a>
      <a class="btn btn-lg btn-outline-secondary me-3 mb-3" href="https://github.com/joemou/NetworkDigitalTwin">
        Download <i class="fab fa-github ms-2"></i>
      </a>
    </div>
  </div>
  <div class="col-lg-6 text-center">
    <img src="/images/NdtArcht.png" class="img-fluid" alt="Architecture">
  </div>
</section>

{{% blocks/section color="light" type="row" %}}

{{% blocks/feature icon="fa-solid fa-network-wired" title="Network Visualization" %}}
Visualize network status, topology changes, and traffic flows in real-time.
{{% /blocks/feature %}}

{{% blocks/feature icon="fa-solid fa-code" title="OpenFlow Support" %}}
Fully compatible with OpenFlow standard. Seamless integration with Ryu SDN controller.
{{% /blocks/feature %}}

{{% blocks/feature icon="fab fa-github" title="Open Source" url="https://github.com/joemou/NetworkDigitalTwin" %}}
Join our community on GitHub.
{{% /blocks/feature %}}

{{% /blocks/section %}}