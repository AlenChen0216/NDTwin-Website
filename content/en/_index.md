---
title: "NDTwin"
linkTitle: "NDTwin"
menu: {main: {weight: 10}}
---

{{< rawhtml >}}

<section class="w-100 p-0 m-0 d-flex align-items-center justify-content-center" style="background-color: #2E74B5; min-height: 90vh;">
    <div class="container py-5">
        <div class="row align-items-center">
            <div class="col-lg-6 text-left text-white">
                <h1 class="display-4 font-weight-bold mb-4">
                    Network Digital Twin <br>
                </h1>
                <div class="mb-5" style="font-size: 1.05rem; line-height: 1.7; opacity: 0.95; font-weight: 300;">
                    A novel network digital twin (NDT) open source framework designed for optimally operating and managing a network.<br><br> 
                    
                    Its Kernel continuously collects real-time network, device, and flow states. Its Apps use simulation and AI/ML technologies to: 1) evaluate/predict the outcome of many "what-if" conditions, 2) find the optimal solution to the current or a predicted situation, and 3) issue commands to network devices in real time to perform the best solution. Its Tools feature a Web GUI that uses Large Language Model (LLM) to support intent-based network management and a real-time network traffic visualizer.<br><br>
                    
                    NDTwin operates correctly and successfully on both physical networks composed of hardware switches and emulated networks formed by Mininet. It can be used as an automatic system to optimize the operation of a production network or as an academic platform to conduct NDT-based research. Developers can use this framework to develop, test, evaluate, and deploy their innovative NDT applications
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
            <div class="col-lg-6 text-center mt-5 mt-lg-0">
                <div class="bg-white p-2 rounded shadow-lg" style="transform: perspective(1000px) rotateY(-5deg) rotateX(2deg); transition: transform 0.3s;">
                    <img src="images/NdtArcht.png" class="img-fluid rounded" alt="NDTwin Architecture">
                </div>
            </div>
        </div>
    </div>
</section>

<section class="w-100 py-5 bg-white">
    <div class="container mt-4 mb-4">
        <div class="text-center mb-5">
            <h2 class="font-weight-bold display-5" style="color: #2E74B5;">How NDTwin Works</h2>
            <div style="width: 60px; height: 4px; background: #ED7D31; margin: 1rem auto;"></div>
        </div>
        <div class="row">
            <div class="col-md-6 mb-4 px-lg-5 text-center">
                <h4 class="font-weight-bold mb-3"><i class="fas fa-microchip mr-2" style="color: #2E74B5;"></i> The Kernel</h4>
                <p class="text-muted" style="font-size: 1.1rem; line-height: 1.8;">
                    Its Kernel continuously collects real-time network, device, and flow states. It acts as the brain, ensuring synchronization between the physical network and the digital twin.
                </p>
            </div>
            <div class="col-md-6 mb-4 px-lg-5 text-center">
                <h4 class="font-weight-bold mb-3"><i class="fas fa-robot mr-2" style="color: #2E74B5;"></i> AI & Apps</h4>
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
                    <i class="fas fa-project-diagram fa-2x" style="color: #2E74B5;"></i>
                </div>
                <h3 class="h4 font-weight-bold mb-3 mt-2">Digital Twin Powered</h3>
                <p class="text-muted px-3">
                    Employ digital twin technologies to optimize a production network.
                </p>
            </div>

            <div class="col-md-4 mb-5">
                <div class="mb-3 p-3 d-inline-block rounded-circle bg-white shadow-sm">
                    <i class="fas fa-code fa-2x" style="color: #2E74B5;"></i>
                </div>
                <h3 class="h4 font-weight-bold mb-3 mt-2">OpenFlow Support</h3>
                <p class="text-muted px-3">
                    Compatible with OpenFlow standard. Seamless integration with Ryu SDN controller.
                </p>
            </div>

            <div class="col-md-4 mb-5">
                <div class="mb-3 p-3 d-inline-block rounded-circle bg-white shadow-sm">
                    <i class="fab fa-github fa-2x" style="color: #2E74B5;"></i>
                </div>
                <h3 class="h4 font-weight-bold mb-3 mt-2">Open Source</h3>
                <p class="text-muted px-3">
                    Join our community on GitHub and contribute.
                </p>
                <a href="https://github.com/joemou/NetworkDigitalTwin" class="font-weight-bold" style="color: #2E74B5; text-decoration: none;">
                    Read more <i class="fas fa-arrow-right small ms-1"></i>
                </a>
            </div>

        </div>
    </div>
</section>

{{< /rawhtml >}}