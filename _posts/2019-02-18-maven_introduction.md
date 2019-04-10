---
layout: post
title: maven入门
categories: 编译构建
tags: maven
---

# maven入门

maven是一种java工程的构建、打包工具，具有自动化、灵活、可扩展等优秀特点，能够一次构建一个
或者多个java项目，很多开源java项目采用maven构建。

## 核心概念

### Artifact

在maven中，一个项目称之为artifact，artifact拥有{groupId, artifaceId, version}三个属性，
这三个属性都是字符串， 比如，一个叫做app的项目，其artifact属性为{"com.my.company", "myApp", "1.0.0"},
当你的项目发布到maven仓库中后，别人就可以使用artifact的这三个属性，来引用你的项目，当然你也可用通过
artifact来引用别人的项目。通过artifact的三属性就可以唯一的确定一个artifact，便于artifact之间相互引用。

### Build Lifecycle & Phase

构建过程称为build lifecycle， 在maven中有三种build lifecycle：default、clean、site， 其中default用于构建
用户定义的对象， clean用于清除构建过程中生成的文件，site用于生成项目文档。phase表示构成过程中的步骤，一个build
lifecycle由一个和多个**有序**的phase构成，当执行某一个phase时，会顺序执行该phase前面所有的phase，然后才执行该
phase，除非某一个phase失败。三种build lifecycle包含的phase如下：

![]({{site.baseurl}}/assets/images/maven1.png)

![]({{site.baseurl}}/assets/images/maven2.png)
![]({{site.baseurl}}/assets/images/maven3.png)
![]({{site.baseurl}}/assets/images/maven4.png)

![]({{site.baseurl}}/assets/images/maven5.png)

### Goal & Plugin 

phase定义了构建过程中的步骤，而这些步骤的具体实现者叫Goal，相当于phase定义了一个接口，而goal则是接口的实现者，每个
phase都绑定了一个或者多个goal，plugin是多个goal的集合，goal能独立运行，使用plugin:goal的形式应用，例如：*mvn 
dependency:copy-dependencies*， 则是运行dependency这个plugin中的copy-dependencies，该goal用于打包项目的所有
依赖，这种架构使maven拥有极高的可扩展性，我们可以改写phase绑定的goal，是maven能够构建任何我们想要的东西。默认下
每个phase都有绑定的goal，这些goal是构建java项目的，如下：

![]({{site.baseurl}}/assets/images/maven6.png)

![]({{site.baseurl}}/assets/images/maven7.png)

![]({{site.baseurl}}/assets/images//maven8.png)


### POM



--------
maven

goal : 实现特定目的的对象
plugin: goal的集合，实现特定功能
phase：构建过程中的某一个步骤，构建过程可以是一些列有序phase的集合，例如，一个构建过程可能包括如下有序
phase：预处理、汇编、编译、打包。maven执行某一个phase的时，会将上游的phase也一起执行，比如在例子中的
构建过程中执行编译phase，那么会依次执行预处理、汇编、编译这三个phase

artifact：只一个maven项目，每个artifact都有一个artifactid

pom.xml： maven项目的配置文件

archetype mechanism，创建maven工程的机制，使用一套模板来创建maven工程

build lifecycle

执行构建的步骤集合

三种build lifecycle及其组成phrase

default

    validate
    compile
    test
    package
    verify
    install
    deploy

clean

site


phase功能是由特定的goal去实现的，也就是说phase通过绑定goal，来实现其功能

goal可以脱离build lifecycle，独立运行

一个phase可以绑定一个或多个goal，也可以不绑定goal，没有绑定goal的phase是空phase，也就是没有任何功能


pom

A POM requires that its groupId, artifactId, and version be configured. These three values form the project's fully qualified artifact name

maven的配置文件，xml格式，pom中常见的元素

dependencies
developers and contributors
plugin lists (including reports)
plugin executions with matching ids
plugin configuration
resources


<exclusions>
        <exclusion>
          <groupId>com.google.protobuf</groupId>
          <artifactId>protobuf-java</artifactId>
        </exclusion>
        <exclusion>
          <groupId>org.slf4j</groupId>
          <artifactId>slf4j-api</artifactId>
        </exclusion>
        <exclusion>
          <groupId>org.apache.commons</groupId>
          <artifactId>commons-lang3</artifactId>
        </exclusion>
        <exclusion>
          <groupId>org.slf4j</groupId>
          <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
      </exclusions>
