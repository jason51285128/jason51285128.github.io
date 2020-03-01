---
layout: post
title: cpu虚拟化之IPC
categories: linux
tags: [os, linux, kernel, virtualize, IPC] 
---

# cpu虚拟化之IPC

Intier-Process Communication (IPC)，进程间间通信，操作系统通过该机制
让内部彼此隔离的进程可以通信。

## signal

一种无序、异步通信机制，用于进程间或者内核于进程间。
信号可能来自内部，如：执行非法指令，也有可能来自外部，如：按下ctlr+c。

### 信号的种类及产生条件

信号值|信号名|信号源
--|--|--
1|SIGHUP|本信号在用户终端连接(正常或非正常)结束时发出, 通常是在终端的控制进程结束时, 通知同一session内的各个作业, 这时它们与控制终端不再关联。登录Linux时，系统会分配给登录用户一个终端(Session), 在这个终端运行的所有程序，包括前台进程组和后台进程组，一般都属于 这个Session。当用户退出Linux登录时，前台进程组和后台有对终端输出的进程将会收到SIGHUP信号。 这个信号的默认操作为终止进程，因此前台进程组和后台有终端输出的进程就会中止。不过可以捕获 这个信号，比如wget能捕获SIGHUP信号，并忽略它，这样就算退出了Linux登录，wget也能继续下载。 此外，对于与终端脱离关系的守护进程，这个信号用于通知它重新读取配置文件。
2|SIGINT|程序终止(interrupt)信号, 在用户键入INTR字符(通常是Ctrl-C)时发出，用于通知前台进程组终止进程。
3|SIGQUIT|和SIGINT类似, 但由QUIT字符(通常是Ctrl-/)来控制. 进程在因收到SIGQUIT退出时会产生core文件, 在这个意义上类似于一个程序错误信号。
4|SIGILL|执行了非法指令. 通常是因为可执行文件本身出现错误, 或者试图执行数据段. 堆栈溢出时也有可能产生这个信号。
5|SIGTRAP|由断点指令或其它trap指令产生. 由debugger使用。
6|SIGABRT|调用abort函数生成的信号。
7|SIGBUS|非法地址, 包括内存地址对齐(alignment)出错。比如访问一个四个字长的整数, 但其地址不是4的倍数。它与SIGSEGV的区别在于后者是由于对合法存储地址的非法访问触发的(如访问不属于自己存储空间或只读存储空间)。
8|SIGFPE|在发生致命的算术运算错误时发出. 不仅包括浮点运算错误, 还包括溢出及除数为0等其它所有的算术的错误。
9|SIGKILL|用来立即结束程序的运行. 本信号不能被阻塞、处理和忽略。如果管理员发现某个进程终止不了，可尝试发送这个信号。
10|SIGUSR1|留给用户使用
11|SIGSEGV|试图访问未分配给自己的内存, 或试图往没有写权限的内存地址写数据.
12|SIGUSR2|留给用户使用
13|SIGPIPE|管道破裂。这个信号通常在进程间通信产生，比如采用FIFO(管道)通信的两个进程，读管道没打开或者意外终止就往管道写，写进程会收到SIGPIPE信号。此外用Socket通信的两个进程，写进程在写Socket的时候，读进程已经终止。
14|SIGALRM|时钟定时信号, 计算的是实际的时间或时钟时间. alarm函数使用该信号.
15|SIGTERM|程序结束(terminate)信号, 与SIGKILL不同的是该信号可以被阻塞和处理。通常用来要求程序自己正常退出，shell命令kill缺省产生这个信号。如果进程终止不了，我们才会尝试SIGKILL。
17|SIGCHLD|子进程结束时, 父进程会收到这个信号。如果父进程没有处理这个信号，也没有等待(wait)子进程，子进程虽然终止，但是还会在内核进程表中占有表项，这时的子进程称为僵尸进程。这种情况我们应该避免(父进程或者忽略SIGCHILD信号，或者捕捉它，或者wait它派生的子进程，或者父进程先终止，这时子进程的终止自动由init进程来接管)。
18|SIGCONT|让一个停止(stopped)的进程继续执行. 本信号不能被阻塞. 可以用一个handler来让程序在由stopped状态变为继续执行时完成特定的工作. 例如, 重新显示提示符
19|SIGSTOP|停止(stopped)进程的执行. 注意它和terminate以及interrupt的区别:该进程还未结束, 只是暂停执行. 本信号不能被阻塞, 处理或忽略.
20|SIGTSTP|停止进程的运行, 但该信号可以被处理和忽略. 用户键入SUSP字符时(通常是Ctrl-Z)发出这个信号
21|SIGTTIN|当后台作业要从用户终端读数据时, 该作业中的所有进程会收到SIGTTIN信号. 缺省时这些进程会停止执行.
22|SIGTTOU|类似于SIGTTIN, 但在写终端(或修改终端模式)时收到.
23|SIGURG|有"紧急"数据或out-of-band数据到达socket时产生.
24|SIGXCPU|超过CPU时间资源限制. 这个限制可以由getrlimit/setrlimit来读取/改变。
25|SIGXFSZ|当进程企图扩大文件以至于超过文件大小资源限制。
26|SIGVTALRM|虚拟时钟信号. 类似于SIGALRM, 但是计算的是该进程占用的CPU时间.
27|SIGPROF|类似于SIGALRM/SIGVTALRM, 但包括该进程用的CPU时间以及系统调用的时间.
28|SIGWINCH|窗口大小改变时发出.
29|SIGIO|文件描述符准备就绪, 可以开始进行输入/输出操作.
30|SIGPWR|Power failure
31|SIGSYS|非法的系统调用。

