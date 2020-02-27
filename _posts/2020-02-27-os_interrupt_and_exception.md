---
layout: post
title: 异常和中断
categories: linux
tags: [os, linux, kernel, interrupt, exception] 
---

# 异常和中断

中断门与陷阱门的区别

通过中断门进入中断服务程序时cpu会自动将中断关闭，也就是将cpu中eflags寄存器中IF标志复位，防止嵌套中断的发生；而通过陷阱门进入服务程序时则维持IF标志不变。


中断，异常：
不允许把控制转移到比 CPL更低特权级代码段的中断处理程序（只能网更高或者同级特权级转移），否则会产生异常
不检查 RPL
对于使用 INT 指令产生的软中断，才会检查们描述符中的 DPL，此时必须满足 CPL 小于等等 DPL


运行在内核态，开始使用的是用户态栈，然后通过模拟中断返回，恢复