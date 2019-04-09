# 常见的设计模式

## 单例模式

一个类有且仅有一个实例，并提供该实例的访问方法。

**实现**

隐藏类的构造函数，提供获取该类的静态成员函数。

````c++
class demo{
    private:
        demo(){}
        static demo *ins;
    public:
        static demo* get_instance();
}
demo* demo::ins = nullptr;
demo* demo::get_instance()
{
    if (!demo::ins)
    {
        //不考虑多线程
        demo::ins = new demo();
    }
    return demo::ins;
}
````

## 观察者模式

被观察者发送改变，通知观察者。

**实现**

通过接口实现。

````c++
class observer
{
    public:
        virtual void on_change() = 0;
};

class zhangsan : public observer
{
    public:
        virtual void on_change()
        {
            // do something...
        }
};

class lisi: public observer
{
    public:
        virtual void on_change()
        {
            // do something...
        }
};

class trigger
{
    private:
        vector<observer*> observer_list;
    public:
        void add_observer(observer* ob)
        {
            observer_list.push_back(ob);
        }
        void notify()
        {
            for(vector<observer*>::iterator it = observer_list.bengin();
                it != observer_list.end(); it++)
            {
                it->on_change();
            }
        }   
}

int main()
{
    trigger t;
    observer *ob1 = new(zhangshan());
    observer *ob2 = new(lisi());
    t.add_observer(ob1);
    t.add_observer(ob2);
    t.notify();
}
````

## 适配器模式

实现接口之间的转化。三个角色：

*   target: 目标接口
*   adaptee: 当前接口
*   adapter: 适配器，将当前接口，转化为目标接口

````c++
#include<iostream>
using namespace std;
 
// "ITarget"
class Target
{
public:
	// Methods
	virtual void Request(){};
};
 
// "Adaptee"
class Adaptee
{
public:
	// Methods
	void SpecificRequest()
	{
		cout<<"Called SpecificRequest()"<<endl;
	}
};
 
// "Adapter"
class Adapter : public Target
{
private:
	Adaptee *adaptee;
 
public:
	Adapter()
	{
		adaptee = new Adaptee();
	}
 
	// Implements ITarget interface
	void Request()
	{
		// Possibly do some data manipulation
		// and then call SpecificRequest  
		adaptee->SpecificRequest();
	}
};
 
 
int main()
{
	// Create adapter and place a request
	Target *t = new Adapter();
	t->Request();
 
	return 0;
}
````

## 工厂模式

用于动态获得对象实例。

**实现**

````c++
class car 
{

};

class car_factory
{
    public:
        static shared_ptr<car> create()
        {
            return make_shared<car>();
        }
}
````

## 代理模式

为了封装一个类的细节，为用户提供一个代理类，用户使用代理类，来实现对应的功能。

例如：有一个TCP通信类，他能够实现tcp通信，基于socket实现的，但是用户只想通过向目的主机
发送消息，并不想了解socket的细节，这时我们可以提供一个tcp代理类。

**实现**

````c++
class tcp
{
    private：
        string host_;
        uint16 port_;
        int sfd_;
    public:
        tcp(string host, uint16 port):host_(host), port_(port)
        {
            sfd_ = socket(...);
        }
        int connect_peer()
        {
            connect(host_, port_,...);   
        }
        int write_data(char buf[], int len)
        {
            write(sfd_, buf, len....)
        }
        
};

//用户使用tcp_proxy
class tcp_proxy
{
    private: 
        unique_ptr<tcp> tcp_ins_;
    public:
        tcp_proxy(string host, uint16 port)
        {
            tcp_ins_ = std::move(unique_ptr<tcp>(new tcp(xxx, xxx)));
        }
        int send(char buf[], int len)
        {
            tcp_ins_->connect_peer();
            tcp_ins_->write_data(buf, len);
        }
};
````

