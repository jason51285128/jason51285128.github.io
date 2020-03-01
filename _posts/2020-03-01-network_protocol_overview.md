---
layout: post
title: 计算机网络概述 
categories: network protocol
tags: [network protocol] 
---

# 计算机网络概述

## 计算机网络的定义,分类及拓扑组成

1.  定义:一些互相连接的,自治的计算机的集合
1.  组成:网络由若干结点(计算机,集线器,交换机,路由器)和连接这些节点的链路组成.
1.  拓扑组成:边缘部分,所有连接在因特网上的主机组成,用来进行通信和资源共享;核心部分,由大量网络和连接这些网络的路由器组成,为边缘部分提供服务
1.  分类:广域网,城域网,局域网,个人区域网

## 计算机网络的主要性能指标

1.  速率:b/s,kb/s, mb/s
1.  带宽:最高速率,即单位时间内通过信道的最大数据量
1.  吞吐量
1.  时延:发送时延,传播时延,处理时延,排队时延
1.  时延带宽积
1.  往返时间RTT
1.  利用率

## 交换

![]({{site.baseurl}}/assets/images/network_protocol_overview_01.png)	
![]({{site.baseurl}}/assets/images/network_protocol_overview_02.png)	
		
## 网络协议要素

![]({{site.baseurl}}/assets/images/network_protocol_overview_03.png)

## 网络体系结构

![]({{site.baseurl}}/assets/images/network_protocol_overview_04.png)
	
## 网络分层的意义

1.  屏蔽链路层差异，不同链路层的设备无法直接通信，连接不同
    链路层的设备；

1.  分层实现解耦和，功能独立
