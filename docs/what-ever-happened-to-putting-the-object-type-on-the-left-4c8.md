# 把对象类型放在左边会怎么样？

> 原文：<https://dev.to/nektro/what-ever-happened-to-putting-the-object-type-on-the-left-4c8>

我提前为这看起来像咆哮而道歉，但是有一个语法我最近看了很多次，我似乎不知道为什么。

这是类型定义的`:`和`->`语法。考虑一下 TypeScript 和 Java 中的两个片段。

```
function add(a: Number, b: Number): Number { ... } 
```

Enter fullscreen mode Exit fullscreen mode

```
public static int add(int a, int b) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

我注意到上面的两种行为在新的和“旧的”语言之间非常不同。

Java、C、C++、Objective-C 和 C#用 Java 的方式定义函数和变量。

Kotlin、Swift、Rust、TypeScript 和 Go 都将类型放在右边。

* * *

对这种转变的原因有什么想法吗？你喜欢哪种方式？