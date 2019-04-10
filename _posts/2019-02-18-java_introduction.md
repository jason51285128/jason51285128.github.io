---
layout: post
title: java入门
categories: java
---

classpath 类似gopath(gopath比classpath多一层src目录)：指定.class文件的搜索路径，每个类对应一个.class文件
package和go的package一样，源文件相对classpath的路径必须和package的名字一样，例如
CLASSPATH=.
source helloworld.java
package=zhangchen.net.onething.www
则helloworld.java必须放在CLASSPATH/zhangchen/net/onething/www目录下，生成的.class文件也必须位于该目录下

在CLASSPATH 目录下执行java zhangchen.net.onething.www.helloworld 才能正常运行


