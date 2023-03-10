# 什么是 Null？(宝贝不要伤害我)

> 原文：<https://dev.to/decoeur_/what-is-null-baby-dont-hurt-me-a8j>

有两种程序员。那些在程序中有空相关错误的人和那些刚刚开始职业生涯的人。可怕的 null、nil、undefined…大多数编程语言都有一个“空”的概念。但什么是虚无？你有没有想过 null，它到底是什么？什么不是呢？让我们看看 null 在 Java 中是什么意思。

首先，Java 中的 null 不是任何类型的实例。Java 的类型比较运算符 instanceof，[定义如下](https://docs.oracle.com/javase/specs/jls/se7/html/jls-15.html#jls-15.20.2):

> 在运行时，如果 RelationalExpression **的**值**不为 null** ，则 instanceof 运算符的结果为 true，并且引用可以转换为 ReferenceType，而不会引发 ClassCastException。否则结果为假。

所以根据定义，null 不是任何事物的实例。

然而，Java 语言规范或 JLS 定义了一种特殊的空类型。空类型没有名字，因此不能声明空类型的变量。您也不能将任何内容强制转换为 null 类型。唯一可能存在的价值(或者不是？)是空引用本身。对于每一个其他的[引用类型](https://docs.oracle.com/javase/specs/jls/se7/html/jls-4.html#jls-4.3)，您总是可以将该值赋为 null。正如 JLS 所说:

> 程序员可以忽略 null 类型，假装 null 只是一个特殊的文字，可以是任何引用类型。

在 Java 中，null 等于 null。意思是“null == null”。此外，根据约定，java.lang.Object 的 equals 方法声明，对于任何类型 type 的非空引用“ref ”, ref . equals(null)应返回 false。因此，虽然 null 等于 null，但它不等于任何其他值。

那你怎么用 null 呢？可能最常见的用例是表示所请求的引用不存在或不可用。例如，如果没有控制台可用，Java 的 System.console()方法将返回 null。

Null 也是所有引用变量的默认值。这意味着对于每个引用变量，如果你不赋值，它将为空。这一事实可以用来实现字段的惰性初始化，只有在第一次需要时才填充可能很昂贵的字段。在这种情况下，null 用于表示未初始化的状态。

除了引用不存在的对象或未初始化的状态，null 的一般用例还包括表示终止条件(参见 buffered reader . readline()；)并且有一个未知的值(比如将 null 映射到映射中的某个键)。

东尼·霍尔爵士有一句名言:发明 null 是他犯下的一个十亿美元的错误。有很多方法可以解决可空性的概念，例如使用[空对象模式](https://en.wikipedia.org/wiki/Null_object_pattern)。无论您决定做什么，当引用可能为空时，都要注意您的代码。否则你的应用程序可能会

```
Exception in thread "main" java.lang.NullPointerException
    at to.dev.WhatIsNull.main(WhatIsNull.java:42) 
```