# python

## 模块

指一个python文件，包含python类，函数，变量等等，模块中的内容都是可见的，当被
import时。

使用import导入模块，使用模块名作为前缀，引用模块内容

````python
import math
math.xxxx
````

使用from...import...导入模块的部分变量，函数。这是可以直接使用导入的名称

````python
from fib import fibonacci
from fib import * #import all
````

## 模块的搜索路径


1、当前目录
2、如果不在当前目录，Python 则搜索在 shell 变量 PYTHONPATH 下的每个目录。
3、如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/。

## 包

包就是文件夹，它定义了一个由模块及子包，和子包下的子包等组成的 Python 的应用环境。

包必须包含一个__init__.py的文件，文件内容可以为空。

包的搜索路径和模块的搜索路径一致

## 字符串前缀

u表示unicode编码的字符串

````python
s = u"helloworld"
````

r表示非转义的原始字符

````python
s = r"\n\thello" # \n \t 将表示原始字符
````

b表示单字节的字符串

````python
s = b"helloworld"
````

## lambda

lambda 定义简单的匿名函数

## iterator和iterable

iterator是一种顺序容器对象，该对象实现了next()方法，next方法顺序访问容器中的值，
直到容器的结尾，抛出StopIteration异常

````python
class Iterator:

    def __init__(self, iterable)

        self.iterable = iterable

    def __iter__(self):  #iter should return self if called on iterator

        return self

    def next(self):  #Use __next__() in python 3.x

        if condition: #it should raise StopIteration exception if no next element is left to return

            raise StopIteration
````

iterable是一个实现了__iter__方法的对象，__iter__方法返回该对象的迭代器

````python
class list_iter(object):

    def __init__(self, list_data):

        self.list_data = list_data

        self.index = 0

    def __iter__(self):

        return self

    def next(self):   #Use __next__ in python 3.x

        if self.index < len(self.list_data):

            val = self.list_data[self.index]

            self.index += 1

            return val

        else:

            raise StopIteration()

class List(object):

    def __init__(self, val):

        self.val = val

    def __iter__(self):

        return list_iter(self.val)
````

函数中使用**yield**关键字，该函数将被解释成一个iterator，调用函数时，遇到yield挂起函数，
返还yield后的对象，再次调用时，接着yield后面的语句继续执行。



