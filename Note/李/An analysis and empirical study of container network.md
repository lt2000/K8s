# Contributions？

> Three finding 

* 在单个主机上的容器网络中，**性能和安全隔离是一个困难的权衡**.通过共享相同的网络名称空间可以获得良好的性能，而通过使用隔离的名称空间可以加强安全性
* 多主机容器网络在选择网络方案时面临困难的权衡。
  * overlay 网络包处理带来较大开销；但在网络管理上更加灵活安全
  * NAT网络性能高，但安全性低
  * 路由网路性能高但需要额外的设备支持（BGP）
* 虚拟化会带来吞吐率下降和延迟增加。容器网络和虚拟机网络之间复杂的交互
* 集装箱网络在启动时间上有一个数量级的差异。在选择容器网络时，短时间且对延迟敏感的工作负载应该考虑启动时间

# Why Container?

* VMs limitations：
  * VM成熟的操作系统占用了大量内存空间，限制了可以整合到单个物理机的VM数量
  * OS启动时间长，不利于部署生命周期短的应用
* Container advantages：
  * 整合率高，即单个机器的容器密度高
  * 启动时间短，适用于部署事务驱动的微服务，现实场景中很多容器的生命周期都很短

# Challenge？

> 在动态云环境中为**大量短期**使用的容器提供网络连接

* 操作系统级虚拟化的灵活性允许容器以多种方式将连接到外部网络（**需要根据隔离性、安全性、性能的要求弹性选择网络**）
* 由于单个服务器上的容器密度很高，因此为了保证性能，在扩展大量链接的同时，也要求单个链接对网络性能的影响降到最小
* 在建立容器间连接之前，容器不能使用（**网络连接建立时延会削弱容器启动快的优势**，例如事务触发的无服务器代码）

* 容器的安全隔离性低于虚拟机

# Solution？

## single host network

> Through shared memory

* Node mode

* bridge mode

* container mode

* host mode

  <img src="image/image-20210902200608526.png" alt="image-20210902200608526" style="zoom:67%;" />
  
  <img src="image/image-20210902200634053.png" alt="image-20210902200634053" style="zoom:150%;" />

## multi host network

> providing IP addressing

* host mode
* NAT
  * 方案：container IP address = host IP + port num
  * 每个包都需要地址转换，会导致性能下降
  * 在由生命周期短的容器组成的动态网络中，端口冲突是个问题（**安全性**）
* overlay network
  * 方案：容器将其私有IP地址和主机IP之间的映射保存在键-值(KV)存储中，所有主机都可以访问该存储，**将容器IP和其物理位置解耦**
  * 包处理过程、改变 数据包大小
* routing
  * 方案：它在主机内核中实现了一个虚拟路由器并使用用于分组路由的BGP
  * 支持网络协议有限
  * BGP在大部分网络中心未被支持
  * 路由表的大小限制了容器网络的规模

![image-20210902200901132](image/image-20210902200901132.png)

# Experiment？

> 影响因子

## packet size

* ![4](D:\Github\K8s\Note\李\image\4.png)
* 对overlay**网络数据包越大封装时间越长**

* 两个问题？
  * 为什么overlay网络的吞吐率没有随着packet size 的增加而增加？？
    * 虽然随着数据包增大，时间开销增大，但是数据速率也在增大，因此吞吐率基本没有变化
  * 为什么overlay网络的吞吐率随着packet size的增加，开销越来越大
    * 数据包的封装和解封装需要在网络堆栈里来回复制，因此数据包越大，时间开销越大

## network protocol

* ![5](D:\Github\K8s\Note\李\image\5.png)
* 在包括基线在内的所有网络中，TCP实现了比UDP更高的吞吐量
  * TCP采用滑动窗口避免网络拥塞，动态改变数据发送速率
  * 采用Nagle‘s算法，提高链路效率
* TCP的网络方案比UDP的吞吐率开销大很多
  * 由于TCP中的确认涉及更多的操作和CPU消耗，主机间容器网络对TCP的开销要大于UDP
* **以上导致当packet size 较大时，采用UDP和TCP的overlay网络的吞吐率相差不大**

## network launch time

* ![6](D:\Github\K8s\Note\李\image\6.png)
* 建立容器网络的时间对于短期的或对延迟敏感的工作负载非常重要
* 启动一个覆盖层或初始化BGP路由表比初始docker映像启动时间长4.5 - 23倍
  * overlay 网络大部分时间花费在向KV存储注册容器
  * Calico (BGP)的时间开销主要在于传播路由表

## interference

* ![7](D:\Github\K8s\Note\李\image\7.png)
* 容器网络整合度高，容器的抗干扰性需要考虑
* 容器网络通常包括一个用于管理网络流的用户空间守护进程和一个用于路由或包封装的内核组件
* 用户空间守护进程很容易因干扰而变慢，而内核组件却能承受这种影响，因为内核内处理的优先级严格高于任何用户级计算，Calico (BGP)，实现内核虚拟路由器，而overlay网络采用用户空间守护进程，**因此BGP网络比overlay网络的抗干扰性强**
* 实验表明，在高度整合的环境中，需要将容器网络服务与其他用户容器工作负载隔离开来，或者在主机操作系统内核中实现此类服务

## CPU overhead

* ![8](D:\Github\K8s\Note\李\image\8.png)
* Calico (BGP模式)会增加虚拟路由器的计算量，导致CPU开销增加。由于包的封装和解封装，所有覆盖网络消耗更多的CPU

## scalability

* ![9](D:\Github\K8s\Note\李\image\9.png)
* 单个主机上容器网络的可扩展性，**集中式桥接docker0成为了多个容器连接到桥接时的瓶颈。**
* ![10](D:\Github\K8s\Note\李\image\10.png)
* 这表明跨host的overlay网络是扩展的主要瓶颈

## impact of virtualization

