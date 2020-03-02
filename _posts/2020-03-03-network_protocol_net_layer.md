# 计算机网络之网络层

## 数据报和虚电路

无连接的网络层模型和面向连接的网络层模型,前者被应用于现在的以太网模型,网络层向上提供无连接的,尽最大努力交付的数据报服务
	
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_01.png)	
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_02.png)	
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_03.png)
	
## IP协议

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_04.png)

## 地址划分

经历三个阶段：分类编址，子网划分，构成超网（无分类编址）

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_05.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_06.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_07.png)
	
## ip数据报格式(只校验头部)

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_08.png)

## arp协议
	
IP到MAC的映射协议

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_09.png)

## 分组转发算法

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_10.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_11.png)
	
## 无分类编址（构造超网）

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_12.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_13.png)

## ICMP&IGMP

### ICMP：网络控制

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_14.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_15.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_16.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_17.png)

### IGMP：实现组播（多播）

广播是对同一个同一个网段的主机而言的；多播可以跨不同的网段；

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_18.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_19.png)

## 路由选择协议
	
路由器的作用：转发分组

内部网关协议，RIP：距离向量算法

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_20.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_21.png)

外部网关协议：BGP

## VPN
	
内部ip（专有ip，私有ip）：由于解决内部主机之间的通信

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_22.png)

VPN：解决两个远距离内部网的通信问题，或者使一个外网用户加入到一个专有内部网络中，通过ip隧道实现

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_23.png)
	
## NAT

nat协议（一般在路由器上实现）：解决内部主机访问外部网络的问题

![]({{site.baseurl}}/assets/images/network_protocol_net_layer_24.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_25.png)
![]({{site.baseurl}}/assets/images/network_protocol_net_layer_26.png)
	