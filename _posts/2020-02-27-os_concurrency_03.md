---
layout: post
title: 并发之条件变量
categories: linux
tags: [os, linux, kernel, concurrency, conditional variable] 
---

# 并发之条件变量

线程调用wait进入睡眠，等待某些条件满足，当其他的线程改变条件变量时，调用signal
唤醒在改条件变量上等待的线程。


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
