---
title: 虚拟 IP
toc: true
tags:
  - HA
  - 网络
  - 虚拟 IP
categories:
  - "\U0001F4BB 工作"
  - 网络
date: 2020-05-26 12:27:56
---

## 虚拟 IP
在 TCP/IP 的架构下，所有想上网的电脑，不论是用何种方式连上网路，都必须要有一个唯一的 IP-address。事实上 IP 地址是主机硬件地址的一种抽象，简单的说，MAC 地址是物理地址，IP 地址是逻辑地址。

虚拟 IP，就是一个未分配给真实主机的 IP，也就是说对外提供服务器的主机除了有一个真实 IP 外还有一个虚 IP，使用这两个 IP 中的任意一个都可以连接到这台主机。

虚拟 IP 一般用作达到 HA(High Availability)的目的，比如让所有项目中数据库链接项配置的网络 ip 地址都是这个虚 IP，当主服务器发生故障无法对外提供服务时，动态将这个虚 IP 切换到备用服务器。

## 虚拟 IP 原理

**ARP**是地址解析协议，它的作用很简单，将一个 IP 地址转换为 MAC 地址，然后给传输层使用。

每台主机中都有一个 ARP 高速缓存，存储同一个网络内的 IP 地址与 MAC 地址的对应关系，以太网中的主机发送数据时会先从这个缓存中查询目标 IP 对应的 MAC 地址，会向这个 MAC 地址发送数据。操作系统会自动维护这个缓存。

在 Linux 下可以使用 arp 命令操作 ARP 高速缓存。

比如存在物理机 A(IP 是 192.168.192.54 )和物理机器 B(IP 是 192.168.192.40)，A 作为对外服务的主服务器(比如数据库主库)，B 作为备份机器，两台服务器之间的通信是通过 HEARTBEAT，即主服务器会定时的给备份服务器发送数据包，告知主服务器服务正常，当备份服务器在规定时间内没有收到主服务器的 HEARTBEAT，就会认为主服务器宕机，则备份服务器就会升级为主服务器。假设物理机 A 的 ARP 缓存如下：

```plain
地址                     类型    硬件地址            标志  Mask            接口
192.168.192.54           ether   00:21:5A:DB:68:E8  C                     eth0
192.168.192.237          ether   00:21:5A:DB:68:E8   C                     eth0
192.168.192.40           ether   00:21:5A:DB:7F:C2   C                     eth0
...
```
另外物理机器 B(IP 是 192.168.192.40)的 ARP 缓存如下：
```plain
地址                     类型    硬件地址            标志  Mask            接口
192.168.192.54           ether   00:21:5A:DB:68:E8   C                     eth0
192.168.192.237          ether   00:21:5A:DB:7F:C2   C                     eth0
192.168.192.40           ether   00:21:5A:DB:7F:C2   C                     eth0
...
```
当机器 B 通过 BeatHeart 得知机器 A 对外服务质量低于预期的时候(比如发生故障，服务无响应)，会将自己的 ARP 缓存发送出去，让路由器修改路由表，告知虚拟地址应该指向我(物理机器 B：192.168.192.40)。这时候，外界再次访问虚拟 IP（192.168.192.237）的时候，机器 B 会变成主服务器，而 A 降级为备份服务器。这就完成了主从机器的自动切换，这一切对外界是透明的（不可感知）。

## IP 漂移
上面的 VIP 自动切换的过程就称之为 IP 漂移。

我们可以通过 Keepalived 来实现这个过程。 Keepalived 是一个基于 VRRP 协议(Virtual Router Redundancy Protocol，即虚拟路由冗余协议)来实现的 LVS(负载均衡器)服务高可用方案，可以利用其来避免单点故障。一个 LVS 服务会有 2 台服务器运行 Keepalived，一台为主服务器（MASTER），一台为备份服务器（BACKUP），但是对外表现为一个虚拟 IP，主服务器会发送特定的消息给备份服务器，当备份服务器收不到这个消息的时候，即主服务器宕机的时候， 备份服务器就会接管虚拟 IP，继续提供服务，从而保证了高可用性。

## 参考链接
[虚拟 IP 和 IP 漂移 - xiaobaoqiu Blog](https://xiaobaoqiu.github.io/blog/2015/04/02/xu-ni-iphe-ippiao-yi/)
[LVS 专题-(3) 虚拟 ip 理解 - crazyYong - 博客园](https://www.cnblogs.com/crazylqy/p/7741958.html)