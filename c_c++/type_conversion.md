# 类型转化

1.  static_cast
    
    隐式转化，相当于c中的强制类型转化，没有运行时类型信息，因此只能保证向上类型
    转换安全，不能保证向下类型转换安全，且不能转化表达式的const, volatile, 
    __unaligned(修饰指针，指示地址非对齐，一般用于嵌入式开发)属性。


1.  dynamic_cast

    动态类型转换，用于类之间的转换，包含运行时信息


1.  const_cast

    用于修改类型的const或volatile属性

1.  reinterpret_cast

    指针和整数之间相互转化，只有把转换后的类型，再转化为原来的类型，才是安全的







