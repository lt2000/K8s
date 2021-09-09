> **MigrOS: Transparent Live-Migration Support for Containerised RDMA Applications**  **2021**
>
> https://www.usenix.org/system/files/atc21-planeta.pdf

# What？

* MigrOS：实现了一个**容器化RDMA应用透明实时传输**的OS级架构


# Why？

## motivation？

* RDMA network 和 container 的冲突：**容器化加强了应用程序和主机之间更严格的隔离，而RDMA网络试图使应用程序和底层硬件“更接近”彼此** ？？？
* **Live migration vs restart cost**：
  * 静态迁移需要挂掉container后，在其他主机重启container，但RDMA应用程序往往有较大的状态，重启代价非常高
  * 相反，动态迁移可以透明地移动应用程序状态，并且不会对应用程序施加额外的要求

# How?

## challenge?

* 对于大多数应用容器状态驻留在内核中，迁移后可进行提取和恢复，但是RDMA通信信道的状态没有保存在内核中，是不能被现有系统恢复的，因此

  RDMA应用程序不能被检查点或迁移

  * 在RDMA应用中，操作系统不能改变连接状态，除非断开连接，而且尽管操作系统可以阻止进程进一步发送消息，但NIC仍然可以无声地改变应用程序的状态
  * 部分连接状态驻留在网卡上，操作系统无法访问。在这种情况下，透明地创建一致的检查点是不可能的

* **OS不可能拦截数据包来操作和修复中断的连接**

## design & implementation？



> **FreeFlow: Software-based Virtual RDMA Networking for Containerized Clouds 2019**
> https://www.usenix.org/conference/nsdi19/presentation/kim



> **Slim: OS Kernel Support for a Low-Overhead Container Overlay Network 2019**
> https://www.usenix.org/conference/nsdi19/presentation/zhuo



> **Parallelizing Packet Processing in Container Overlay Networks 2021**
> http://www.cs.binghamton.edu/~huilu/pubs/eurosys21-final134.pdf