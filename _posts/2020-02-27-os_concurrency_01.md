---
layout: post
title: 并发之多线程
categories: linux
tags: [os, linux, kernel, concurrency, multi thread] 
---

# 并发之多线程

## 轻量级进程

线程共享进程的地址空间，不需要拷贝页表，创建线程的开销比进程小，
因为共享地址空间，线程切换，不需要切换地址空间，因而比多进程模型更高效。
Linux 线程基于 clone 系统调用实现。

## 线程 API

1.  pthread_create

    ````c
    #include <pthread.h>

    int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                          void *(*start_routine) (void *), void *arg);
    ````

1.  pthread_join

    ````c
    #include <pthread.h>

    int pthread_join(pthread_t thread, void **retval);
    ````

1.  pthread_cancel

    ````c
    #include <pthread.h>

    int pthread_cancel(pthread_t thread);
    ````

1.  pthread_detach

    ````c
    #include <pthread.h>

    int pthread_detach(pthread_t thread);
    ````