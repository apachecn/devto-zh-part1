# 将 Rust 翻译成其他语言(第 1 部分-上下文无关语法)

> 原文：<https://dev.to/living_syn/translating-rust-to-other-languages-pt-1--context-free-grammar-4kec>

# 概述

在我目前工作的地方，我用 Rust 编写库，这些库被其他语言调用，在多个操作系统上，在多个平台上。此外，它还包括从不同的通信总线接收数据流，并由其他平台上的其他开发人员用其他语言将其解码成可用的形式！结果，我得到了 rust 项目，它有数百个通过 Rust FFI 公开的结构。

我不想手工维护那些翻译，所以我写了一个工具来帮我做这件事。有多种方法可以做到这一点，我可以写一个简单的翻译器，一行一行地翻译，并期望 rust 结构看起来非常特别。然而，这是不可维护的，也不能共享的，所以我使用了一个名为 [Lark](https://github.com/erezsh/lark) 的解析库。Lark 允许我编写一个[上下文无关语法](https://en.wikipedia.org/wiki/Context-free_grammar)来指定我期望的语言，并为我提供一个很好的接口来将定义的符号转换成易于使用的 python 对象。一旦我有了这些对象，用其他语言构建源代码就相当简单了。

你可以在这里看到整个项目:[https://github.com/LivingInSyn/Rust_Api_Generator](https://github.com/LivingInSyn/Rust_Api_Generator)

# 上下文无关语法

我不会对写语法的所有细节进行过多的描述。我将主要关注我写的语法。Lark 有一个很好的教程[在这里](https://github.com/erezsh/lark/blob/master/docs/json_tutorial.md)，它要深入得多

当从头开始写一个语法时，我发现最好是从内向外写。也就是说，从最基本的元素开始，反向工作。事物名称(变量、结构等)是一个很好的起点，这就是我们今天要开始的地方。我们将我们的第一个符号称为“名称”，它看起来像这样:

```
?name: /[a-z_A-Z][a-z_A-Z0-9]*/ 
```

Enter fullscreen mode Exit fullscreen mode

前面的问号运算符说，如果这个符号只有一个子，不要用另一个对象包装(解析的时候)，把子给我就行了。那个？后面是我们符号的名称，在这种情况下就是“名称”。接下来是一个正则表达式，它定义了一个名字可能是什么样子。在这种情况下，它是一个大写或小写的 A-Z，或者一个下划线，后面跟着无限数量的字母数字字符和下划线。

接下来我们要定义一个我们称之为 rtype 的东西。除非我们在语法中做进一步的解释，否则这没有多大意义，但是可以说，rtype 被定义为一个名字或者一个数组声明。看起来是这样的

```
?rtype: name
    | array 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了 rtype 的定义，我们就可以定义一个指针修饰符，我们将在其他声明中使用它。

```
pointer: "*" mutable
    | "*" const

mutable: "mut"
const: "const" 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了 3 个符号，指针，变量和常量。我们让可变和常量拥有自己的符号，因为我们希望能够根据它们的属性执行不同的操作。指针由字符串“*”定义，后跟这两个符号中的一个，而这两个符号又由字符串定义

一旦我们有了一个 rtype 和一个指针的定义，我们就可以定义一个叫做“修改的类型”的东西，它告诉我们，如果我们在看另一个声明中的名字，它是不是一个指针。指针符号周围的方括号([])表示它是可选的。这样

`*mut i16`

和

`i16`

就会两者都被匹配了？修改类型符号。

```
?modifiedtype: [pointer] rtype 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们回顾一下，定义“数组”,因为我们已经在 rtype 定义中使用了它。数组由左括号、修饰类型、分号、一个或多个以 1-9 开头的数字(不能是 0)定义。最后，它必须以右括号结束。

```
array: "[" modifiedtype ";" /[1-9]{1}[0-9]*/ "]" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经很好地掌握了一些上下文无关的语法规则，让我们向前跳一点，看看我们需要定义一个结构的其余部分

```
reprc: "#[repr(C)]"

comment: /\/\/[^\n]*/

decl: [ispub] name ":" modifiedtype [","]
    | comment

?ispub: "pub"
struct:  [reprc] [ispub] "struct" name "{" decl+ "}" 
```

Enter fullscreen mode Exit fullscreen mode

这里我们将 repr(C)指令定义为 rust，它告诉 rust 让这个结构使用符合 C 的内存布局。我们将使用这个(和 pub)来决定翻译哪些结构。如果它不是 repr(C)和 pub，那么翻译它就没有意义，因为它不能在 FFI 接口上工作。我们声明 decl 是一个组合，它是否是公共的，一个名字，一个冒号，一个修改的类型和一个可选的逗号。它也可以是注释，因为我们允许在结构定义中使用注释。这里值得注意的是，我们有意设计了不处理多行注释的语法。预处理器稍后会删除它们。我们希望单行注释被移走，而不是多行注释。

我们将结构定义为可能是 reprc，可能是 public，单词 struct，一个名称，一个左括号，一个或多个 decl 和一个右括号。

接下来，我们将定义一些我们不打算在语言之间翻译的符号。我们必须在我们的语法中定义它们，这样当我们解析文件时就不会出错。它们是“using”语句、“extern crate”语句和“impl”语句。定义在这里:

```
externcrate: "extern" "crate" name [optionalas] ";"
?optionalas: "as" name

usedecl: "use" namespace+ ";"
?namespace: ["::"] name

impl: ["unsafe"] "impl" name "for" name "{}" 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是定义文件的起点和顶层定义。还有一些关于 Lark 的说明，基本上忽略空白，尽可能将事情作为字符串处理。

```
?statement: struct
    | usedecl
    | comment
    | impl
    | externcrate

?start: statement+

//for use by lark
%import common.ESCAPED_STRING
%import common.WS
%ignore WS 
```

Enter fullscreen mode Exit fullscreen mode

完整的上下文无关语法可以在这里找到

# 使用 CFG

现在我们已经得到了一个 CFG，让我们把它解析成一个抽象的语法树。我们将以下面的 rust 结构作为起点:

```
#[repr(C)]
pub struct bar {
    pub unsigned_32bit: u32,
    pub arraytest: [u8;10],
    pub pntr_array: [*mut c_char;4],
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将运行以下 python 代码。基本上，我们正在创建解析器，将它传递到我们在上一节中创建的语法文件中。然后我们要求它解析一些输入，并以一种漂亮的方式打印出结果语法树。

```
from lark import Lark

gf = open("rgrammar.g", 'r')
parser = Lark(gf.read(), parser="lalr")
tree = parser.parse('''#[repr(C)]
pub struct bar {
    pub unsigned_32bit: u32,
    pub arraytest: [u8;10],
    pub pntr_array: [*mut c_char;4],
}''')
print(tree.pretty()) 
```

Enter fullscreen mode Exit fullscreen mode

运行这段代码得到以下输出:

```
struct
  reprc
  ispub
  bar
  decl
    ispub
    unsigned_32bit
    u32
  decl
    ispub
    arraytest
    array
      u8
      10
  decl
    ispub
    pntr_array
    array
      modifiedtype
        pointer
          mutable
        c_char
      4 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的抽象语法树。我们可以看到该结构上有一个 reprc，它是公共的，名为 bar，并且它有 4 个 decl，以及它们所有的相关字段。

# 结论

在本文中，我们讨论了如何使用 Lark 创建 CFG 并使用它来创建一个基本的抽象语法树。在下一节中，我们将使用这些信息将 lark 创建的对象转换成更有用的形式，最后用其他语言输出代码。