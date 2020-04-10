---
layout: post
title: linux虚拟内存管理 
categories: linux
tags: [os, linux, process address space, virtual memory management] 
---

# linux虚拟内存管理

所有合法的虚拟地址都是受VMA管理的，VMA通过对虚拟地址空间分段，每段都有不同的权限，
然后通过双向链表和红黑树来管理进程使用的所有虚拟地址。

虚拟地址通常会保留一段低地址空间，如32位系统的0~0x08048000(低128M空间)，用来捕获空指针
和小整型值指针引用内存的异常

如何查看进程的缺页中断信息？
可通过以下命令查看缺页中断信息

````shell
ps -o majflt,minflt -C <program_name>
ps -o majflt,minflt -p <pid>
````

其中， majflt 代表 major fault ，指大错误， minflt 代表 minor fault ，指小错误。
这两个数值表示一个进程自启动以来所发生的缺页中断的次数。其中 majflt 与 minflt 的不同是， 
majflt 表示需要读写磁盘，可能是内存对应页面在磁盘中需要 load 到物理内存中，
也可能是此时物理内存不足，需要淘汰部分物理页面至磁盘中。

1.  [Linux的进程地址空间一](https://zhuanlan.zhihu.com/p/66794639)
1.  [Linux的进程地址空间二](https://zhuanlan.zhihu.com/p/67936075)
1.  [Linux的进程地址空间三](https://zhuanlan.zhihu.com/p/68398179)


## 内存分配器

### dlmalloc

1.  [内部实现参考1](https://blog.csdn.net/txx_683/article/details/53456357?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
1.  [内部实现参考2](https://blog.csdn.net/txx_683/article/details/53456661?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)
1.  [内部实现参考3](https://blog.csdn.net/vector03/article/details/40977679)
1.  [内部实现参考4](https://blog.csdn.net/vector03/article/details/40979181)
1.  [内部实现参考5](https://blog.csdn.net/vector03/article/details/41008671)
1.  [内部实现参考6](https://blog.csdn.net/vector03/article/details/41074005)
1.  [内部实现参考7](https://blog.csdn.net/vector03/article/details/41114611)
1.  [内部实现参考8](https://blog.csdn.net/vector03/article/details/41205355)
1.  [内部实现参考9](https://blog.csdn.net/vector03/article/details/41314055)

### ptmalloc2

dlmalloc的多线程版本

### tcmalloc

[tcmalloc实现](https://wallenwang.com/2018/11/tcmalloc/)

### jemalloc

实现和tcmalloc类似

### 对比

[各版本malloc对比](https://blog.csdn.net/junlon2006/article/details/77854898)

### 优化点

1. 提高分配速度-降低锁粒度，增加线程本地cache
1. 增加局部亲和性，相对未使用的页面，优先使用dirty page，提升缓存命中
1. 空闲内存能够空闲


