---
layout: post
title: 4步使用git仓库跨平台同步文件 
categories: 工程工具
tags: git
---

# 4步使用git仓库跨平台同步文件

开发过程中，我们经常在Windows下编辑代码，在Linux下编译调试，这样就需要经常将Windows下修改的代码，同步到编译
环境，然后调试，通常的做法是：

1.  使用scp，逐个拷贝文件
1.  使用rz，上传文件
1.  使用个人远程git仓库，中转
1.  ...

第一种和第二种方法，假如你修改了多个文件，并且这些文件离散在不同的路径，那么这两种方法显然就会比较吃力，第三种
方法需要中转，也不是很优雅。下面介绍一种使用git仓库的方案，直接进行文件同步。


## 依赖

1.  Linux上开启ssh服务
1.  Linux和Windows下均安装git客户端

## 操作

1.  Linux下新建目录*xxx.git*，例如：test.git，也可以将原来的目录改成：*xxx.git*，目录的名字一定要以**git**作为后缀；

    ![]({{site.baseurl}}/assets/images/step1.png)

1.  在test.git目录下，执行：

    ```shell
    git init

    git config receive.denyCurrentBranch warn
    ```

1.  在windows中，将上面的仓库添加到本地git仓库中；

    上面仓库的url格式为：username@host:path_of_test.git，例如：root@192.168.126.221:/root/zhangchen/test.git

    在windows下的git仓库中，执行：

    ![]({{site.baseurl}}/assets/images/step3.png)

1.  现在你就能够直接将window下的代码直接push到linux下了，然后在Linux下checkout出来了

    在window下push:

    ![]({{site.baseurl}}/assets/images/step4.1.png)

    在Linux下checkout:

    ![]({{site.baseurl}}/assets/images/step4.2.png)

    使用这种方法同步文件，就像操作两个git仓库，所有git的优点都能保持，方便，便捷！

