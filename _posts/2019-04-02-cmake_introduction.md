---
layout: post
title: cmake入门
categories: cmake
---

# cmake入门

逻辑目标构成

二进制目标

可执行文件和库

add_executable

add_library


target_link_libraries


command

variable

function

policy

1.  相当于一个feature toggle，每个policy的值可是时NEW或者OLD，
    当一个新版的cmake，带来了某一个新的特性时，
    对于以前写的cmake脚本，会产生一个告警，这时可以手动将policy设置成OLD，这样
    cmake还是按照旧的规则解析cmake脚本
