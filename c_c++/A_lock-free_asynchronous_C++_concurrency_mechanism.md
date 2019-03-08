# 一种无锁、异步的C++并发机制

## 概述

通常在C++开发中，我们一般会使用原子操作、条件变量或者锁等方式，来进行多线程同步，但是上锁、解锁、临界区保护这
些操作，会带来较大的性能开销，下面介绍C++ 11中的一种无锁、异步的并发机制。

## future对象

future对象类似go语言中的只读channel，只有当一个future对象ready时，才能从中读取到数据，否则程序将阻塞。默认
构造的future对象是一个无效的future对象，对无效future对象进行读操作，将导致程序永久阻塞，C++ 11中提供了三个
方法来构造有效的future对象，程序只能通过这三个方法来使用future对象：

*   async

*   promise::get_future

*   packaged_task::get_future

下面我们分别说明这三种方法。

## promise对象

promise对象类似一个只能往里面写的管道，但同时它拥有一个future对象，通过future对象可以读取其中的数据，promise
对象对应多线程模型中的生产者，消费者通过promise对象的get_future方法，获取其对应的future对象，来读取promise
中的数据，从而实现一种无锁的同步机制。

**example**

````c++
#include <iostream>       // std::cout
#include <functional>     // std::ref
#include <thread>         // std::thread
#include <future>         // std::promise, std::future

void worker(std::promise<int>& prom) {
  std::cout << "worker: now sending my result : " << 10 << '\n';
  prom.set_value (10);
}

int main ()
{
  std::promise<int> prom;                      // create promise

  std::future<int> fut = prom.get_future();    // engagement with future

  std::thread th1 (worker, std::ref(prom));  // send  promise to new thread
  th1.detach();                              // start worker

  int x = fut.get();      //blocking until worker finish 

  std::cout << "main: receive result from worker : " << x << '\n';

  return 0;

}
````

## async对象

async是对异步调用的封装，类似go语言中的goroutine, async对象封装了thread对象和promise对象，
同样提供get_future方法，用于获取异步调用的结果。

**example**

````c++
#include <iostream>       // std::cout
#include <future>         // std::promise, std::future

int worker(int workerId) {
  std::cout << "worker: now sending my result : " << workerId  << '\n';
  return workerId;
}

int main ()
{
  std::future<int> fut[3];

  //start three worker 
  fut[0] = std::async (std::launch::async, worker,0);
  fut[1] = std::async (std::launch::async, worker,1);
  fut[2] = std::async (std::launch::async, worker,2);

  //now receive all worker's result
  int i ;
  for (i =0; i < 3; i++)
  {
      std::cout << "result of worker " << i << " is: " << fut[i].get() << std::endl;
  }

  return 0;

}
````
## packaged_task对象

packaged_task对象是对函数指针和promise对象的封装，可以被一个线程运行，并且提供get_future方法，用于异步取回
函数的结果。

**example**

````c++
#include <iostream>       // std::cout
#include <future>         // std::promise, std::future
#include <thread>

int worker(int workerId) {
  std::cout << "worker: now sending my result : " << workerId  << '\n';
  return workerId;
}

int main ()
{
  // define 3 task
  std::packaged_task<int(int)> tsk0(worker);
  std::packaged_task<int(int)> tsk1(worker);
  std::packaged_task<int(int)> tsk2(worker);

  // get the future object
  std::future<int> fut[3];
  fut[0] = tsk0.get_future();
  fut[1] = tsk1.get_future();
  fut[2] = tsk2.get_future();

  //start task
  std::thread th0 (std::move(tsk0),0);
  th0.detach();
  std::thread th1 (std::move(tsk1),1);
  th1.detach();
  std::thread th2 (std::move(tsk2),2);
  th2.detach();

  //now receive all worker's result
  int i ;
  for (i =0; i < 3; i++)
  {
      std::cout << "result of worker " << i << " is: " << fut[i].get() << std::endl;
  }

  return 0;

}
````

## 参考

[http://www.cplusplus.com/reference/future/](http://www.cplusplus.com/reference/future/)