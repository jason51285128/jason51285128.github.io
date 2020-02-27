---
layout: post
title: 内存虚拟化之地址空间
categories: linux
tags: [os, linux, kernel, virtualize, address space] 
---

# 内存虚拟化之地址空间

进程的概念抽象了CPU，使得每个进程都可以“独享”CPU，操作系统另外一个问题：如何将多个进程
彼此隔离，使得各个进程的数据互不干扰，同时还要保存进程除了能使用系统调用提供的接口外，不能
修改系统的数据？答案就是地址空间，**让每个进程运行在自己的虚拟空间中，操作系统保证不同进程的
虚拟地址空间映射到不同的物理地址，在每个进程的地址空间中，再划分出用户空间和内核空间，用户只能
直接访问用户空间，内核空间则只能通过系统调用才能访问**。


## 地址空间

每个进程的虚拟（逻辑）地址空间为4G，其中0~3G为用户空间，3~4G为内核空间，操作系统再对每个进程
的逻辑地址空间进行映射

![进程地址空间]({{site.baseurl}}/assets/images/os_mm_virtualize_01.png)
![地址映射]({{site.baseurl}}/assets/images/os_mm_virtualize_02.png)


