---
title: "NDTwin - Network Digital Twin Framework"
linkTitle: "NDTwin"
description: "NDTwin is an open source Network Digital Twin framework designed for optimally operating and managing a network."
menu: {main: {weight: 10}}
---

{{< rawhtml >}}

<section class="w-100 d-flex flex-column" style="background-color: #2D75B2; min-height: 100vh; padding-top: 80px; padding-bottom: 50px;">
    
    <div class="container my-auto">
        <div class="row align-items-center">
            
            <div class="col-lg-6 text-left text-white px-4 px-lg-0">
                
                <h1 class="display-4 font-weight-bold mb-3">
                    Network Digital Twin
                </h1>
                
                <p class="lead mb-4" style="font-weight: 400; opacity: 1; line-height: 1.6;">
                    <span style="color: #ED7D31; font-weight: 800; font-size: 1.1em;">NDTwin</span>
                    is a novel network digital twin (NDT) open source framework designed for optimally operating and managing a network.
                </p>

                <div class="mb-2" style="font-size: 1rem; line-height: 1.6; opacity: 0.95; font-weight: 300;">
                    <ul class="list-unstyled">
                        <li class="mb-3 d-flex align-items-start">
                            <div class="mt-1 me-3 flex-shrink-0" style="width: 24px; text-align: center;">
                                <i class="fas fa-microchip" style="color: #ED7D31;"></i>
                            </div>
                            <span class="flex-grow-1">
                                Its Kernel continuously collects real-time network, device, and flow states.
                            </span>
                        </li>
                        <li class="mb-3 d-flex align-items-start">
                            <div class="mt-1 me-3 flex-shrink-0" style="width: 24px; text-align: center;">
                                <i class="fas fa-brain" style="color: #ED7D31;"></i>
                            </div>
                            <span class="flex-grow-1">
                                Its Apps use simulation and AI/ML technologies to: 1) evaluate/predict the outcome of many "what-if" conditions, 2) find the optimal solution to the current or a predicted situation, and 3) issue commands to network devices in real time to perform the best solution.
                            </span>
                        </li>
                        <li class="mb-3 d-flex align-items-start">
                            <div class="mt-1 me-3 flex-shrink-0" style="width: 24px; text-align: center;">
                                <i class="fas fa-desktop" style="color: #ED7D31;"></i>
                            </div>
                            <span class="flex-grow-1">
                                Its Tools feature a Web GUI that uses Large Language Model (LLM) to support intent-based network management and a real-time network traffic visualizer.
                            </span>
                        </li>
                    </ul>
                </div>

                <div class="my-3"></div>

                <div class="mb-5" style="font-size: 1rem; line-height: 1.6; opacity: 0.95; font-weight: 300;">
                    <ul class="list-unstyled">
                        <li class="mb-3 d-flex align-items-start">
                            <div class="mt-1 me-3 flex-shrink-0" style="width: 24px; text-align: center;">
                                <i class="fas fa-network-wired" style="color: #ED7D31;"></i>
                            </div>
                            <span class="flex-grow-1">
                                NDTwin operates correctly and successfully on both physical networks composed of hardware switches and emulated networks formed by Mininet.
                            </span>
                        </li>
                        <li class="mb-3 d-flex align-items-start">
                            <div class="mt-1 me-3 flex-shrink-0" style="width: 24px; text-align: center;">
                                <i class="fas fa-cogs" style="color: #ED7D31;"></i>
                            </div>
                            <span class="flex-grow-1">
                                It can be used as an automatic system to optimize the operation of a production network or as an academic platform to conduct NDT-based research.
                            </span>
                        </li>
                        <li class="mb-3 d-flex align-items-start">
                            <div class="mt-1 me-3 flex-shrink-0" style="width: 24px; text-align: center;">
                                <i class="fas fa-laptop-code" style="color: #ED7D31;"></i>
                            </div>
                            <span class="flex-grow-1">
                                Developers can use this framework to develop, test, evaluate, and deploy their innovative NDT applications.
                            </span>
                        </li>
                    </ul>
                </div>

                <div class="d-flex flex-wrap">
                    <a class="btn btn-lg me-3 mb-3 shadow" href="/docs/" style="background-color: #ED7D31; color: #fff; font-weight: 800; padding: 0.8rem 2rem; border-radius: 50px;">
                        Get Started <i class="fas fa-arrow-right ms-2"></i>
                    </a>
                    <a class="btn btn-lg btn-outline-light me-3 mb-3" href="https://github.com/joemou/NetworkDigitalTwin" style="border-width: 2px; font-weight: bold; padding: 0.8rem 2rem; border-radius: 50px;">
                        Download <i class="fab fa-github ms-2"></i>
                    </a>
                </div>
            </div>
            
            <div class="col-lg-6 text-center mt-5 mt-lg-0 px-4 px-lg-0">
                <div class="bg-white p-2 rounded shadow-lg">
                    <a href="#" data-bs-toggle="modal" data-bs-target="#imageModal" title="Click to enlarge">
                        <img src="images/NdtArcht.png" class="img-fluid rounded" alt="NDTwin Architecture Diagram" style="cursor: pointer;">
                    </a>
                </div>
            </div>
        </div>
    </div>
