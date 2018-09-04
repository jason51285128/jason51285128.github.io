# go学习笔记

## go的工作区

go的工作区即存放go代码的目录，用go开发项目都是统一放到工作区目录下，配置go提供的
工具，进行编译、构建。

go的工作区由环境变量GOPATH指定，默认的工作区为“$HOME/go”，可以通过修改GOPATH变量
修改工作区，**修改后，记得使用export导出，否则不生效**


### go工作区的目录结构

go的工作区从上到下分为workplace、repository、package，其中workplace相当于根目录，
可以包含多个repository，每个repository可以包含多个package，直接包含go源代码的目录，
则为package

一个workplace下，只包含bin和src目录，其中bin中包含由go工具生成的可执行文件，如图所示

![go工程目录结构](media/go-1.png)

代码的结构按照go的工作区规则部署，就可以使用go install, build等工具，进行构建

## Import Path

import关键字用于从指定的目录下（可以是本地，也可以是远程的），导入其他package，供程序
使用，i.e: import zhangchen.com/my/func_lib，用于导入$GOPATH/src/zhangchen.com/my/func_lib
下的func_lib包，可执行程序所在的包名一定是main，程序的入口为main包中的main函数

使用import导入某个包时，会执行该包下所有文件中的init函数，**如果只想执行某个package下的init
函数，则可以在import关键字后加 "_"。 **

## 包

go程序以包为单位组织程序，包是最小的编译单元。程序的执行入口为main包的main函数，其他包在编译时
只会生成对象文件。

### 包中标识符的可见性

*	以大写字母开头的标识符（变量、常量、类型、函数等等），能够别外部的包所引用，相当于public；
*	以小写字母开头的标识符（变量、常量、类型、函数等等），只在包内可见，相当于private；

--------

## go语法

### 变量

1.	变量的申明

	go是一种强类型语言，因此在申明变量时，需要指定变量类型，也不允许不同类型之间的变量相互赋值，
	使用**var**关键字声明变量，格式为**var name type = initialvalue**，e.g:
	```go
	var age int = 3
	```
	对于没有初始值的变量，go会自动赋值成“0值”，go具有**类型推断**特性，对于**有初始值**的变量，go能
	自动推断类型，在申明的时候，可以省略变量类型，e.g:
	```go
	var age = 3 //age为int类型
	```
	可以一条语句声明多个变量，e.g:
	```go
	var age, name = 13, "zhangsan"
	```
	也可以同时包含初始化，和初始化的变量，e.g:
	```go
	var (
		age = 13
		name = zhangsan
		height int
	)
	```
	
	**简短申明**
	
	使用**:=**，来完成变量的声明和初始化，但是必须满足下面两个条件：
	
	*	:= 操作符左边的所有变量都必须有初始值，否则抛出异常
	*	:= 操作符的左边至少有一个变量是尚未声明的，否则抛出异常
	*	只能在函数内部
	
1.	变量的作用域

	go只有两种作用域，包级作用域和函数级作用域。
	
1.	变量的生命周期
	
	包级作用域的变量，声明周期随进程，函数级作用域的生命周期直到没有引用，e.g:
	```go
	var global *int

	func f() {
		var x int
		x = 1
		global = &x
	}
	
	func g() {
		y := new(int)
		*y = 1
	}
	```
	
	变量global的生命周期随进程，注意**函数f中的变量x，在函数退出时，依然存在，因为通过global能够访问到他**，
	这种现象叫**变量逃逸**， 大量的变量逃逸，会阻止对短生命周期对象的垃圾回收，从而可能影响程序的性能，
	函数g中的变量y，在函数推出后，生命周期结束，所以go可以把该变量分配到栈上，虽然
	是通过new申明的，上面的例子说明，函数级变量的生命周期不一定随函数。
	
	go具有gc特性，不需要手动管理内存。
	
1.	数据类型

	go语言的数据类型可分为4大类：
	
	1.	**布尔型：**布尔型的值只可以是常量 true 或者 false。一个简单的例子：var b bool = true。
	
	2.	**数字类型：** 整型 int 和浮点型 float32、float64，Go 语言支持整型和浮点型数字，并且原生支持复数，其中位的运算采用补码。
	
		![数字类型1](media/go-2.png)
		![数字类型2](media/go-3.png)
		![数字类型3](media/go-4.png)
		
	3.	**字符串类型：**字符串就是一串固定长度的字符连接起来的字符序列。Go的字符串是由单个字节连接起来的。Go语言的字符串的字节使用UTF-8编码标识Unicode文本。
	
	4.	**派生类型：**

		*	指针类型（Pointer）
		
		*	数组类型
		
			声明：var name [len]type
			```go
			var a [5]int
			
			//define and init
			var b = [5]int{1, 2, 3, 4, 5}
			
			//define and discard len
			var c = []int{1, 2, 3, 4, 5}
			```
			
		*	结构化类型(struct)
		
		*	Channel 类型
		
		*	函数类型
		
		*	切片类型
		
		*	接口类型（interface）
		
		*	Map 类型
	
	
--------

### 常量

在编译期间，就能确定的值，一旦定义就不能改变

定义：

const var [type] = value

带初始值的常量可以不定义类型，由类型自动推导完成

可以使用括号，一次定义多个常量，例如：

```go
const (
a1 int = 1
a2 int 
a3 = 1.1
)
```

#### 预定义常量

*	true
*	false
*	iota (初始值为0，每调用一次加1，知道遇到const关键字，被重新置0)

#### 枚举

go中的枚举通过const实现，例如：

const (
    Monday = iota
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
    Sunday
)

--------

### 程序流控制

