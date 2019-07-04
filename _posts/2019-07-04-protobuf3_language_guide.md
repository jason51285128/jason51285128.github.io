---
layout: post
title: proto3语法指南
categories: 数据格式
tags: protobuf
---

# proto3语法指南

本文提供proto3(google protobuf v3)的语法指南，细节参看：
*https://developers.google.com/protocol-buffers/docs/proto3*

## 定义消息

````protobuf
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
}
````

*   首行指定protobuf版本，**必须放在首行，并且不能空行、空格、注释**
*   消息中的元素叫filed，一个field由四部分组成{[rule], type, name, number}，其中
    rule时可选的，其他三个必须有，number用于在序列化后的二进制流标识该filed，在一个
    消息定义中，number不能重复，其中1~15只占1字节，其他占两个或者4个字节，为了节省体积，
    一个消息的filed数量尽量控制在16个以下
*   可以是由reserve关键字，保留filed number，便于以后修改massage，而不影响兼容性
*   message可以嵌套，嵌套的message可以使用**parent.type**访问
*   可以使用**enum**定义常量
*   可以使用**import**关键字访问其他proto文件定义的消息
*   可以使用map关键字定义key-value对象
*   可以使用package定义命名空间

## 定义rpc服务

使用service关键字定义rpc服务

````protobuf
service SearchService {
  rpc Search (SearchRequest) returns (SearchResponse);
}
````




