</section>

<section class="w-100 py-5 bg-white">
    <div class="container mt-4 mb-4">
        <div class="text-center mb-5">
            <h2 class="font-weight-bold display-5" style="color: #2D75B2;">How NDTwin Works</h2>
            <div style="width: 60px; height: 4px; background: #ED7D31; margin: 1rem auto;"></div>
        </div>
        <div class="row">
            <div class="col-md-6 mb-4 px-lg-5 text-center">
                <h4 class="font-weight-bold mb-3"><i class="fas fa-microchip mr-2" style="color: #2D75B2;"></i> The Kernel</h4>
                <p class="text-muted" style="font-size: 1.1rem; line-height: 1.8;">
                    Its Kernel continuously collects real-time network, device, and flow states. It acts as the brain, ensuring synchronization between the physical network and the digital twin.
                </p>
            </div>
            <div class="col-md-6 mb-4 px-lg-5 text-center">
                <h4 class="font-weight-bold mb-3"><i class="fas fa-robot mr-2" style="color: #2D75B2;"></i> AI & Apps</h4>
                <p class="text-muted" style="font-size: 1.1rem; line-height: 1.8;">
                    Supports advanced AI/ML algorithms and LLM integration for traffic prediction, anomaly detection, and automated network optimization.
                </p>
            </div>
        </div>
    </div>
</section>

<section class="w-100 py-5" style="background-color: #f8f9fa; border-top: 1px solid #e9ecef;">
    <div class="container py-4">
        <div class="row text-center">
            <div class="col-md-4 mb-5">
                <div class="mb-3 p-3 d-inline-block rounded-circle bg-white shadow-sm">
                    <i class="fas fa-project-diagram fa-2x" style="color: #2D75B2;"></i>
                </div>
                <h3 class="h4 font-weight-bold mb-3 mt-2">Digital Twin Powered</h3>
                <p class="text-muted px-3">
                    Employ digital twin technologies to optimize a production network.
                </p>
            </div>

            <div class="col-md-4 mb-5">
                <div class="mb-3 p-3 d-inline-block rounded-circle bg-white shadow-sm">
                    <i class="fas fa-code fa-2x" style="color: #2D75B2;"></i>
                </div>
                <h3 class="h4 font-weight-bold mb-3 mt-2">OpenFlow Support</h3>
                <p class="text-muted px-3">
                    Compatible with OpenFlow standard. Seamless integration with Ryu SDN controller.
                </p>
            </div>

            <div class="col-md-4 mb-5">
                <div class="mb-3 p-3 d-inline-block rounded-circle bg-white shadow-sm">
                    <i class="fab fa-github fa-2x" style="color: #2D75B2;"></i>
                </div>
                <h3 class="h4 font-weight-bold mb-3 mt-2">Open Source</h3>
                <p class="text-muted px-3">
                    Join our community on GitHub and contribute.
                </p>
                <a href="https://github.com/joemou/NetworkDigitalTwin" class="font-weight-bold" style="color: #2D75B2; text-decoration: none;">
                    Read more <i class="fas fa-arrow-right small ms-1"></i>
                </a>
            </div>
        </div>
    </div>
</section>

<div class="modal fade" id="imageModal" tabindex="-1" aria-labelledby="imageModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered modal-xl">
    <div class="modal-content bg-transparent border-0" style="box-shadow: none;">
      <div class="modal-body p-0 position-relative text-center">
        <button type="button" class="btn-close btn-close-white position-absolute top-0 end-0 m-3" data-bs-dismiss="modal" aria-label="Close" style="z-index: 1051; filter: drop-shadow(0px 0px 2px black);"></button>
        <img src="images/NdtArcht.png" class="img-fluid rounded shadow-lg" alt="High Resolution NDTwin Architecture Diagram" style="max-height: 90vh; object-fit: contain;">
      </div>
    </div>
  </div>
</div>

{{< /rawhtml >}}