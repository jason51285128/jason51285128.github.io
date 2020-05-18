---
layout: post
title: JavaScript教程 
categories: web
tags: javascript
---

# JavaScript

## 概述

一种解释性脚本语言，用于web客户端和服务端，客户端的解释器是浏览器，服务端的解释器有:
V8、spidermonkey、Chakra等等，这些解析器满足ECMAScript标准，都是ECMAScript的一种实现。

JavaScript是ECMAScript的一种实现，同时提供了DOM和BOM的API。

## 语法基础

### 变量

无需事先声明，使用未事先申明的变量必须初始化，区分大小写，弱类型，结尾分号可用可无，注释和c相同

````javascript
a = "hello world!";
a = 5;
a = true;
b = 1; // 必须初始化
console.log(a);
````

变量作用域：

* 全局变量：函数之外声明，作用域为当前文件；
* 局部变量：函数内声明，作用域为当前函数，函数可以嵌套，内部函数可以访问外部函数的变量，反之不行
* 语句块变量：使用let申明，作用域为当前语句块；

####  变量类型

**原始值**
* undefined
* null
* boolean
* number
* string

**引用值**
* object

### 函数

形参不必声明类型，无需申明是否所有返还值
可以使用argument对象，动态访问形参，这样可以隐藏形参列表，实现多态
函数也是一种引用类型

````javascript
function functionName(arg0, arg1, ... argN) {
  statements
}
````

````javascript
var a = function(s) {
  console.log(s);
}
b = a;
b('hello world');
````

## 面向对象

* 对象的创建和销毁都发生在运行时;

* 类的方法和属性都是public的;

* new语义：使用prototype创建对象，然后调用对象的构造函数;

* es中对象的属性和方法是在运行时动态修改的;

* 使用this指向调用该方法的对象

### 对象分类

* 本地对象：独立于宿主环境的 ECMAScript 实现提供的对象;
* 内置对象：也属于本地对象，但是在程序执行前，就已经被解释器实例化;
* 宿主对象：由解释器提供的对象;

### 原型链

* prototype: 原型，存在于函数对象，是函数对象的一个属性，属性值是一个对象;

* __proto__: 原型指针，存在于每一个对象，指向对象的父原型，由于父原型也是一个对象，
所以父原型中也有__proto__,从而形成一个原型链，根原型的__proto__为空;

* constructor: 构造属性，存在于每一个对象，指向构造该对象的函数对象, prototype对象的
constructor默认为本身的函数对象，除非显示改写prototype对象

* new: 以函数对象的原型为模板，创建新对象，然后调用该函数对象

  ````javascript
  o = new Constructor(); //等价于：
  {
    o = {};
    o.__proto__ = Constructor.prototype;
    o.constructor = o.__proto__.constructor;
    Constructor.call(o);
  }
  ````


### 创建和销毁

````javascript
o = new Object(); //create
o = null; // destroy
````

### 定义

````javascript
function Person(name, age, sex) {
  this.name = name;
  this.age = age;
  this.sex = sex;
}

Person.prototype.getName = function() {
  return this.name;
}

Person.prototype.getAge = function() {
  return this.age;
}

Person.prototype.getSex = function() {
  return this.sex;
}

Person.prototype.setName = function(name) {
  this.name = name;
}

Person.prototype.setAge = function(age) {
  this.age = age;
}

Person.prototype.setSex = function(sex) {
  this.sex = set;
}

zhangsan = new Person("zhangsan", "18", "male");
console.log(zhangsan.getName());
zhangsan.setAge(19);
console.log(zhangsan.getAge());
````

### 继承和覆盖

````javascript
function Dog(name){
  this.name = name;
}

//不要再原型中添加属性，否则会出现属性遮蔽
Dog.prototype.bark = function(){
    console.log(this.name + " bark");
}

// Haski 的构造函数
function Haski(name){
  // 继承Dog的构造函数，相当于继承基类的属性
  Dog.call(this, name);
  // 可以补充更多Haski的属性
  this.type = "Haski";
};

//相当于只创建Dog对象，而不调用其构造函数
// o = new Object();
// o.__proto__= Dog.prototype;
// Haski.prototype = o;
Haski.prototype = Object.create(Dog.prototype);

// 重置Haski实例的构造函数为本身
Haski.prototype.constructor = Haski;

