---
layout: post
title: redis中的数据类型
categories: redis 
tags: [redis, DB, storage, NoSQL]
---

# redis中的数据类型

## redis 介绍

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。
作为内存NoSQL数据库，redis也支持持久化，redis中的数据以**key—value**的形式存储，**所有的命令
都是原子的**，本文介绍在golang中如何使用redis。

## redis中的数据特点-二进制安全

把数据当作二进制的字节流，按照二进制的格式进行存取，其实现可以理解为：

````c
struct sdshdr {
    int len;//缓冲区实际长度
    int free; //缓冲区空闲长度
    char buf[]; //二进制缓冲区
};
````

## key

可以使用任意二进制序列作为key，空字符串也可以作为有效的key, redis的key是string类型，所以
最大不能超过512M

* key的长度不要太长，不要超过1024字节，这样既消耗内存，又要开销长时间计算

*	key的命名尽量可读

## string

存储任意二进制值，值的长度不能超过512M。

*	支持原子操作

*	支持超时机制

## list

**linked list**， 相当于双向链表


## hash

实现hash结构

## set

无序、不重复的string集合

## order-set

有序、不重复的string集合

## bitmap

其实是字符串，每个字符串最大512MB，所以每个bitmap最大4Gbit

## hyperloglog

估算一个集合的基数（不同元素的个数）， 于set不同，hyperloglog
并不存储元素的值，因而hyperloglog占用的内存不会因为元素个数的
增长而增长，而是占用固定的内存（12KB），就可以估算元素个数长达
2^64的集合的基数，估算误差在一定的范围内



