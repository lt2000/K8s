> Slim: OS Kernel Support for a Low-Overhead Container Overlay Network  

# Contributions

* 在当前的容器覆盖网络中，**我们认为单包处理成本(如包转换、网络堆栈的额外遍历)是主要的瓶颈**
* 实现了一个通过处理**连接级元数据**以实现网络虚拟化的解决方案：Slim。
  * Slim与当今的容器化应用程序和标准的操作系统内核兼容。
  * Slim支持多种网络策略，
  * Slim保证与今天的容器覆盖网络相同的安全模型
* 演示了Slim对广泛流行的容器化应用程序的好处

# Why overlay network

* 覆盖网络消除了应用程序开发人员之间协调端口和IP地址的负担，并极大地简化了将遗留企业应用程序迁移到云的过程
* host mode
  * benefit：主机模式的网络性能接近任何直接使用主机操作系统网络堆栈的进程的性能
  * limitations：容器必须使用主机网络接口的IP地址。这使移植变得复杂；协调端口冲突
* macvlan。。。。。

# Motivation

* 容器覆盖网络对于提供容器的**可移植性**是至关重要的，但是它们**在吞吐量、延迟和CPU利用率方面带来了巨大的开销**
* **开销产生原因**：在覆盖网络上发送一个包需要一次额外的网络堆栈遍历（**数据复制**），也需要包的封装和解封装（**CPU开销**）。
* 现有工作的Limitations：包转向[40]和虚拟化硬件支持[22,14])只能部分解决这些问题
* 为什么可以通过操作连接级数据实现Slim？：**对于容器，操作系统内核完全了解每个网络连接，这一点与VM不同**
* 为什么在连接级实现Slim？：当TCP连接建立后，可以不再需要封装主机的ip、mac地址，就能转发到目的地址，就可以绕过数据包封装过程，将原始数据包送到主机网络空间进行转发

# Challenges

* 网络虚拟化必须与当今未经修改的容器化应用程序兼容
* 我们需要支持与当前容器覆盖网络在数据平面上执行的相同的网络策略
* 我们需要执行与今天的容器覆盖网络相同的安全模型

# Design & implement

* 数据包只通过操作系统内核的网络堆栈一次，从overlay网络的数据平面将包转换消除
* 通过在连接设置时操作连接级元数据来实现网络虚拟化，节省CPU周期并减少包延迟

# Limitations

* Slim增加了连接设置的复杂性，导致连接设置时间延长了106%
* Slim支持静态容器迁移，但不支持动态容器迁移;
* slim支持基于连接的网络策略，而不是基于分组的网络策略;
* slim支持TCP，默认为UDP套接字的标准处理

# Other

* 容器适用于托管**大规模分布式**的应用程序

  

