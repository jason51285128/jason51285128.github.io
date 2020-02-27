---
layout: post
title: 内存虚拟化之机制
categories: linux
tags: [os, linux, kernel, virtualize, mechanism] 
---

# 内存虚拟化之机制

## 地址转换

进程运行在虚拟地址空间，操作系统通过cpu的mm单元进行地址转换，实现内存虚拟化。CPU提供
分段和分页两种内存管理机制，虚拟地址通过分段、分页，转换为实际物理地址。

![地址转换过程]({{site.baseurl}}/assets/images/os_mm_virtualize_03.png)

## 分段

### 段选择符

![段选择符]({{site.baseurl}}/assets/images/os_mm_virtualize_04.png)

1.  RPL： 请求优先级
1.  TI： 0，索引GDT， 1，索引LDT
1.  index： 描述符索引

### 段描述符

![段描述符]({{site.baseurl}}/assets/images/os_mm_virtualize_05.png)
![代码和数据段]({{site.baseurl}}/assets/images/os_mm_virtualize_06.png)
![系统段]({{site.baseurl}}/assets/images/os_mm_virtualize_07_1.png)
![系统段]({{site.baseurl}}/assets/images/os_mm_virtualize_07_2.png)


### gdt&ldt

gdt是存储全局段描述符的表，GDTR指向gdt的地址
ldt是存储局部段描述符的表，LDTR执行ldt的地址

![内存管理寄存器]({{site.baseurl}}/assets/images/os_mm_virtualize_08.png)

### 分段控制寄存器

CR0寄存器中的PE标志位，用于控制是否使用分段机制，置位，打开分段机制，复位，关闭分段机制。
置位PE，也将让处理器进入保护模式


## 分页

### 页目录和页表的表项格式

![表项格式]({{site.baseurl}}/assets/images/os_mm_virtualize_09.png)

### 分页地址转换过程

![地址转换过程]({{site.baseurl}}/assets/images/os_mm_virtualize_10.png)

### 页目录地址和缺页异常

CR3保存页目录表的物理地址，当访问不存在的页目录、页表，或者权限不满足时，处理器将产生
异常，CR2保存引起出错的地址。

![页目录和缺页]({{site.baseurl}}/assets/images/os_mm_virtualize_11.png)

### 分页控制寄存器

CR0的PG位用于开启分页机制，置位，打开分页机制，复位，关闭分页机制。

### 页交换

可以将内存页和块设备进行交换，让系统支持更大的虚拟内存空间。