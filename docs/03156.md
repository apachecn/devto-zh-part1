# 我的快速旅程-基础

> 原文：<https://dev.to/monicag/my-swift-journey---the-basics-bn3>

我目前正在学习 Swift 以扩展我的编程知识。几年前我关注过 Swift，但现在我准备深入研究它。

本人用 Java 编程多年，对 Python 有一定的熟悉。所以，我学习一门新语言的方法受到了先前知识的影响。比如我已经知道什么是循环了。对于 Swift，我想知道可用的控制流语句及其语法。这不同于我第一次学习编程时，除了语法之外，我还必须学习循环的概念。

我希望学习 Java 中没有的概念。我还想看看另一种语言如何接近典型的编程概念。

此帖子包含我对 Swift 的基本语法、范围和元组的注释。这些笔记并不是详尽的资源。随着我继续学习，我计划创建关于其他概念的帖子。

## 语法基础知识

| 语法/概念 | 迅速发生的 |
| --- | --- |
| 分号 | 分号可以用来分隔行，但建议不要这样做。如果一行中有多个语句，则需要分号。 |
| 评论 | 和 Java 一样。 |
| 圆括号 | 在条件语句中不需要。这与需要它们的 Java 不同。 |
| 花括号 | 在条件语句或循环语句后是必需的。在 Java 中，它们不是必需的。这可能会导致错误，因为被认为是在条件/循环块中的代码并不是。Swift 通过要求花括号来避免这种潜在的错误。 |
| 类型推理 | 斯威夫特可以推断类型。整数和双精度是默认类型，而非无符号整数或浮点数。如果需要 Float 或 Unsigned Integer 类型，必须显式声明这些类型。 |
| 常数声明 | `let`关键词。Swift 更喜欢使用常量而不是变量。 |
| 变量声明 | `var`关键词。 |
| 逻辑运算符(非、与、或) | 与 Java 相同 |
| 字符串插值 | `\(variable)`成一串 |

例子:

```
//constant declaration, with type inference
let x = 3
//constant declaration, with type annotation
let tax : Float = 0.7

//variable declaration, with type inference
var y = "Apple"

//variable declaration, with type annotation
var title : String = "Harry Potter And The Half Blood Prince"

//conditionals do not need parentheses, but do need curly braces
let a = true
let b = false
let c = false
if a == b {
    print("They are the same!")
}else {
    print("They are different")
}

//logical operators
if a && b || !c {
    print("in code block")
}

//string interpolation
let name = "Snoopy"
print("Hello \(name)") 
```

Enter fullscreen mode Exit fullscreen mode

## 范围

我喜欢 Swift 中的范围运算符。它们使用起来直观而简洁。

| 迅速发生的 | 笔记 |
| --- | --- |
| a...b | a 和 b 是包含的。被称为“封闭范围操作者” |
| a..< b | 包括 a，但不包括 b。称为“半开值域算子” |
| [2...] | 单边范围。在本例中，获取从索引 2 开始的所有数组项。 |
| [..<2] | 单边范围。在此示例中，获取所有数组项，但不包括索引 2。 |

例子:

```
//Closed Range Operator. 
for x in 1...3 {
    print(x)
}

//Half Open Range Operator
for x in 1..<3 {
    print(x)
}

//One sided ranges
let fruits = ["apple", "banana", "peach", "grape"]

for fruit in fruits[1...] {
    print(fruit)
}

for fruit in fruits[..<2] {
    print(fruit)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 元组

*   万岁！雨燕有元组！我喜欢在 Python 中使用它们，也很高兴 Swift 包含了它们。:)
*   Swift 还支持命名元组。元组值可以通过名称而不是索引值来访问。

示例:

```
//Named tuple
let t = (currencyName: "CAD", exchangeRate: 1.2, id: 4)

print(t.currencyName) 
```

Enter fullscreen mode Exit fullscreen mode

## 第一印象

到目前为止，我喜欢斯威夫特！语法易于阅读，书写简洁。我也喜欢它是一种类型安全的语言。我期待着学习更多！

感谢您的阅读！如果你注意到我错过或做错了什么，请告诉我！:)

## 参考文献

[掌握 Swift 4](https://www.packtpub.com/application-development/mastering-swift-4-fourth-edition)

[Swift 编程语言指南和样本代码](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/index.html)

[Swift 维基百科条目](https://en.wikipedia.org/wiki/Swift_(programming_language))

*这篇文章最初[发表在我的博客](https://monicagranbois.com/blog/swift/my-swift-journey-the-basics/)上。*