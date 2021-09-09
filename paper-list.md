# 容器网络调研

## ATC

* **BASTION: A Security Enforcement Network Stack for Container Networks  2020**

  https://www.usenix.org/system/files/atc20-nam.pdf

* NetKernel: Making Network Stack Part of the Virtualized Infrastructure 2020

  https://www.usenix.org/system/files/atc20-niu_0.pdf

* **MigrOS: Transparent Live-Migration Support for Containerised RDMA Applications**  **2021**

  https://www.usenix.org/system/files/atc21-planeta.pdf

* FaaSNet: Scalable and Fast Provisioning of Custom Serverless Container Runtimes at
  Alibaba Cloud Function Compute  2021

  https://www.usenix.org/system/files/atc21-wang-ao.pdf

## NSDI
* **Iron: Isolating Network-based CPU in Container Environments 2018**
  https://www.usenix.org/system/files/conference/nsdi18/nsdi18-khalid.pdf
  
* **Slim: OS Kernel Support for a Low-Overhead Container Overlay Network 2019**
  https://www.usenix.org/conference/nsdi19/presentation/zhuo
  
* Ship Compute or Ship Data? Why Not Both? 2021
  https://www.usenix.org/conference/nsdi21/presentation/you

* **FreeFlow: Software-based Virtual RDMA Networking for Containerized Clouds 2019**
  https://www.usenix.org/conference/nsdi19/presentation/kim
  
* Minimal Rewiring: Efficient Live Expansion for Clos Data Center Networks 2019	（key：DCN topology）
  https://www.usenix.org/conference/nsdi19/presentation/zhao
## INFOCOM

* **An Analysis and Empirical Study of Container Networks 2018**

  https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8485865

## EuroSys
* **Parallelizing Packet Processing in Container Overlay Networks 2021**
  http://www.cs.binghamton.edu/~huilu/pubs/eurosys21-final134.pdf
## ASPLOS

* Nightcore: Efficient and Scalable Serverless Computing for Latency-Sensitive, Interactive Microservices (Artifacts)  March 2021
  https://dl.acm.org/doi/abs/10.1145/3445814.3446701
  
* **PARTIES: QoS-Aware Resource Partitioning for Multiple Interactive Services 2019 （Network Bandwidth）**
  https://dl.acm.org/doi/10.1145/3297858.3304005


## FAST

* Slacker: Fast Distribution with Lazy Docker Containers 2016

  https://www.usenix.org/system/files/atc21-wang-ao.pdf
## ICPP
* FlowCon: Elastic Flow Configuration for Containerized Deep
  Learning Applications 2019
  https://dl.acm.org/doi/10.1145/3337821.3337868
  

## OSDI
* History-Based Harvesting of Spare Cycles and Storage in Large-Scale Datacenters 2016 （Harvest network resource？）
  https://www.usenix.org/conference/osdi16/technical-sessions/presentation/zhang-yunqi
  
## SOSP
* Resource Central: Understanding and Predicting Workloads for Improved Resource Management in Large Cloud Platforms 2017
  https://dl.acm.org/doi/10.1145/3132747.3132772

## 补录

RPC和网络IO优化 

* µTune: Auto-Tuned Threading for OLDI Microservices（OSDI'18）
  微服务/Serverless的通信依赖RPC，涉及到大量的网络IO，网络IO处理效率会严重影响尾延迟大小。本文综合不同的线程处理模型（同步/异步；内联/派发；阻塞/忙等）来提供高性能的IO处理。
* RPCValet: NI-Driven Tail-Aware Balancing of μs-Scale RPCs（ASPLOS'19）
  基于新硬件（如SmartNIC）和新协议（如RDMA）对RPC处理流程进行优化。
* Dagger: Efficient and Fast RPCs in Cloud Microservices with Near-Memory Reconfigurable NICs（ASPLOS'21）
  利用FPGA和RDMA offload RPC。
* R2P2：Making RPCs first-class datacenter citizens
  设计基于UDP的传输层协议R2P2来代替RPC使用的TCP协议。
* TAS: TCP Acceleration as an OS Service（EuroSys'19）
  将执行RPC的TCP请求交给特定的一组CPU快速处理。
* Parallelizing Packet Processing in Container Overlay Networks（EuroSys'21）
  Overlay网络包并行处理
