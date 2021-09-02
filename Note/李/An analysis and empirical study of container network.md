# Contributions



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

# Solution

## single host network

> Through shared memory

* Node mode

* bridge mode

* container mode

* host mode

  <img src="image/image-20210902200608526.png" alt="image-20210902200608526" style="zoom:67%;" />
  
  <img src="image/image-20210902200634053.png" alt="image-20210902200634053" style="zoom:150%;" />

## multi host network

* host mode
* NAT
* overlay network
* routing

![image-20210902200901132](image/image-20210902200901132.png)
