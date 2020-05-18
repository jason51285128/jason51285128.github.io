## Dockerfile

### 上下文

指构建环境，为目录和URL，构建环境会自动递归，最好以空目录作为上下文，并将Dockerfile
保留在该目录中. 仅添加构建image所需的文件，也就是构建环境应该尽量小，因为构建过程首先
要将构建上下文传输给Docker守护程序，巨大的构建环境将占用很大的开销，影响构建速度。

可以在构建上下文中添加dockerignore文件，来排除不需要的文件和目录.

### 语法格式

````dockerfile
# Comment
INSTRUCTION arguments
````

**Docker按顺序在Dockerfile中运行指令. Dockerfile 必须以`FROM`指令开头 . 
这可能在解析器指令 ， 注释和全局范围的ARG之后**

### 解析器指令

解析器指令以特殊形式的注释形式# directive=value编写. 单个指令只能使用一次.
所有解析器指令必须位于Dockerfile的最顶部.

### 变量

使用ENV指令定义变量，变量的引用类似shell

### 构建指令



## docker 底层技术

### namespaces

### cgroups

### union filesystem