1.	条件语句

	**if 语句**

	*条件语句不需要使用括号将条件包含起来()；

	*无论语句体内有几条语句，花括号{}都是必须存在的；

	*左花括号{必须与if或者else处于同一行；

	*在if之后，条件语句之前，可以添加变量初始化语句，使用;间隔；

	*在有返回值的函数中，不允许将“最终的”return语句包含在if…else…结构中，否则会编译失败：
	
	```go
	func example(x int) int{
		var result int
		if x == 0 { 
			result = 5 
		} else { 
			result = x 
		} 
		return result
	}
	```
	
	**switch语句**
	
	*	左花括号{必须与switch处于同一行；
	*	条件表达式不限制为常量或者整数；
	*	单个case中，可以出现多个结果选项；
	*	Go语言不需要用break来明确退出一个case；
	*	只有在case中明确添加fallthrough关键字，才会继续执行紧跟的下一个case；
	*	可以不设定switch之后的条件表达式，在此种情况下，整个switch结构与多个if…else…的逻辑作用等同。
	
	```go
	switch { 
    case 0 <= Num && Num <= 3: 
        fmt.Printf("0-3") 
    case 4 <= Num && Num <= 6: 
        fmt.Printf("4-6") 
    case 7 <= Num && Num <= 9: 
        fmt.Printf("7-9")
	}
	```
	
1.	循环控制

	```go
	for i:=0; i < 10; i++ {
		fmt.Println(i)
	}
	
	sum := 10
	for {
		sum++
		if sum > 10｛
			break
		}
	}
	
	a := []int{1, 2, 3, 4, 5, 6}
	for i, j := 0, len(a) – 1; i < j; i, j = i + 1, j – 1 { 
		a[i], a[j] = a[j], a[i] 
	}
	```
	
	```go
	for j := 0; j < 5; j++ { 
		for i := 0; i < 10; i++ { 
			if i > 5 { 
				break JLoop 
			} 
			fmt.Println(i) 
		} 
	} 
	JLoop:
	```

--------

### 函数

函数的定义：

func func_name(parameters...) [return_type] { }

注意一下几点：

*	函数可以不返还值

*	函数可以返还值可以大于一个，可以使用“_”，忽略其中的返还值， 例如：
	```go
	func do_some (p int) (int, error) {
	}
	
	//接受全部返还值
	i, e = do_some(1);
	
	//忽略error
	i1, _ = do_some (2);
	```
--------
### 方法和接口

#### 方法

一个拥有receiver的函数，相当于面向对象的成员函数，其中receiver可以是基本型，也可以是自己定义的类型

定义：func (r ReceiverType) funcName(parameters) (results)

#### 接口

只定义方法名，而不实现方法

定义：

```go
type interfaceName interface {  
    //方法列表  
}  
```

接口也是一种类型，即实现了接口所定义方法的对象，支持将具体的对象赋值给
接口变量，或者在接口变量之间赋值，例如：

```go
type animal interface {
	speak()
}

type dog struct {
	//define someting...
}

type cat struct {
	//define someting...
}

func (d *dog) speak() {
	//do someting...
}

func (c *cat) speak() {
	//do someting...
}

//将对象赋值给接口
var i1 animal = new(dog);

//将接口变量赋值给接口
var i2 animal = i1;
```

将一个接口变量赋值给另一个接口变量，注意一下几点：

*	只要两个接口拥有相同的方法列表（与次序无关），即是两个相同的接口，可以相互赋值。
*	接口赋值只需要接口A的方法列表是接口B的子集（即假设接口A中定义的所有方法，都在接口B中有定义），
	那么B接口的实例可以赋值给A的对象，即子接口的对象，可以赋值给父对象。
	
**接口查询**

go支持查询接口变量对应的接收者类型，例如：

```go
package main
import "fmt"

type IPeople interface {
        GetName() string
 }

type IPeople2 interface {
        GetName() string
        GetAge() int
 }

type person struct {
        name string
}
func (p *person) GetName() string {
        return p.name
}
type person2 struct {
        name string
        age  int
}
func (p *person2) GetName() string {
        return p.name
}
func (p *person2) GetAge() int {
        return p.age
}
func main() {
        //p不可以转化为IPeople2接口，没有实现options.IPeople2接口中的GetAge()
        var p IPeople = &person{"jack"}
        if p2, ok := p.(IPeople2); ok {
                fmt.Println(p2.GetName(), p2.GetAge())
        } else {
				//打印此行
                fmt.Println("p不是Ipeople2接口类型")
        }
		
        //p2可以转化为IPeople接口，因为实现了options.IPeople接口的所有方法
        var p2 IPeople2 = &person2{"mary", 23}
        if p, ok := p2.(IPeople); ok {
				//打印此行
                fmt.Println(p.GetName())
        }
		
		var p2 IPeople2 = &person2{"mary", 23}
        if p, ok := p2.(*person2); ok {
				//打印此行
                fmt.Println(p.GetName())
        }

       var pp IPeople = &person{"alen"}
        if pp2, ok := pp.(*person); ok {
				//打印此行
                fmt.Println(pp2.GetName()) //pp接口指向的对象实例是否是*person类型,*不能忘
        }
     switch pp.(type) {
        case IPeople:
				//打印此行
                fmt.Println("IPeople") //判断接口的类型
        case IPeople2:
                fmt.Println("IPeople2")
        default:
                fmt.Println("can't found")
    }
    var ii interface{} = 43 //默认int类型
    switch ii.(type) {
        case int:
				//打印此行
                fmt.Println("int")
        default:
                fmt.Println("can't found")
    }
}
```
--------

### 并发编程






