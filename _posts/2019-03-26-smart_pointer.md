---
layout: post
title: c++智能指针
categories: c/c++
tags: 智能指针
---

# 智能指针

智能指针用于自动管理动态内存，c++中有三类只能指针：unique_ptr、shared_ptr、weak_ptr。

## unique_ptr

unique_ptr和被管理的指针只能是一对一的关系，也就是说不同的unique_ptr对象管理不同的指针，
不能多个unique_ptr对象管理同一个指针，unique_ptr对象只实现了
[移动语义](./rvalue_and_move_semantics.md)的赋值，因此unique_ptr对象之间的赋值，将
导致右值unique_ptr对象不可用。

unique_ptr在以下三种情况，会delete掉管理的指针。

1.  unique_ptr对象生命周期结束；

1.  unique_ptr对象之间赋值后，如果左值unique_ptr对象不为空，左值unique_ptr对象会先
    delete掉管理的指针，然后再接管右值unique_prt对象管理的指针，右值unique_ptr对象
    在赋值后，为空。

1.  显示调用reset成员函数。

示例：

````c++
class A 
{
public:
    A(){cout << "A is constructed!" << endl;}
    ~A(){cout << "A is destructed!" << endl;}
};

void foo(std::unique_ptr<A> pa)
{

}

void bar(std::unique_ptr<A> &pa)
{

}

int main()
{
    std::unique_ptr<A> pa1; // null unique_ptr
    std::unique_ptr<A> pa2(new A()) // point to A
    pa1 = std::move(pa2); //pa1 point to A, pa2 is null

    bar(pa1); // after function call, pa1 still point to A 
    
    foo(std::move(pa1));// after function call, pa1 is null 

    return 0;
}
````

## shared_ptr

shared_ptr和管理的指针可以是多对一的关系，也就是允许多个shared_ptr对象管理同一个指针，
这些shared_ptr对象拥有一个引用计数指针，指向同一个引用计数，shared_ptr对象之间
的赋值、参数传递等操作，将导致他们的引用计数加1，一个shared_ptr对象的生命周期结束时，
将导致他们的引用计数减1，当他们的引用计数为0时，会delete掉对应的指针。

引用计数减1 :

1.  shared_ptr对象生命周期结束

1.  显示调用reset

引用计数加1 ：

1.  shared_ptr对象之间拷贝赋值，左值对象的引用计数不为0，减1，减1后为0，先释放左值对象，
    然后右值对象的引用计数加1；

1.  将shared_ptr对象作为参数传递，但是如果以引用或者指针的形式传递，不会引起引用计数变化

实例

````c++
class A 
{
public:
    A(){cout << "A is constructed!" << endl;}
    ~A(){cout << "A is destructed!" << endl;}
};

void foo(std::shared_ptr<A> pa)
{

}

void bar(std::shared_ptr<A> &pa)
{

}

int main()
{
    std::shared_ptr<A> pa1; // null shared_ptr, use_count=0
    std::shared_ptr<A> pa2(new A()); // point to A, use_count=1
    pa1 = pa2; //both point to A, use_count=2 

    bar(pa1); // keep use_count, use_count=2 
    
    foo(pa1);// in foo, use_count=3, after foo return, use_count=2 

    //函数退出，pa1无效，use_count=1
    //pa2无效，use_count=0，释放A

    return 0;
}
````

## weak_ptr

weak_ptr相当于shared_ptr对象的引用， 它不参与shared_ptr之间的引用计数，
可以由shared_ptr对象构造，或者由shared_ptr对象赋值，可以通过lock函数获得
对应的shared_ptr对象，weak_ptr主要用来解决shared_ptr之间环形引用，导致内存泄漏的问题。

例如：

````c++
class A;
class B;

class A 
{
public:
    A(){cout << "A is constructed!" << endl;}
    ~A(){cout << "A is destructed!" << endl;}
    void set_b(shared_ptr<B> pb)
    {
        this->pb = pb;
    }
private:
    shared_ptr<B> pb;
};

class B 
{
public:
    B(){cout << "B is constructed!" << endl;}
    ~B(){cout << "B is destructed!" << endl;}
    void set_a(shared_ptr<A> pa)
    {
        this->pa = pa;
    }

private:
    shared_ptr<A> pa;
};

int main()
{
    shared_ptr<A> pa(new A()); //pa.use_count=1
    shared_ptr<B> pb(new B()); //pb.use_cout =1

    pa->set_b(pb); //pb.use_count = 2;
    pb->set_a(pa); //pa.use_count =2;

    //程序结束，引用结束都减1，还是不为0， 导致对象A,B得不到释放
}

````

将其中的一个改为weak_ptr，则能解决问题：

````c++
class A;
class B;

class A 
{
public:
    A(){cout << "A is constructed!" << endl;}
    ~A(){cout << "A is destructed!" << endl;}
    void set_b(shared_ptr<B> pb)
    {
        this->pb = pb;
    }
private:
    weak_ptr<B> pb;
};

class B 
{
public:
    B(){cout << "B is constructed!" << endl;}
    ~B(){cout << "B is destructed!" << endl;}
    void set_a(shared_ptr<A> pa)
    {
        this->pa = pa;
    }

private:
    shared_ptr<A> pa;
};

int main()
{
    shared_ptr<A> pa(new A()); //pa.use_count=1
    shared_ptr<B> pb(new B()); //pb.use_cout =1

    pa->set_b(pb); //A中为weak_ptr, 不参与引用计数, 所以pb.use_count = 1;
    pb->set_a(pa); //pa.use_count =2;

    //程序结束，pa的引用计数先减1， 为1，继续
    //pb的引用计数减1，为0，将释放pb，pb的释放会导致pb中pa失效，pa的引用计数再减1，
    //为0，释放pa，最后释放pb
}

````
