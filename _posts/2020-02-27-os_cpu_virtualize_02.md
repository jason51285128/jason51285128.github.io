---
layout: post
title: cpu虚拟化之进程API
categories: linux
tags: [os, linux, kernel, virtualize, process api] 
---

# cpu虚拟化之进程API

## fork

运用写时复制技术，复制当前进程，主要包括：

1. 在task数组中找到一个空的位置，并生成一个与当前已经存在的pid不冲突的pid

1. 复制PCB到新的PCB： ldt,页表（让两个进程的线性地址映射到同一物理地址，然后写时复制）
    文件描述符，eip,cs,eflag,esp,ss

````c
pid_t fork(void);
````

## exec

替换当前进程，新进程的代码、数据、栈从可执行文件中加载
一般先使用fork复制进程，然后在子进程中替换进程映像，
系统调用返还后，将从新映像的入口开始执行

````c
int execv(const char *path, char *const argv[]);
````

## wait

进程进入可中断睡眠态，直到子进程结束（其实也是通过信号告诉父进程）释放子进程
的PCB，进程恢复运行，或者收到其他信号，终止等待，进程恢复运行。
该调用会导致任务切换。

````c
pid_t wait(int *wstatus);
````

## exit

退出进程，释放出PCB外的其他所有资源（文件、页表。。。），将进程置为TASK_ZOMBIE态。

````c
void exit(int status);
````

## clone
在创建新的子进程时，可以指定子进程共享父进程的某些资源，如： 地址空间，文件描述符

指定CLONE_THREAD|CLONE_VM时，用来实现线程。

指定CLONE_VFORK则是vfork的实现

````c
int clone(int (*fn)(void *), void *child_stack,
                 int flags, void *arg, ...
                 /* pid_t *ptid, void *newtls, pid_t *ctid */ );
````

## vfork

创建子进程，阻塞父进程，直到子进程调用execv或者exit

通常用于fork之后马上条用execv的场景，可以节省拷贝页表的时间

该系统调用主要是因为老的unix系统fork没有采用写时复制技术，为了
提高fork-execv-wait的效率

````c
pid_t vfork(void);
````