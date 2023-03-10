# 编程语言中非常简单的绑定概念

> 原文：<https://dev.to/odigietony/the-very-simplified-concept-of-binding-in-programming-languages-op>

最基本的，绑定是将一个事物分配给另一个事物，通常是通过显式声明或隐式声明将值分配给变量。对于编程新手来说，显式和隐式声明这两个术语似乎令人望而生畏，其实不然。显式声明仅仅意味着定义变量名及其类型的语句，例如

```
public int i; 
```

Enter fullscreen mode Exit fullscreen mode

而在隐式中，实现默认约定，例如

```
int i; 
```

Enter fullscreen mode Exit fullscreen mode

它假设它是公共的。
绑定`int a, b = 0;`可以发生在运行时，也可以发生在编译时，当它发生在运行时，变量的当前状态被改变，因为它被赋予了一个值。例如

```
int a, b = 0, c;
    for(a = 0; a < 10; a++){
        b+=c;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在执行`for`循环时，随着数值的增加，`c`的状态也随之改变。
**绑定类型**
*动态绑定*:变量根据赋值被绑定到一个类型。
这发生在运行时。
*静态绑定*:这发生在编译时，编译器会计算出将什么变量分配给什么方法或函数。一个完美的例子是方法重载，其中两个方法具有相同的名称，但参数的数量或类型不同。如果在方法`A`(有两个参数)中传递了两个参数，编译器会计算出要返回的方法是`A`。希望这有所帮助。