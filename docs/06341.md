# 什么是声明式编程？

> 原文：<https://dev.to/mortoray/what-is-declarative-programming>

声明式编程是一种[范式](https://mortoray.com/topics/paradigms/)，它表达了想要的结果，而不是如何实现它。它使用规则和约束来描述用户界面、网络布局、文档结构或语言解析器。声明性语言是高度特定于领域的，为这些应用程序提供了简洁的语法。

## 为什么

声明式编程消除了大量冗余编程和手工工作。这是计算机世界中自动化的主要形式。它有一些最容易阅读的源代码，并且是所有范例中抽象程度最低的。一般来说，程序员和用户可以很快学会使用声明性语言并提高工作效率。

鉴于它的结构化和规则性，声明性源代码可以很好地与工具配合。从可视化编辑器到语法检查器，再到翻译器，以及最终的执行引擎，工具是这种范式中的王者。

> 本文主要关注声明式编程的好处和核心品质。和所有范例一样，都有局限性。我将在以后的文章中讨论这些内容。

## 用户界面

文档和用户界面可能是典型的声明式程序。我们可以创建类似这样的表单:

```
<Header>User Information</Header>
<P>Fill out this form, pretty please? We'll give you a cookie.</P>
<Grid ColumnCount="2">
    <Text Value="Name"/>
    <TextInput Name="name"/>

    <Text Value="Age"/>
    <Counter Name="age"/>

    <Text Value="Country"/>
    <DropDown Options="countries" Name="country"/>

    <WhileValid>
        <Button Label="Save" Clicked="onSave"/>
    </WhileValid>
</Grid> 
```

Enter fullscreen mode Exit fullscreen mode

这个片段描述了表单的结构和布局。我们的 UI 引擎将使用它为用户生成一个实际的图形界面。在声明性范例中，我们对具体是如何发生的并不感兴趣，只关心这个表单会呈现给用户。

标签表明声明性语言不一定是静态的。如果输入有效，该界面将仅显示“保存”按钮。这种动态特性通常被认为是将用户界面与文档分开的一个方面。

> 一个`Clicked`处理程序显示了[事件范例](https://mortoray.com/2017/06/26/what-is-event-programming/)可以用来将这个接口连接到其他代码。由于声明性语言通常非常严格和有限，它们通常提供了挂钩其他范例的方法。

## 部署配置

手动安装和配置软件可能很麻烦并且容易出错。相反，声明式控制系统提供了一种方式来描述应该安装什么，并让部署引擎处理其余的事情。

```
host:
    name: company_web
    instances: 2
    daemons:
        - { service: web_serve_pro, version: 3.5 }
        - { service: sshd }
    ports:
        - { port: 443, access: public }
        - { port: 8080, access: vpn }
    files:
        - { directory: /web/, source: file_serv:/source/company_web/build } 
```

Enter fullscreen mode Exit fullscreen mode

像 UI 一样，我们声明我们想要的结果。我们不关心配置引擎如何让我们到达那个点。一些系统甚至支持对结构的实时更改:对这里的`port`的更改将更新网络中所有机器上的值。

也许比 UI 示例更明显，这里我们看到声明性语言公开了一系列规则和约束。声明式引擎的工作是生成一个兼容的系统。

## 解析器

声明性语言可以用来解释文档，而不是创建文档。正则表达式是一个简单的声明性程序:它指定一个字符串看起来像什么，并且正则表达式引擎可以解构这些部分。对于完整的语言，我们经常看到使用解析器生成器:

```
blog_markdown = { elements } ;

elements = title | paragraph | empty ;

title = '#' clickbait | sensible ;

clickbait = power_word ' ' noun ' ' emotional_verb ' ' uncommon_word ;

sensible = error("is this code path ever used?") ;

empty = r?^$? ; 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们可以看到一系列的约束规则。我们正在编写一种语言来描述我们希望解析的文档。同样，我们不关心引擎如何获取解析后的文档，只关心它是否符合这些规则。

## 语法和引擎

这些例子都使用了不同的可视化语法，但是在抽象层次上并没有什么不同。这些都是规则、约束和条件的树。它们中的任何一个都可以被转换成看起来更像另一个的语法。选择对目标领域来说最具表现力和最少冗余的语法。不匹配会导致语言过于庞大，或者不便于使用。

所有声明性语言的一个共同点是引擎。声明性程序很少被直接编译成可执行的形式。有的根本没有编制；这些声明由引擎直接读取并执行。与通用的[抽象机器](https://mortoray.com/2012/06/18/abstract-machines-interpreters-and-compilers/)不同，这些引擎通常是单一用途的；它们只支持一种特定领域的语言。

## 这不是编程

声明性语言通常不被认为是编程语言。特定于领域的语言通常缺少许多与传统编程相关的结构，比如流控制或内存管理。精通这些语言的人通常不被称为程序员。这种观点也许应该被看作是一种语言可以简化其领域的证明。

不管我们的定义是什么，声明性范式在编程中扮演着重要的角色。我们不能忽视的是，它以各种各样的形式在任何地方被使用。即使一个人不认为它是真正的编程，如果他们不使用它，他们将是一个糟糕的程序员。领域特定语言是在各种各样的项目中降低复杂性和提高生产率的强大工具。