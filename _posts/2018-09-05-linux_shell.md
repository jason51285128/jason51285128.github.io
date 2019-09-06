---
layout: post
title: shell教程
categories: linux
tags: shell
---

# chapter1 简介

shell时Linux中负责用户和操作系统交互的程序，它从标准输入或者文件中读取用户命令，
然后将结果输出到标准输出或者文件，有自己的内置命令和语法规则，本质是一个解释器，使用
shell语法编写的程序叫做shell脚本。shell后来被Linux工作组标准化，常见的shell实现有sh、
bash、ksh、csh，其中bash使用最广泛，本教程适用bash环境，其他shell环境基本类似。

--------





1.	 重定向
	* 	标准输入文件描述符：0
	* 	标准输出文件描述符：1
	* 	标准错误文件描述符：2
	
	eg：
	* 	重定向输出，覆盖：ls -al > txt1.txt
	* 	重定向输出，追加：ls -al >> txt1.txt
	* 	重定向输出及错误输出： ls -al >txt1.txt 2>&1
	* 	重定向输出及错误输出空设备，即丢弃：ls -al >/dev/null 2>&1
	* 	重定向输入：more <txt1.txt

1.	管道

	连接进程，一个进程的输出，作为另一个进程的输入
	
1.	shell语法

	1.	变量
	
		默认都是字符串，不需申明，使用$对变量进行引用，
		使用双引号进行字符串扩展，使用单引号防止字符串扩展，eg：
		
		name=zcc
		
		echo "my name is $name" #my name is zcc
		
		echo 'my name is $name' #my name is $zc
		
		![linux_shell_1]({{site.baseurl}}/assets/images/linux_shell_1.png)
		
		使用“变量名=”来取消一个变量的定义，eg：
		
		name=
		
		上面的命令取消name的定义。
		
		登录linux时，系统会为我们预先定义一些变量，比如home目录，程序搜索路径，这些变量
		称为**环境变量**，这些变量都是大写的，注意linux的大小写敏感的系统，常见的环境变量有：
		* HOME
		* PATH
		* ...
		
		另外，系统还定义了一些特殊变量，让我们在脚本中处理用户传递的参数，这些变量叫做
		**参数变量**，常用的参数变量：
		* $0   #shell脚本的名字
		* $#   #shell脚本的参数个数
		* $$   #shell脚本的进程号
		* $1, $2...  #shell脚本的参数
		
	1. 	条件
	
		shell使用test命令来测试条件，符号 “[” 等同于test命令，但如果使用"["符号，还应使用"]"结尾
		支持三类条件比较：字符串比较，表达式比较，文件测试
		
		![linux_shell_2]({{site.baseurl}}/assets/images/linux_shell_2.png)
		
		![linux_shell_3]({{site.baseurl}}/assets/images/linux_shell_3.png)
		
	1. 	控制语句
		* 	条件控制语句
		
			if condition
			
			then
			
			else
			
			fi
			
			if condition
			
			then
			
			elif condition
			
			then
			
			else
			
			fi
		* 	循环语句
			
			for var in value
			
			do
			
			done
			
			while condition 
			
			do
			
			done
			
			util condition
			
			do
			
			done
			
		*	case 语句
			
			case var in
			
			pattern1 | pattern2)
			
			statements
			
			;;
			
			pattern3)
			
			statements
			
			;;
			
			pattern4 | pattern5)
			
			statements
			
			;;
			
			esac
			
		* 	命令列表
			
			&& #只有前面的命令都执行成功，才执行后面的命令
			
			|| #连续执行一系列命令，知道有一个命令执行成功，就停止执行
			
		* 	语句块
			
			使用“{}”，将多条语句合并为一个语句
	
	1.	函数
		*	函数定义
			
			functionname()
			
			{
			
			}
		*	参数传递(想调用普通命令一样调用函数)
			
			functionname para1 para2
			
			在函数内部使用参数变量，引用传递给函数的参数
		*	函数返还
			
			return
    1.	调试
		
		通过给设置shell的参数，来对shell进行调试
		