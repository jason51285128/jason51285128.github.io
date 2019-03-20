# 右值引用和std::move
nested dependent type name

引用一个在模板中定义的类型，显示的告诉编译器，引用的时类型而不是静态变量


右值引用，移动

左值

指向一块被定义的内存区域


右值

一个表达式的值


左值引用

必须绑定到一个左值，除非用const修辞，才能绑定到一个右值


右值引用

必须绑定到一个右值，对应一个临时变量，具有move语义
通过隐式转换，可以绑定到一个左值，

右值引用变量是左值



引用叠加规则


模板参数类型推导规则


std::remove_reference为C++0x标准库中的元函数，其功能为去除类型中的引用。
std::remove_reference<U&>::type <=> U
std::remove_reference<U&&>::type <=> U
std::remove_reference<U>::type <=> U
