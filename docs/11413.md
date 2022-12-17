# PHP 三元运算符

> 原文：<https://dev.to/emmanuelgautier/php-ternary-operator-49n3>

三元运算符的使用并不是最简单、可读性最强的开发方式，但对于简单的情况可能很有用。三元运算符的实现依赖于语言，我们来看看在 PHP 中如何实现。

## 什么是三元运算符？

这是一个单行的方式来编写一些条件，如下面的例子:

```
if($boolean) {
  echo "foo";
} else {
  echo "bar";
} 
```

Enter fullscreen mode Exit fullscreen mode

这个条件很简单，但是需要 5 行代码。三进制操作符只允许在一行中包含这个条件，而不会影响太多的可读性

```
echo ($boolean ? "foo" : "bar"); 
```

Enter fullscreen mode Exit fullscreen mode

你为每个用例编写这样的代码，并把一个三元运算符转换成一个三元运算符。但是这种方式会降低代码的可读性，从而使应用程序更难理解和维护:

```
echo ($boolean ? ($boolean2 ? "true true" : "true false") : "false"); 
```

Enter fullscreen mode Exit fullscreen mode

三元运算符也可以写成如下形式:

```
$value = $value ?: $othervalue; 
```

Enter fullscreen mode Exit fullscreen mode

这种方式对于指定默认值很有用。让我们以一个用例为例，我们希望显示姓名，如果存在的话，显示姓氏，否则显示用户名:

```
$display_name = $lastname ?: $username; 
```

Enter fullscreen mode Exit fullscreen mode

[文档](http://php.net/manual/fr/language.operators.comparison.php#language.operators.comparison.ternary)