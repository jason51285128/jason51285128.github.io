---
layout: post
title: 持久化之文件系统
categories: linux
tags: [os, linux, kernel, persistence, fs] 
---

# 持久化之文件系统

## 文件系统基本概念

超级块： 记录整个文件系统系统，类型、inode表区域、data块区域
inode位图： 记录inode使用情况
data块位图： 记录data块使用情况
inode block： 存储所有inode，inode记录了文件的相关属性： 文件名、大小、权限。。。
data block： 文件的真是数据

**目录也是一个文件，里面记录了属于该目录下的文件或者目录**

文件系统可简单的理解为一颗树结构

## Linux文件系统架构

![linux文件系统架构]({{site.baseurl}}/assets/images/os_persistence_01.jpg)






