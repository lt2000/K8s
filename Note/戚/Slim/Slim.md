## Slim: OS Kernel Support for a Low-Overhead Container Overlay Network (NSDI ’19)

https://www.usenix.org/presentation/zhuo

### 问题：

在overlay network中，发送和接收的每个报文都必须要经历两次网络栈，导致系统吞吐量下降，报文RTT延时增大，cpu利用率下降。

### 分析：

1、packet flow：

![1](https://github.com/lt2000/K8s/blob/main/Note/%E6%88%9A/images/Slim/1.jpg)

容器app发送的报文首先经过虚拟网络接口上网络栈，然后报文交给虚拟交换机进行转换（例如，添加主机网络头），最后数据经过主机网络栈，并在主机网络接口上发送出去。接收方执行相反的操作。

2、Quantifying Overhead（定量化开销）：

实验设置：

|               | 参数                                            |
| ------------- | ----------------------------------------------- |
| CPU           | Intel Xeon E5-2680 (12 physical cores, 2.5 GHz) |
| NIC           | Intel XL710(40Gbps)                             |
| Linux version | 4.4                                             |

结果：

- 系统吞吐量和往返时延：

![](https://github.com/lt2000/K8s/blob/main/Note/%E6%88%9A/images/Slim/2.jpg)

Intra表示连接在同一台物理机器上的tcp连接，Inter表示连接在不同物理机器上的tcp连接。Host表示主机模式。

结论：overlay网络模式相较于主机模式，性能严重下降，不同物理机的tcp连接性能下降更严重，也更能反映真实的应用场景。

- cpu利用率：

  ![](https://github.com/lt2000/K8s/blob/main/Note/%E6%88%9A/images/Slim/3.jpg)

usr：用户级应用程序消耗的CPU周期。

sys：内核内部（不包括中断处理）消耗的CPU周期。

soft：软件中断消耗的CPU周期。默认overlay网络（Random）模式下，软件中断相当于使用了0.56 virtual cores，Host模式相当于使用了0.21 virtual cores。

结论：overlay网络的CPU开销主要来自于服务的软件中断，原因是数据包转换和额外的网络栈的处理未直接使用系统调用，而是采用了软件中断的处理方式。

### slim架构：（go through the OS kernel’s network stack **only once**）

![](https://github.com/lt2000/K8s/blob/main/Note/%E6%88%9A/images/Slim/4.jpg)

SlimSocket：将POSIX套接字接口公开给应用程序，以拦截与容器相关的系统调用的调用。当SlimSocket检测到应用程序试图新建tcp连接时，它会向SlimRouter发送请求。

SlimRouter：设置网络连接后，它将作为文件描述符的访问权限传递给容器内的进程。然后容器内的应用使用主机名称空间的文件描述符直接向主机网络发送/接收数据包。

SlimKernModule：为了保证安全性，SlimKernModule有三个功能：（1)跟踪在容器内传播时的文件描述符。(2）根据SlimRouter的请求撤销文件描述符。(3)禁用不安全的系统调用列表（例如，getpeername，connect，setsocket）。
