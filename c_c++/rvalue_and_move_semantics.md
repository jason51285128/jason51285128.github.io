# c++11: 右值引用和移动语义

## 什么是移动语义

假设类X拥有一项资源的指针，例如堆内存，STL中很多容器，便是如此:

````c++
class X 
{
    public:
        char * p_;
        size_t len;
}
````

现在我们需要实现两个X对象之间的赋值操作，通过重载操作符=实现：

````c++
X& X::operator= (const X &x)
{
    //step1. 销毁this->p_指向的内存；
    if (!this->p_)
    {
        delete [] this->p_;
    }
    
    //step2. 重新申请内存
    this->p_ = new char[x.len]

    //step3. 复制x的内容
    memcpy((void*)this->p_, (void*)x.p_, x.len);
}
````

这样的缺陷是，假如对象X拥有的堆内存很大，那么X对象之间赋值操作效率会较低，销毁旧内存，
申请新内存，拷贝内存内容，这些操纵都需要较长时间完成，类似的情形还出现的拷贝构造函数中。

一种更有效的办法是，直接交换两个X对象的成员指针：

````c++
X& X::operator= (const X &x)
{
    //直接交换指针
}
````

这就是移动语义：**在对象赋值和拷贝构造函数中，直接交换对象的资源。**

## 右值引用

在移动语义下，有一个明显的问题：因为移动语义实质是交换，所以我们会改变原对象的值，导致
原来的对象不可用！例如：

````c++
//类X实现了移动语义
X x1;
...
X x2;
...
x1 = std::move(x2);
````

经过赋值后，x2原来的值将不可用，但是出于兼容的考虑，我们希望保留复制的语义，由用户选择
是用复制语义还是用转移语义，通过重载=操作符实现：

````c++
X& X::operator= (const X &x)
{
    //复制语义
}

X& X::operator= (<mystery type> x)
{
    //转移语义
}
````

复制语义我们使用引用类型，为了添加转移语义，需要定义一种新的类型，c++11中这种类型叫做
右值引用，用&&表示，普通的引用叫做左值引用，几点说明：

*   右值引用，并不一定只能做右值，本质还是引用，一样可以做左值，只是在实现了
    移动语义的拷贝构造函数和赋值操作符中，会改变右值引用对应对象的值，导致右值引用
    对应对象的原值不可用；

    ````c++
    int a = 1;
    int &&b = (int &&)a;
    b = 3;
    cout << "b=" << b << endl;
    cout << "a=" << a << endl;

    //输出
    b=3
    a=3
    ````

*   任何对象都可以通过std::move()函数，返回该对象的右值引用，包括立即数，std::move
    其实就是进行[静态类型转换](./type_conversion.md)；

    ````c++
    #include <utility>

    int a = 1;
    int &&b = move(a);
    b = 3;

    int &&c = move(1)
    c=3

    cout << "b=" << b << endl;
    cout << "a=" << a << endl;
    cout << "c=" << c<< endl;

    //输出
    b=3
    a=3
    c=3
    ````

*   右值引用本质是引用，不能为空，必须初始化，并且初始化后，不能改变引用的对象。

## 什么时候使用右值引用

### 临时对象

考虑stl中vector的实现，当vector中的内存不够时，vector会将自己的内存扩大一倍，传统的
实现逻辑是：

````c++
//伪代码
void vector::resize()
{
    //step1 使用一个临时对象，保存当前内容，复制语义, 复制一次
    vector tmp(*this);

    //step2 销毁当前内容
    this->clear()

    //step3 重新申请内存
    this->p = new ...

    //step4 拷贝临时对象, 复制两次
    *this=tmp;
}
````
这种做法，会带来临时对象的冗余复制，使用右值引用，进行优化：

````c++
//伪代码
void vector::resize()
{
    //step1 移动构造
    vector tmp(std::move(*this));

    //step2 重新申请内存
    this->p = new ...

    //step3 拷贝临时对象，只复制一次
    *this=tmp;
}
````
所以当需要临时对象时，使用右值引用。

### 完美转发

所谓[完美转发](https://blog.csdn.net/liujiayu2/article/details/49279419)简单的说，
就是在泛型编程中，当传入的参数是左值引用时，调用左值对应的函数，当传入的参数时右值引用时，
调用右值引用对应的函数。

c++完美转发是建立在引用叠加规则上的：

*   A& & => A&
*   A& && => A&
*   A&& & => A&
*   A&& && => A&&

例如有个工厂函数：

````c++
template<typename T, typename Arg> 
shared_ptr<T> factory(Arg&& arg)
{ 
  return shared_ptr<T>(new T(std::forward<Arg>(arg)));
}

//std::forward的定义如下：
template<class S>
S&& forward(typename remove_reference<S>::type& a) noexcept
{
  return static_cast<S&&>(a);
}
template<class S>
S&& forward(typename remove_reference<S>::type&& a) noexcept
{
  return static_cast<S&&>(a);
}

//std::remove_reference为C++0x标准库中的元函数，其功能为去除类型中的引用。
std::remove_reference<U&>::type => U
std::remove_reference<U&&>::type => U
std::remove_reference<U>::type => U

````

假如我们传入左值引用：

````c++
//假如X和A是两种类型
X x;
factory<A>(x);

//展开
shared_ptr<A> factory(X& && arg)
{
    return shared_ptr<A>(new A(std::forward<X&>(arg)))
}

X& && forward(typename remove_reference<X&>::type& a) noexcept
{
  return static_cast<X& &&>(a);
}

// 按照引用叠加原则，得到
shared_ptr<A> factory(X& arg)
{
    return shared_ptr<A>(new A(std::forward<X&>(arg)))
}

X& forward(X& a) noexcept
{
  return static_cast<X&>(a);
}
````

假如我们传入右值引用：

````c++
X foo();
factory<A>(foo());

//展开
shared_ptr<A> factory(X&& && arg)
{
    return shared_ptr<A>(new A(std::forward<X&&>(arg)))
}

X&& && forward(typename remove_reference<X&&>::type&& a) noexcept
{
  return static_cast<X&& &&>(a);
}

// 按照引用叠加原则，得到
shared_ptr<A> factory(X&& arg)
{
    return shared_ptr<A>(new A(std::forward<X&&>(arg)))
}

X&& forward(X&& a) noexcept
{
  return static_cast<X&&>(a);
}
````

所以在泛型编程中，右值引用加引用叠加，可以解决完美转发问题。

## 参考

1.  [https://www.cnblogs.com/forcheryl/p/3983901.html](https://www.cnblogs.com/forcheryl/p/3983901.html)




