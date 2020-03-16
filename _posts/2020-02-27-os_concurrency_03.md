---
layout: post
title: 并发之条件变量
categories: linux
tags: [os, linux, kernel, concurrency, conditional variable] 
---

# 并发之条件变量

1.  消费者线程在获得临界变量的锁之后，查看临界变量（条件）是否满足，如果不满足，调用wait等待，
wait会将线程加入到条件变量的等待队列，然后释放临界变量的锁资源，将线程切换到睡眠态；
1.  另一方面，生产者线程，索取临界变量的锁资源，改变临界变量，调用signal唤醒在条件变量上等待的线程，
然后释放临界变量的锁资源；
1.  消费者线程被唤醒后（继续wait系统调用处理），再次获取临界变量的锁资源，将线程加入到可运行队列；
1.  消费者线程真正被唤醒，继续执行

## 为什么使用条件变量需要结合锁？

**signal**只会唤醒已经存在于条件变量的等待队列中的线程，也就是说如果调用signal时，条件
变量的等待队列没有线程，那么signal什么也不做

**wait**是将线程加入到条件变量的等待队列，并让线程进入休眠态，也就是说如果在线程加入到
条件变量的等待队列之前，信号已经被触发，并且之后信号不会再触发，那么线程将永久睡眠

所以我们需要保证生产者发出信号时，消费者时位于条件变量的等待队列的？

1.  设置一个临界变量，用来指示条件是否已经满足，消费者只有在条件为满足时，才将自己添加到
条件变量的等待队列；
1.  临界变量需要加锁，防止生产者在消费者加入等待队列之前，就发送信号

## API

1.  init
    
    ````c
    int pthread_cond_init(pthread_cond_t *restrict cond,
           const pthread_condattr_t *restrict attr);
    pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
    ````

1.  wait

    ````c
    int pthread_cond_timedwait(pthread_cond_t *restrict cond,
           pthread_mutex_t *restrict mutex,
           const struct timespec *restrict abstime);
    int pthread_cond_wait(pthread_cond_t *restrict cond,
           pthread_mutex_t *restrict mutex);
    ````

1.  signal

    ````c
    int pthread_cond_signal(pthread_cond_t *cond);
    ````

##  demo

````c
#include <stdio.h>
#include <pthread.h>
#define MAX 5

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t notfull = PTHREAD_COND_INITIALIZER;  //是否队满
pthread_cond_t notempty = PTHREAD_COND_INITIALIZER; //是否队空
int top = 0;
int bottom = 0;

void* produce(void* arg)
{
    int i;
    for ( i = 0; i < MAX*2; i++)
    {
        pthread_mutex_lock(&mutex);
        while ((top+1)%MAX == bottom)
        {
            printf("full! producer is waiting\n");
            //等待队不满
            pthread_cond_wait(notfull, &mutex);
        }
        top = (top+1) % MAX;
        //发出队非空的消息
        pthread_cond_signal(notempty);
        pthread_mutex_unlock(&mutex);
    }
    return (void*)1;
}
void* consume(void* arg)
{
    int i;
    for ( i = 0; i < MAX*2; i++)
    {
        pthread_mutex_lock(&mutex);
        while ( top%MAX == bottom)
        {
            printf("empty! consumer is waiting\n");
            //等待队不空
            pthread_cond_wait(notempty, &mutex);
        }
        bottom = (bottom+1) % MAX;
        //发出队不满的消息
        pthread_cond_signal(notfull);
        pthread_mutex_unlock(&mutex);
    }
    return (void*)2;
}
int main(int argc, char *argv[])
{
    pthread_t thid1;
    pthread_t thid2;
    pthread_t thid3;
    pthread_t thid4;

    int ret1;
    int ret2;
    int ret3;
    int ret4;

    pthread_create(&thid1, NULL, produce, NULL);
    pthread_create(&thid2, NULL, consume, NULL);
    pthread_create(&thid3, NULL, produce, NULL);
    pthread_create(&thid4, NULL, consume, NULL);

    pthread_join(thid1, (void**)&ret1);
    pthread_join(thid2, (void**)&ret2);
    pthread_join(thid3, (void**)&ret3);
    pthread_join(thid4, (void**)&ret4);
    return 0;
}
````
