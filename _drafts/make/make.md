# make

## Overview

构建工具，使用makefle来定义规则、目标，以此自动推导一个工程中哪些文件需要编译。

## makefile

makefile用于知道make程序进行构建，makefile主要包含：显示规则、隐士规则、变量定义、指令、注释。

*   显示规则：明确地指定如何构建一个目录，需要显示的指定：target、prerequisites, directives；
*   隐士规则：make自动推导如何构建目标；
*   变量：makefile支持变量定义；
*   指令：任何用于构建目标的操作；
*   注释：使用*#*
