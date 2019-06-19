---
layout: post
title: stl源码分析
categories: c/c++
tags: stl
---

1.  考虑是否是线程安全的？



# stl源码分析 

## allocator

对于大于128字节的分配，直接由malloc/free，对于小于等于128字节的内存分配，采用内存池
技术：以8字节为单位，用16条链表管理小内存分配和释放，分配内存时，先从16条链表中，取出
合适的空闲内存，若无空闲内存，从内存池中分配一块出来，插入到链表中，如内存池中也无空闲
内存，调用系统malloc/free，如系统malloc/free失败，尝试从空闲链表中释放。

malloc调用失败处理：

1.  用户未定义oom处理程序，调用默认oom处理：抛出bad_alloc异常;

1.  用户自定义oom处理程序，调用用户oom处理，然后重新malloc，直到malloc成功;

面试点：

1.  能够简单写出一个allocator

    ````c++
    class allocator 
    {
        static void* allocate(size_t n)
        {
            void *result = malloc(n);
            if (result == 0)
            {
                fprintf(stderr, "out of memory!");
                exit(1);
            }
            return result;
        }
        static void deallocate(void *p)
        {
            free(p);
        }
    }
    ````

1.  能够分析malloc的异常，异常分支，错误处理

1.  能够提出内存池优化，并给出内存池的设计思路

1.  能够了解系统调用，malloc的操作系统底层实现

## iterator

类似指针，根据实现的功能，分为五类：

![]({{site.base}}/assets/images/media/stl1.png)

![]({{site.base}}/assets/images/media/stl2.png)

为了隐藏container实现的细节，iterator由container自己实现，并通过traits技术，只暴露
iterator的接口。

![]({{site.base}}/assets/images/media/stl2.png)


## container

### 顺序容器

#### vector

维护三个变量，当内存不够时，重新allocate当前2倍内存，然后copy construct， 最后
deconstruct旧内存。所以使用的时候，预估大小。

````c++
typedef value_type* iterator
protected:
  _Tp* _M_start;
  _Tp* _M_finish;
  _Tp* _M_end_of_storage;
````

