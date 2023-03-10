# 如何处理涉及多个字段的验证？

> 原文：<https://dev.to/goshacmd/how-to-handle-validations-involving-several-fields-1ln3>

通常，单独验证每个字段就足够了:

*   电子邮件应该包含一个`@`；
*   密码应该超过四个字符。

但是偶尔，您需要一起验证几个字段:

*   密码确认应该与密码匹配；
*   电话号码的长度取决于单独输入的国家代码。

很难或者不可能在不使代码变得不必要复杂的情况下，用[字段级验证](https://dev.to/y-no-field-level-validations/)实现这一点。然而，使用来自[其他文章](https://dev.to/instant-form-fields-validation-react/)的一个简单的验证架构，这是相当琐碎的。

验证整个表单的函数很简单:

```
function validate(email, password) {
  // true means invalid, so our conditions got reversed
  return {
    email: email.length === 0,
    password: password.length === 0,
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我是说，还有比这更简单的吗？然而，它非常强大。一起验证几个字段很容易实现:

```
function validate(email, password, passwordConfirm) {
  // true means invalid, so our conditions got reversed
  return {
    email: email.length === 0,
    password: password.length === 0,
    passwordConfirm: password !== passwordConfirm,
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试输入不同的密码:

[jsbin.com 上的 JS Bin](https://jsbin.com/dunukov/2/embed?js,output)

受控输入再一次拯救了我们🐶

*本帖最初发表于[goshakk . name](https://goshakkk.name/validations-several-fields/)T3】*

* * *

我专门写了 React 中的表单和其他与 React 相关的东西。
如果你喜欢你在这里看到的，[订阅这里](http://goshakkk.name/subscribe/)以确保你不会错过我的下一篇文章。