**注意**
1.  使用kill -l查看系统支持的信号
1.  SIGKILL与SIGSTOP是两个唯一不能忽略也不能捕获的信号
1.  SIGRTMIN之前的信号都是不可靠信号，没有信号队列，同一种信号，发送多次，可能只处理一次
1.  SIGRTMIN以后的信号是可靠信号，拥有信号队列
1.  信号没有优先级，同时收到不同的信号时，操作系统随机选择一个处理

### 信号在内核中的实现

#### 内核数据结构（位于task_struct中）

````c
long signal; //记录收到的信号，每1bit代表一种信号
struct sigaction sigaction[32]; //信号的处理函数
long blocked;			/* bitmap of masked signals */

struct sigaction
{
  /* Signal handler.  */
  #if defined __USE_POSIX199309 || defined __USE_XOPEN_EXTENDED
    union
    {          
      /* Used if SA_SIGINFO is not set.  */
      __sighandler_t sa_handler;
      /* Used if SA_SIGINFO is set.  */ 
      void (*sa_sigaction) (int, siginfo_t *, void *);
    }
    __sigaction_handler;
    # define sa_handler	__sigaction_handler.sa_handler
    # define sa_sigaction	__sigaction_handler.sa_sigaction
  #else
    __sighandler_t sa_handler;
  #endif

  /* Additional set of signals to be blocked.  */
  __sigset_t sa_mask;

  /* Special flags.  */
  int sa_flags;

  /* Restore handler.  */
  void (*sa_restorer) (void);
};
````

#### 内核信号处理

内核在系统调用返还前，检查进程是否有未阻塞的信号，如果有，则检查信号的处理函数，
如果是ingore，则直接返还，
如果是默认处理，则调用默认处理函数，然后返还，
如果是用户指定的函数，则通过修改中断返回的地址和用户栈，将中断返还到用户定义的函数中，信号处理后，
再返还到用户系统调用处



## pipe

自带互斥和同步，面向字节流，通过VFS提供接口

### 匿名管道(pipe)

用于具有亲缘关系的进程通信，父进程创建管道，获得管道的读描述符和写描述符，然后创建
子进程，接着通信双方各自关闭一个描述符，发送数据的一方保留写描述符，读数据的一方保留，
读描述符，当pipe的所有读写描述符都关闭时，内核中的pipe对象被销毁，pipe使用*pipe*函数
创建。
		
### 命名管道(fifo)

fifo除了创建和打开方式与pipe不一样，其IO特性和pipe一样，FIFO可用于任意进程间通信，
它对应文件系统上的一个文件，但不会发生正真的磁盘操作，只是提供一个访问点，供任意
进程使用，进程能够像打开普通文件一样打开fifo来和别的进程通信，**但是FIFO必须被双向
打开，以读的方式打开FIFO，会阻塞，直到有FIFO的写描述符被打开，反之亦然**。

###	pipe的IO特性

对一个所有写描述符都关闭的管道进行读操作，会返还0字节，就像读到文件末尾；对一个所有
读描述符都关闭的管道进行写操作，会收到SIGPIPE信号，默认会把进程杀死

pipe是一个带缓冲区的channel, 在内核中的实现为一段环形缓冲区，POSIX规定至少为512，
Linux中可以用ulimit设置。

对管道的读操作一定时原子的，读一个空管道，会阻塞直到数据可读。
		
写管道，如果写的长度小于等于BUF_FIFO则写入是原子的，如果是同步写，将阻塞，直到所有的
数据写入完成，如果是异步的，如果管道剩余空间足够，则数据被写入，如果管道剩余空间不够，
则不执行写入操作，直接返还失败。但当写入的长度大于BUF_FIFO，则不保证写入为原子操作，
可能多个进程会交错写入。

### 管道API

1.  匿名管道

    ````c
    int pipe(int pipefd[2]);
    ````

1.  命名管道

    ````c
    int mkfifo(const char *pathname, mode_t mode);
    ````

## Message Queues

带优先级的链表，同优先级的消息，具有FIFO特性，自带互斥和同步，通过VFS提供接口

### API

````c
mq_close(3)          
mq_getattr(3)        
mq_notify(3)         
mq_open(3)           
mq_receive(3)        
mq_send(3)           
mq_setattr(3)        
mq_timedreceive(3)   
mq_timedsend(3)      
mq_unlink(3)         
````

## Semaphores

非负整数，原子操作，原子的减1（wait）或者加1（post），当值为0时，减1将导致阻塞

### API

````c
sem_init
sem_open
sem_post
sem_wait
sem_close
````

## Shared Memory

通过映射页表实现，需要自己实现互斥和同步