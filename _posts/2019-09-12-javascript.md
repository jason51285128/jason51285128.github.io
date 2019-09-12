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

import 和 require 有同样的效果

## vue 

创建一个vue实例，只需再vue构造函数中传入一个对象即可
组件也是一个vue实例，所以组件需要导出一个对象，以供动态创建vue对象


## 参考连接

* [原型链](https://segmentfault.com/a/1190000004700001)
* [继承](https://segmentfault.com/a/1190000003017751)