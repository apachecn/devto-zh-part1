# 一个 Rubyist 看水晶(第一部分)

> 原文：<https://dev.to/citizen428/a-rubyist-looks-at-crystal-part-1>

目前不缺乏新的有趣的编程语言。几乎不可能花时间在黑客新闻或 Twitter 上，而不是每周都看到新语言的公告。作为一个编程语言的超级书呆子，我通常会阅读其中的大部分，但很少有人能让我保持长久的兴趣。然而，Crystal 是一种新的语言，他们在[boundary source 页面](https://salt.bountysource.com/teams/crystal-lang)上的标语对此做出了贡献:

> 像 C 一样快，像红宝石一样光滑

我从 2004 年开始就是一名 Rubyist 爱好者，喜欢这种语言和它的表现力。虽然它肯定不是一匹赛马，但一般来说，它的速度足以满足我的需求。尽管如此，拥有一种语法相似、性能卓越的语言的前景还是很诱人的，所以我决定花一些时间和 Crystal 在一起，并写下我的印象。

### 第一印象

该项目的网站陈述了 Crystal 的五个目标，其中第一个是“拥有与 Ruby 相似的语法(但与其兼容不是目标)”。

水晶看起来像红宝石。事实上，编写两个编译器都能接受的(琐碎的)程序是可能的。然而，如上所述，兼容性不是目标，这可能是更好的，因为尽管语法相似，语言的语义实际上是非常不同的。

下面的类定义给出了 Crystal 的第一印象:

```
class Person
  property age
  getter name

  def initialize(@name : String, @age : Int32)
  end
end

p = Person.new("Michael", 35)
p.name #=> "Michael" : String
p.age #=> 35 : Int32
p.age += 1 #=> 36 : Int32
p.name = "Other person" # undefined method 'name=' for Person 
```

Enter fullscreen mode Exit fullscreen mode

虽然这看起来有点像 Ruby，但还是有一些明显的区别。可读性更强的`property`和`getter`代替了`attr_accessor`和`attr_reader`。该方法使用一种快捷方式直接将其参数赋给实例变量，并且还使用了[类型限制](https://crystal-lang.org/docs/syntax_and_semantics/type_restrictions.html)，由于 Crystal 非常好的类型推断，这种限制通常是不必要的。

下面的例子展示了两种语言之间的另一个巨大差异，清晰性和基于类型的方法重载，这是我在 Ruby 中一直渴望的特性:

```
class Dog
  def greet
    "Woof! Woof!"
  end

  def greet(name : String)
    "Woof #{name}!"
  end

  def greet(times : Int32)
    greet * times
  end
end

d = Dog.new
d.greet #=> "Woof! Woof!" : String
d.greet("dear readers!") #=> "Woof dear readers!" : String
d.greet(3) #=> "Woof! Woof!Woof! Woof!Woof! Woof!" : String 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了`Dog#greet`的三种不同实现，编译器正确地分派到具有正确 arity/type 组合的版本。我个人认为这比条件检查可选参数的存在要好得多。

Crystal 的语法胜过 Ruby 的另一个地方是块的缩写形式(见 [Symbol#to_proc](http://ruby-doc.org/core-2.3.1/Symbol.html#method-i-to_proc) ):

```
%w(foo bar).map(&.upcase) #=> ["FOO", "BAR"] : Array(String)
(1..5).map(&.+(2)) #=> [3, 4, 5, 6, 7] : Array(Int32) 
```

Enter fullscreen mode Exit fullscreen mode

```
%w(foo bar).map(&.upcase) #=> ["FOO", "BAR"]
(1..5).map(&2.method(:+)) #=> [3, 4, 5, 6, 7] 
```

Enter fullscreen mode Exit fullscreen mode

而 Ruby 使用了`&:upcase` Crystal 使用了`&.upcase`，我发现这更能揭示意图。它还使得向被调用的方法传递参数成为可能，而这在 Ruby 中通常是不可能的(或者像上面的例子一样，只需要一些技巧)。

还有一些其他微小的语法差异，比如字符串总是用双引号括起来(单引号表示字符文字)或者访问修饰符是方法声明的一部分，但是它们都不会让 Ruby 开发人员感到过于困惑(尽管我有时发现很难克服肌肉记忆)。

### 类型

Crystal 是一种强类型语言。然而，这并不意味着你的代码需要类型注释，编译器通常在推断类型方面做得很好。但是，在某些情况下，该语言需要您的帮助，在这种情况下，它会为您提供一个有用的错误消息。

我们来看一个例子:

```
class Foo
  def initialize(a)
    @a = a
  end
end

Foo.new(1) 
```

Enter fullscreen mode Exit fullscreen mode

这个看似简单的示例不会编译，而是产生以下编译时错误:

> 无法推断实例变量的类型——@ a ' of Foo
> 一个实例变量的类型，如果没有用
> `@a : Type`显式声明，则是从整个程序中对它的赋值中推断出来的。作业必须是这样的。`@a = 1`(或其他文字)，推断为文字的类型
> 2。`@a = Type.new`，型号推断为
> 3 型。`@a = Type.method`，其中`method`有一个返回类型
> 的注释，类型是从它的
> 4 中推断出来的。`@a = arg`，arg 是带有
> 类型限制的方法参数，type 被推断为 Type
> 5。`@a = arg`，arg 是一个带有
> 默认值的方法参数，使用规则 1、2 和 3 从它
> 6 中推断出类型。`@a = uninitialized Type`，类型推断为类型
> 7。`@a = LibSome.func`，而`LibSome`是一个`lib`，类型
> 就是从那个好玩的推断出来的。8。`LibSome.func(out @a)`，而`LibSome`是一个`lib`，类型
> 是从那个有趣的参数中推断出来的。
> 其他赋值对其类型没有影响。
> 无法推断 Foo 的实例变量“@a”的类型

虽然这条消息确实很长，但是它对 Crystal 的类型推断是如何工作的给出了一个完整的解释。对上面显示的程序的正确修正是对方法参数的类型限制，如 4 中所指出的。

```
def initialize(a : Int32)
  @a = a
end

# or shorter
def initialize(@a: Int32)
end 
```

Enter fullscreen mode Exit fullscreen mode

我听到经验丰富的 ruby 爱好者在这一点上问，为什么要为静态类型费心，这个问题不是没有价值的。因此，让我们来看一个例子，Crystal 的类型推断和对联合类型的巧妙使用使我们避免了运行时错误。

```
found = %w(foo bar).find { "foo" }
typeof(found) #=> (String | Nil)
found.upcase # undefined method 'upcase' for Nil (compile-time type is (String | Nil))
found.upcase if found #=> "FOO" : String 
```

Enter fullscreen mode Exit fullscreen mode

这里[可枚举的#find](https://crystal-lang.org/api/0.19.4/Enumerable.html#find(if_none=nil,&block)-instance-method) 将返回一个字符串或`nil`，在 Ruby 中，当没有找到元素时，这将导致一个`RuntimeError`，我们尝试在`nil`上调用`upcase`方法。然而，Crystal 编译器在这里使用联合类型`(String | Nil)`来表示`found`，并且不会编译这段代码，因为不是联合中的所有类型都知道如何响应`upcase`消息。因此，要真正编译这个程序，我们需要明确防范`nil`的情况，如示例的最后一行所示。

这当然是一个人为的简短例子，但是它展示了 Crystal 是如何在程序执行过程中不需要我们做任何额外的工作就将我们从错误中拯救出来的。

### 抢包

为了结束对 Crystal 的第一次观察，让我们看看 Ruby 没有提供的一些更好的特性。

#### 结构

Crystal 提供了不止一种定义类的方法。除了使用`class`关键字，它们也可以用`struct` :
来定义

```
struct Point
  property x, y

  def initialize(@x : Int32, @y : Int32)
  end
end

p = Point.new(5, 3)
p.class #=> Point : Class 
```

Enter fullscreen mode Exit fullscreen mode

虽然上面也定义了一个类，`Struct` s 将被分配到堆栈上，而不是堆上，并且具有按值传递的语义。我个人认为这是对不可变类型的一个很好的补充，让你对程序的内存占用有更多的控制。

#### 枚举

[枚举](https://crystal-lang.org/docs/syntax_and_semantics/enum.html)允许我们对相关值进行分组:

```
enum Suit
  Spades
  Diamonds
  Clubs
  Hearts
end

Suit::Spades.value #=> 0 
```

Enter fullscreen mode Exit fullscreen mode

它们经常被用在 Rubyists 可能使用符号的地方，具有类型检查的额外优势(例如，`Suits::Club`而不是`Suits::Clubs`将导致编译器错误，而`:club`可能导致不正确的运行时行为)。因为枚举可以像类和结构一样定义自己的方法，所以它们对于分组相关的值和关联的行为非常有用。

#### 元组

像巨蟒水晶有一个[元组类型](https://crystal-lang.org/docs/syntax_and_semantics/literals/tuple.html)。元组是用`{element1, element2,...}`定义的，非常适合对相关值进行临时分组。在内部，它们也被用于多重赋值，比如`a, b = 1, 2`，Ruby 用`Array`代替。

### 总结

Crystal 是一种很好的语言，感觉很像 Ruby，同时通过 LLVM 编译成快速高效的代码。我希望这篇简短的介绍能够激起你对这门语言的兴趣，甚至激励你去尝试一下。在下一篇文章中，我计划展示一些更高级的特性，比如用宏进行元编程，与 C 和并发接口。敬请期待！