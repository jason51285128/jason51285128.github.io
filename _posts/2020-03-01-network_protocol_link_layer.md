---
layout: post
title: 计算机网络之链路层
categories: [network protocol]
tags: [network protocol, link layer] 
---

# 计算机网络之链路层

## 链路层的主要功能:封装成帧,透明传输,差错检验

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_01.png)

### 封装成帧：帧定界和控制信息

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_02.png)

### 透明传输：解决当帧的数据部分出现帧定界符时，带来的冲突问题

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_03.png)
![]({{site.baseurl}}/assets/images/network_protocol_link_layer_04.png)

### 差错检验

无比特差错传输：CRC（循环冗余校验），FCS（帧检查序列），k位数据，n位校验码，一共n+k位，模二运算，无进位或者借位

n位校验码产生方法：

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_05.png)

接收端校验方法：（校验整个帧）

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_06.png)

可靠传输：发送的是什么接受的就是什么，没有传输差错，即没有帧丢失，帧重复，帧失序等现象

解决办法：帧编号，确认，重传机制，在上层协议实现

## PPP各字段意义

PPP：一种点对点信道的数据链路层实现协议

PPP数据链路帧及各字段意义：

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_07.png)
![]({{site.baseurl}}/assets/images/network_protocol_link_layer_08.png)


## Csma/cd协议

Csma/cd:一种共享信道的数据链路层实现协议，其特点是：载波监听多点接入/碰撞检测。每一台接入总线的机器有一个硬件Mac地址，用于寻址。这种链路层被应用于以太网。


争用期：在共享信道中，多台主机同时收/发数据，抢占总线，产生冲突，导致总线无法使用,2T(51.2us)

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_09.png)

## 网卡的作用

发送和接收IP数据包

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_10.png)

## Mac地址和Mac帧
1.  Mac地址：48位，用于硬件寻址，因为以太网是采用共享信道的
1.  Mac层：以太网数据链路层的一部分,Mac帧则是以太网数据链路层帧格式
1.  Mac帧格式：以太帧最短64字节（DA+SA+ETYPE+DATA+FCS, 不包括前导码（前同步码0x55+帧开始符0xD5，图中不是网络字节序，要倒过来））

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_11.png)

### MAC地址分类

MAC 地址是48 bit 二进制的地址，如：00-e0-fc-00-00-06。

可以分为单播地址、多播地址和广播地址。

单播地址：第一字节最低位为0，如：00-e0-fc-00-00-06

多播地址：第一字节最低位为1，如：01-e0-fc-00-00-06

广播地址：48 位全1，如：ff-ff-ff-ff-ff-ff

### MAC帧中的ETYPE

只有当ETYPE >= 0x0600, ETYPE字段才表示MAC客户机协议

0x8100:VLAN标签

0x0800: ip协议

0x8847: mpls 单播

0x8848: mpls 组播

### 为什么wireshark抓取不到前序和FCS?

因为wireshark把这2个都给过滤了。
在物理层上网卡要先去掉前导同步码和帧开始定界符，然后对帧进行CRC检验，如果帧校验和错，就丢弃此帧。如果校验和正确，就判断帧的目 的硬件地址是否符合自己的接收条件（目的地址是自己的物理硬件地址、广播地址、可接收的多播硬件地址等），如果符合，就将帧交“设备驱动程序”做进一步处 理。这时我们的抓包软件才能抓到数据，因此，抓包软件抓到的是去掉前导同步码、帧开始分界符、FCS之外的数据.

## 二层转发（交换机）
### 转发原理
二层转发过程：自学习：登记（from），转发，丢弃（to，转发表）

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_12.png)

生成树算法：避免在转发过程中成环

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_13.png)

### VLAN
在二层，将交换机上的端口进行分组，隔离，只有在同一个组的端口之间才能通信；

#### VLAN划分方法
1.  基于端口的VLAN划分
1.  基于MAC地址的VLAN
1.  基于三层协议的VLAN
1.  基于组播组的VLAN
1.  基于IP地址映射的VLAN
1.  基于策略的VLAN

#### VLAN 格式
插在DA, SA 和ETYPE之间

![]({{site.baseurl}}/assets/images/network_protocol_link_layer_14.png)