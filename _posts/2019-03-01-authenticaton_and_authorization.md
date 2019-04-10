---
layout: post
title: 认证和鉴权
categories: security
tags: [authentication, authorization]
---

# Authentication and Authorization

## 概述

处于安全考虑，一般的系统都会做认证(authentication)和鉴权(authorization)，认证，即确认用户是否为系统的有效用
户（who you are），鉴权，即确认给定有效的用户是否有访问某项资源的权限（what you can do）。

![]({{site.baseurl}}/assets/images/Authentication-and-Authorization.png)

## Authentication

对于一个认证系统，需要考虑如何标识用户(identity)以及如何设计用户的凭据(credential)，标识用户一般使用用户名，
凭据根据系统安全级别， 可分为三大类：

1.  单因子认证

    就是最常见的用户名/密码认证系统，这里的凭据只有密码这一个因素，属于最简单的认证系统。

1.  双因子认证

    例如手机银行转账服务，需要用户登陆后，然后再输入转账密码，这里的凭据出来用户密码外，还有一个只有该用户知道
    的转账密码，这类认证系统，叫做双因子认证。

1.  多因子认证

    例如手机银行的大额转账服务，需要用户密码加转账密码加动态密码，这里的动态密码相当一个session key，只在一定
    的时间段内有效，这类认证系统，叫做多因子认证。 

## Authorization

对于鉴权系统，需要定义系统的权限类型，例如对于一个文件系统，有读、写、执行等权限类型，定义系统权限类型后，需要
设计如何授予用户权限，可以基于用户、基于角色等等。ACL（access control list）是最常用的鉴权系统。
    