// 可以为子类添加更多的方法
Haski.prototype.say = function(){
  console.log("I'm " + this.name);
}
var ha = new Haski("Ha");
````


## module

每个文件相当于一个module
module 通过exports对象，暴露方法或者对象，module中的变量是私有的，对外不可见
module 通过require被引用

````javascript
//module原型
module = {
  exports: {

  }
};
exports = module.exports;

//quire 原型
function require() {
  return module.exports;
}
````

import 相当于require('moduleid').default

````javascript
import vue from 'vue' //相当于
var vue = require('vue').default
````

## Promise

代表一个异步操纵和它的结果，当操作完成时（成功或者失败），执行回调。







## vue 

创建一个vue实例，只需再vue构造函数中传入一个对象即可
组件也是一个vue实例，所以组件需要导出一个对象，以供动态创建vue对象

[vue中name的作用](https://blog.csdn.net/Uookic/article/details/80420472)

## 参考连接

* [原型链](https://segmentfault.com/a/1190000004700001)
* [继承](https://segmentfault.com/a/1190000003017751)

## 严格模式

使用use strict声明严格模式，[严格模式介绍](https://www.runoob.com/js/js-strict.html)

## require加载规则
使用require来加载文件时可以省略扩展名。比如require('./module')，则对应的加载顺序为，

1.  按js文件来执行（先找对应路径当中的module.js文件来加载）
1.  按json文件来解析（若上面的js文件找不到时，则找对应路径当中的module.json文件来加载）
1.  按照预编译好的c++模块来执行（寻找对应路径当中的module.node文件来加载）
1.  若参数字符串为一个目录（文件夹）的路径，则自动先查找该文件夹下的package.json文件，然后再再加载该文件当中main字段所指定的入口文件。（若package.json文件当中没有main字段，或者根本没有package.json文件，则再默认查找该文件夹下的index.js文件作为模块来载入。）


## 语法糖

当直接使用变量的变量名作为 key 时，可以直接省略 key
````javascript
var x1='hello'
var x2='world'
var o = {
  x1,
  x2
}
console.log(o)
````

## promise

表示一个异步操作，这个操作要么成功，成功则返还一个对象，要么失败，失败则返还原因，
成功时，叫做resolve，对象，失败时，叫做reject，拒绝。

1.  构造

   ````javascript
   var promise1 = new Promise(function(resolve, reject){
     //异步操作，通常为io操作
     if (success) {
       resolve(object)
     } else {
       reject(err)
     }
   });
   ````
2. 使用

  生产者负责对promise经行resolve或者reject
  消费者设置回调

  ````javascript
  const promise1 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve('foo');
  }, 300);
});

promise1.then(function(value) {
  console.log(value);
  // expected output: "foo"
});

console.log(promise1);
// expected output: [object Promise]
  ````

3. 嵌套 Promise 是一种可以限制 catch 语句的作用域的控制结构写法。明确来说，嵌套的 catch 仅捕捉在其之前同时还必须是其作用域的 failureres，而捕捉不到在其链式以外或者其嵌套域以外的 error。如果使用正确，那么可以实现高精度的错误修复。 

````javascript
doSomethingCritical()
.then(result => doSomethingOptional()
  .then(optionalResult => doSomethingExtraNice(optionalResult))
  .catch(e => {console.log(e.message)})) // 即使有异常也会忽略，继续运行;(最后会输出)
.then(() => moreCriticalStuff())
.catch(e => console.log("Critical failure: " + e.message));// 没有输出
````

[参考](https://zhuanlan.zhihu.com/p/29783901)

## 本地存储

1.  sessionStorage
  
    临时保存同一窗口(或标签页)的数据，在关闭窗口或标签页之后将会删除这些数据

1.  localStorage
   
   浏览器窗口关闭后还保留数据

##  函数的默认参数和可选参数

1.  默认参数

````javascript
f = function (a, b=1) {
  console.log(a,b)
}
````

1. 可选参数

````javascript
f = function (a, b=1, options) {
  console.log(a,b,options.c || 0)
}
````

## 遍历对象

````javascript
//1
obj = {a: 1, b:2, c:3}
Object.keys(obj).forEach(function(k){
  console.log(obj[k])
})

//2
for (k in obj) {
  console.log(obj[k])
}
````


## 箭头表达式

(param1, param2, …, paramN) => expression
//相当于：(param1, param2, …, paramN) =>{ return expression; }