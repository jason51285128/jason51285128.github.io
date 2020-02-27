---
layout: post
title: 并发之锁
categories: linux
tags: [os, linux, kernel, concurrency, lock] 
---

# 并发之锁

## 锁实现的硬件支持

原子操作指令：test-and-set instruction

## 锁实现的原则

效率： 尽量减少因为等待锁释放造成的上下文切换开销
公平： FIFO

## 锁类型

### 自旋锁spinlock

所不可用时不是睡眠等待（进程切换），而是自旋（不释放CPU，相当于不断轮旋），一般用于内核，
暂未给出用户态API。

### 互斥锁mutex

同一时刻只能有一个线程占用，其他线程只能睡眠等待

### 读写锁rwlock

多个读线程可以获取锁，当没有写线程占用锁时；同时仅有一个写线程占用锁

### RCU锁

RCU锁是读写锁的扩展版本，简单来说就是支持多读多写同时加锁，多读没什么好说的，但是对于多写同时加锁，还是存在一些技术挑战的。RCU锁翻译为Read Copy Update Lock，读-拷贝-更新 锁。Copy拷贝：写者在访问临界区时，写者将先拷贝一个临界区副本，然后对副本进行修改；Update更新：RCU机制将在在适当时机使用一个回调函数把指向原来临界区的指针重新指向新的被修改的临界区，锁机制中的垃圾收集器负责回调函数的调用。更新时机：没有CPU再去操作这段被RCU保护的临界区后，这段临界区即可回收了，此时回调函数即被调用。从实现逻辑来看，RCU锁在多个写者之间的同步开销还是比较大的，涉及到多份数据拷贝，回调函数等，因此这种锁机制的使用范围比较窄，适用于读多写少的情况，如网络路由表的查询更新、设备状态表更新等，在业务开发中使用不是很多。


## 锁API

### 互斥锁

1.  init

    ````c
    int pthread_mutex_init(pthread_mutex_t *restrict mutex,
           const pthread_mutexattr_t *restrict attr);
    pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
    ````

1.  lock,trylock,unlock  

    ````c
    int pthread_mutex_lock(pthread_mutex_t *mutex);
    int pthread_mutex_trylock(pthread_mutex_t *mutex);
    int pthread_mutex_unlock(pthread_mutex_t *mutex);
    ````

1.  settype

    ````c
    int pthread_mutexattr_settype(pthread_mutexattr_t *attr, int type);
    ````

### 读写锁

1.  init

    ````c
    int pthread_rwlock_init(pthread_rwlock_t *restrict rwlock,
           const pthread_rwlockattr_t *restrict attr);
    pthread_rwlock_t rwlock = PTHREAD_RWLOCK_INITIALIZER;
    ````

1.  read lock

    ````c
    int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock);
    int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock);
    ````

1.  write lock

    ````c
    int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock);
    ````

1.  unlock

    ````c
    int pthread_rwlock_unlock(pthread_rwlock_t *rwlock);    
    ````

