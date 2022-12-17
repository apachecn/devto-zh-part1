# 去看朱莉娅的五个(更多)理由

> 原文：<https://dev.to/jballanc/five-more-reasons-to-check-out-julia-26k6>

###### 这篇文章最初发表在我的[个人博客](https://www.manhattanmetric.com/blog)上，作为[五个(更多)理由去关注茱莉亚](https://www.manhattanmetric.com/blog/2018/01/five-more-reasons-to-check-out-julia.html)。

随着 2018 年的到来，如果你听说过 [Julia](https://julialang.org/) ，你可能会知道它是一种用于科学、金融和人工智能研究的编程语言。*诚然，这些领域中的每一个都采用了 Julia 并从其特性中受益匪浅，但我觉得 Julia 代表了一种有趣的、新的编程方法，可以改进我们所有人编写代码的方式。关于 Julia 对类型、函数的可选类型化以及基于类型的多重分派的组织，已经有很多文章(尤其是在[官方文档](https://docs.julialang.org/en/stable/))发表了。在这篇文章中，我想看看五个鲜为人知的特性，我相信这些特性让 Julia 工作起来很愉快，即使作为一个多面手开发者也是如此。*

## #1: do...结束

Ruby 是启发 Julia 的语言之一，所以 Julia 采用了 Ruby 的一个更具标志性的特性:`do...end`块也就不足为奇了。正如 Julia 采用的许多其他特性一样，它的`do...end`实现比 Ruby 的更简单、更灵活。

在 Ruby 中，使用`do...end`创建一个 Block 对象，该对象可以通过调用`yield`关键字来调用，或者如果 terminal 方法参数用`&`修饰，则该对象可以作为方法的一个参数。相反，Julia 从`do...end`块中定义了一个匿名函数，并在所有情况下将其作为第一个参数传递给前面的函数调用。因为 Julia 不像 Ruby(但像 Python)可以直接操作函数，所以它的工作非常简单。

Ruby 中块的另一个细微区别是去掉了管道字符来分隔块的参数。相反，从`do`到行尾的所有内容都被解析为块的参数列表。这确实意味着单行`do...end`块是不可能的(尽管在 Ruby 中它们总是不舒服，充其量也不过如此)。Julia 通过采用与 Java 8 几乎相同的匿名函数字面形式弥补了这一点。

```
julia> function mymap(f, coll)
         println(typeof(f))
         map(f, coll)
       end
mymap (generic function with 1 method)

# With a `do...end` block
julia> mymap(range(1, 4)) do x
         x * 2
       end
##1#2
4-element Array{Int64,1}:
 2
 4
 6
 8

# Using an anonymous function
julia> mymap(x -> x * 2, range(1, 4))
##3#4
4-element Array{Int64,1}:
 2
 4
 6
 8

# Passing a function directly
julia> double(x) = x * 2
double (generic function with 1 method)

julia> mymap(double, range(1, 4))
#double
4-element Array{Int64,1}:
 2
 4
 6
 8 
```

Enter fullscreen mode Exit fullscreen mode

(注意`##1#2`只是 Julia 表示匿名函数的方式。)

所有这些形式都是等效的，并且自 Julia v0.6 版发布以来，表现完全相同。

## #2: Unicode 运算符

对于许多编程语言来说，在标识符(变量和函数名)中支持完整的 Unicode 字符集，除了偶尔的玩笑或混乱的代码竞争之外，没什么用处。另一方面，Julia 在支持 Unicode 方面做得非常好，不仅在标识符方面，而且对于某些字符，还作为运算符。

为了理解为什么这是一个优势，考虑除法的例子。在 Ruby，Python，甚至 C 中，`4 / 3`的值是`1`！如果您想要更接近于 4/3 的*实际值*，一个或两个值必须是浮点数开始:`4 / 3.0`导致`1.3333...`。有人可能会说这是编程语言中的一个长期传统，所以“没什么大不了的”，但这是错误的、复杂的，会导致错误。如果你在计算`a / b`而不是数值，如果不知道每个参数的显式类型，你就无法知道结果的类型。

Julia 通过两个除法运算符避免了这一困境:`/`和`÷`。使用`/`将总是给出除法最接近的浮点近似值，即使两个操作数都是`Integer`类型。人们认为这通常是程序员对`a / b`的期望。使用`÷`执行截断的`Integer`除法。(值得注意的是，还有两个除法运算符:`//`执行`Rational`除法，而`\`执行左除法。)

当然，如果你每次想插入表情符号时都必须在表情符号托盘中搜寻，那么 Unicode 运算符就没什么用了。在这里，朱莉娅也非常乐于助人。在 REPL 上，插入一个 Unicode 字符就像输入一个`\`后跟符号的缩写，然后按下`<TAB>`一样简单。因此，对于上面的除法示例，您可以从`4 \div`开始，按`<TAB>`，然后输入`3`表示:`4 ÷ 3`。Vim、Emacs、Atom 等的 Julia 包(都可以从优秀的[Julie adorsupport](https://github.com/JuliaEditorSupport)GitHub 组织获得)支持相同的功能，因此在编辑器中输入 Unicode 字符应该和 REPL 一样工作。

## #3:点播

尽管 Julia 在非数字编程任务中比 R 和 Matlab 等语言更有用，但它仍然包含了许多方便处理矩阵的特性(比如能够用`'`运算符简单地转置一个矩阵)。然而，为了与 Julia 的设计哲学保持一致，即使这些便利也是以最灵活的方式简单实现的(为了看看*如何*灵活，考虑一下最近的[关于将转置操作符重新用于方法匹配](https://github.com/JuliaLang/julia/issues/5571#issuecomment-352247104)的提议)。

最近的一个例子是“点”广播，它原本是为数字节目设计的，后来证明更为有用。如果你熟悉`map`的函数式编程概念，那么`broadcast`看起来会很熟悉...但是不一样。`map`和`broadcast`的本质区别在于，当`broadcast`应用于可迭代值和标量值的组合时，它有效地重复标量以匹配可迭代值的维度。(我希望，通过下面的一些例子，这一点会变得更清楚。)

朱莉娅有一种方法已经很久了。最近增加的是通过添加一个不起眼的`.`将任何运营商或任何功能变成广播功能的能力。对于函数，`.`位于函数名和左括号之间。对于操作员，`.`在操作员之前。为了理解为什么广播很重要，考虑一个简单的平方值的函数:

```
julia> square(x) = x * x
square (generic function with 1 method)

julia> square(2)
4

julia> a = reshape(range(1, 9), 3, 3)
3×3 Base.ReshapedArray{Int64,2,UnitRange{Int64},Tuple{}}:
 1  4  7
 2  5  8
 3  6  9

julia> square(a)
3×3 Array{Int64,2}:
 30  66  102
 36  81  126
 42  96  150

julia> square.(a)
3×3 Array{Int64,2}:
 1  16  49
 4  25  64
 9  36  81 
```

Enter fullscreen mode Exit fullscreen mode

对矩阵求平方与对矩阵中的每个值*求平方是非常不同的操作。广播`square`功能可以让我们轻松地做后面的事情。将标量混合在一起，我们可以看到广播和`map` ing:
之间的明显区别*

```
julia> dotjoin(a, b) = "$a.$b"
dotjoin (generic function with 1 method)

julia> searchengines = ["google", "bing", "duckduckgo"]
3-element Array{String,1}:
 "google"
 "bing"
 "duckduckgo"

julia> dotjoin.(searchengines, "com")
3-element Array{String,1}:
 "google.com"
 "bing.com"
 "duckduckgo.com"

julia> map(dotjoin, searchengines, "com")
3-element Array{String,1}:
 "google.c"
 "bing.o"
 "duckduckgo.m" 
```

Enter fullscreen mode Exit fullscreen mode

然而，当你开始形成链时，点传播的真正力量就来了。这需要一点时间来适应，但它有可能极大地简化您编写的任何操作集合的代码。

```
julia> httpify(a) = "http://$a"
httpify (generic function with 1 method)

julia> wwwify(a) = "www.$a"
wwwify (generic function with 1 method)

julia> comify(a) = "$a.com"
comify (generic function with 1 method)

julia> httpify.(wwwify.(comify.(searchengines)))
3-element Array{String,1}:
 "http://www.google.com"
 "http://www.bing.com"
 "http://www.duckduckgo.com" 
```

Enter fullscreen mode Exit fullscreen mode

更进一步说，Julia 中的`|>`操作符(目前)做函数链接。将这一点与点广播结合起来，揭示了朱莉娅真正的力量和美丽。

```
julia> searchengines .|> comify .|> wwwify .|> httpify
3-element Array{String,1}:
 "http://www.google.com"
 "http://www.bing.com"
 "http://www.duckduckgo.com" 
```

Enter fullscreen mode Exit fullscreen mode

## #4: Pkg.generate()

有一个很好的理由，尽管它相对年轻，社区规模适中，朱莉娅已经拥有一个健康的包装生态系统。这是因为 Julia 的核心是软件包的开发、发行和支持。开箱即用，朱莉娅包括下载和安装第三方软件包的概念。它甚至会在你第一次尝试`using Foo`时告诉你，你可能首先需要运行`Pkg.add("Foo")`。

真正让 Julia 与众不同的是默认的`Pkg`模块被`PkgDev`包扩展，以包含开发新包的工具。

```
julia> Pkg.add("PkgDev")

julia> using PkgDev

julia> PkgDev.generate("MyAwesomeLib", "MIT")
INFO: Initializing MyAwesomeLib repo: /Users/jballanc/.julia/v0.6/MyAwesomeLib
INFO: Origin: https://github.com/jballanc/MyAwesomeLib.jl.git
INFO: Generating LICENSE.md
INFO: Generating README.md
INFO: Generating src/MyAwesomeLib.jl
INFO: Generating test/runtests.jl
INFO: Generating REQUIRE
INFO: Generating .gitignore
INFO: Generating .travis.yml
INFO: Generating appveyor.yml
INFO: Generating .codecov.yml
INFO: Committing MyAwesomeLib generated files

julia> using MyAwesomeLib 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这不仅仅是生成一个项目框架，而是用管理 Git 忽略的文件、macOS、Linux、*和* Windows 的 CI 以及代码覆盖度量标准来预填充这个框架。由于这个原因，Julia 包倾向于被广泛测试，并且覆盖面很大，这也就不足为奇了。新生成的包也会自动放置在正确的位置，以便立即使用。

如果你碰巧发现了其他人的软件包中的一个 bug，Julia 也会让你很容易地做出反馈。

```
julia> Pkg.checkout("Requests")
INFO: Checking out Requests master...
INFO: Pulling Requests latest master... 
```

Enter fullscreen mode Exit fullscreen mode

调用`checkout`会暂时将有问题的包从正常的依赖关系和版本解析过程中分离出来，这样您就可以进行任何必要的更改，并提交一个包含您的修复的 pull-request。一旦完成，调用`Pkg.free("Requests")`会将包返回到正常的版本控制。

## #5:字符串宏

其他 LISPs 的一个更强大但不被重视的特性是 reader 宏的概念。这使得开发人员可以在基础层面上操纵语言的解析方式，从而创建定制的文字形式。虽然 Julia 没有合适的阅读器宏，但它有下一个最好的东西:字符串宏。

基本上这些是如何工作的，如果你定义一个名字以`_str`结尾的宏，那么它可以用来修饰一个字符串。不是直接将该文字解析为字符串，而是首先将该字符串传递给相应的宏，并替换它返回的任何类型。

```
julia> struct Person
         firstname::String
         lastname::String
       end

julia> macro person_str(fullname)
         nameparts = split(fullname, " ")
         Person(nameparts[1], nameparts[end])
       end
@person_str (macro with 1 method)

julia> person"Joshua Ballanco"
Person("Joshua", "Ballanco") 
```

Enter fullscreen mode Exit fullscreen mode

字符串宏也可以在调用它们的字符串后面接受额外的参数(为这个人为的例子道歉):

```
julia> macro person_str(fullname, suffix)
         strippedname = replace(fullname, Regex(" $suffix\.?\$", "i"), "")
         nameparts = split(strippedname, " ")
         Person(nameparts[1], nameparts[end])
       end
@person_str (macro with 1 method)

julia> person"Sammy Davis Jr."jr
Person("Sammy", "Davis") 
```

Enter fullscreen mode Exit fullscreen mode

事实上，虽然 Julia 使用`r"foo.*bar"i`来构造正则表达式文字很像 Python，但这是作为一个简单的字符串宏实现的，而不是解析器黑客。许多 Julia 包已经充分利用了这个特性。例如，Cxx.jl 包中有一个字符串宏，用于在 Julia 函数中编译 C++代码。

## 奖金原因

希望现在我已经说服你在 2018 年开始时给朱莉娅另一个造型，但如果你需要一些额外的动力，这里还有一个原因:朱莉娅很有可能在 2018 年达到 1.0 版本。

众所周知，预测软件里程碑是令人担忧的，尤其是开源软件。也就是说，朱莉娅已经进入了最后冲刺阶段。还将发布一个 1.0 之前的版本(v0.7)，具有与 v1.0 相同的所有功能，但保留了对自 v0.6 以来所有更改的不赞成警告。然后，一旦每个人都有机会验证他们的包并消除不赞成的代码，警告将被删除，v1.0 将命名为...或者，至少计划是这样的。

核心团队已经宣布他们打算严格遵守 Julia 的语义版本，所以任何针对 v1.0 开发的库将在 Julia v1 期间继续工作。x 的一生。这并不意味着 1.0 版会很完美，但会很稳定。事实上，在很多情况下，核心团队选择了*而不是*在 1.0 版中加入特性，理由是在 1.1 版中加入特性要比等到 2.0 版才移除它们更容易。

朱莉娅社区仍然很小，但它正在增长。已经有了一个健康的库集合，以及合并 C、Python 和 Java 库的相对简单的方法(以及调用 C++的稍微不太简单的方法)。如果你在 2018 年寻找一门新的语言来学习，既能帮助你成长为一名程序员，又能为你提供一个对这门语言的未来产生重要影响的机会，Julia 绝对值得一看！