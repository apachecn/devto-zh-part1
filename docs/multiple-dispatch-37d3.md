# 多重调度

> 原文：<https://dev.to/jj/multiple-dispatch-37d3>

[![Multiple versions of "hilos"](img/8f252099753e02bec7a878aa8306d407.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sbID42Pv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/by1rl1xd2k4mfqpkod3b.jpg) 
有时候你想为了一块数据把事情做好，但是你事先并不知道那块数据是由什么组成的。它可以是数组，可以是字符串，可以是更复杂的数据排列。在[动态类型语言](https://en.wikipedia.org/wiki/Type_system#Dynamic_typing)中经常发生:你想对一列东西做些什么，比如打印它或者简单地把它转换成一个字符串。您可以跳转到 hoops，执行一个`switch`子句，检查类型并分派相应的操作。或者您可以使用多个分派。

## 许多语言使用多重分派

他们用它将一个函数绑定到一个类型。例如， [Elixir](http://elixir-lang.github.io/) ，这是 Erlang 的 Rubyfication，它把 Erlang 变成了人类可以理解的东西:

```
defprotocol Stringifier do
  @doc "Can convert to string using tostring"
  def tostring( whatever)
end

defmodule Span do
  @doc "A few words"
  defstruct words: []
end

defimpl Stringifier, for: Span do
  def tostring(span), do: Enum.join(span.words," ")
end

defmodule Quoted do
  @doc "A few words and a quote"
  defstruct words: [], quote: "em"

  def i(string, quote) do
    "<#{quote}> #{string} </#{quote}>"
  end
end

defimpl Stringifier, for: Quoted do
  def tostring(quoted), do: Quoted.i(Enum.join(quoted.words," "),quoted.quote)
end 
```

一些语言要求程序员预先声明我们打算用于多重分派的函数；在长生不老药的说法中，它们被称为[协议](http://elixir-lang.github.io/getting-started/protocols.html)。我们需要用函数名和函数签名声明一个`protocol`。在这种情况下，它需要一个单独的乐器，我们称之为`whatever`。我们将把这个协议称为`Stringifier`。
我们将在这里使用标记的文本模型，正如我们到目前为止所做的。我们有一个`Span`，它是一个简单的单词列表，还有一个`Quoted`，它添加了某种引用结构。Elixir 中的模块是封装构造，但绝不是对象:它们是结构和你可以对它们做的事情，但不会被实例化或类似的事情。但是它们至少定义了一个结构，我们可以在以后使用它来定义多重分派:

```
defimpl Stringifier, for: Span do
  def tostring(span), do: Enum.join(span.words," ")
end 
```

由于协议是一种抽象接口，我们通过使用`defimpl`声明一个实现来定义一个遵循该接口的函数；`defimpl Stringifier`定义了该协议的实现。应用于什么？`for: Span`表示这将应用于特定数据`struct`。该函数的实际实现如下，它包装了我们定义的引用的`join` ed 单词。另一个实现遵循相同的方式，但是当我们想要使用它时:

```
defmodule Main do
  def main do
    words = %Span{words: ["A","few","words"]}
    IO.puts(Stringifier.tostring(words))
    quoted = %Quoted{words: ["More","words"], quote: "em"}
    IO.puts(Stringifier.tostring(quoted))
  end
end

Main.main 
```

无论我们使用什么，`IO.puts(Stringifier.tostring(quoted))`将检测数据类型，并将*消息*发送给相应的代码，以便正确打印。

我们知道 Perl6 把所有东西和厨房水槽都扔了进去。让我们看看

## perl 6 中的多重分派

我们已经看到了一些。语法使用多重分派来处理功能几乎相同的符号，如在引用结构中。因为语法是类，所以作为方法的多重分派规则是有意义的。但是让我们回到实际的数据结构，它可以丰富地表示段落:

```
role stringifiable {
    method to-string( Str $ligature ) { ... }
}

# Thanks to https://perl6advent.wordpress.com/2009/12/18/day-18-roles/
role Span does stringifiable {
    has @.words;
    method to-string( Str $ligature ) {
    return @!words.join( $ligature );
    }
}

class Quoted does Span {
    has $.quote;

    method to-string( Str $ligature ) {
    return self.þ ~  self.Span::to-string( $ligature ) ~ self.ø;
    }

    method þ () {
    return "<" ~ $!quote ~ ">";
    }

    method ø () {
    return "</" ~ $!quote ~ ">";
    }

}

class Heading does Span {

    submethod BUILD( :@!words ) {
    }

    method to-string() {
    return "\n<h1>" ~ self.Span::to-string( " " ) ~ "</h1>\n";
    }
} 
```

这相当长，因为我们把到目前为止所做的事情都扔进去了一点。我们定义了一个轻量级的[角色](https://dev.to/jj/assuming-roles-2dog)，它只是一个函数，没有实现。这个`{ ... }`会处理好的。我们将实现委托给角色用户，实际上我们强迫他们这样做。我们还有另一个角色，`Span`，由两个类使用，`Quoted`用于丰富的单词，`Heading`用于标题类。这是一个更加成熟的类，拥有`BUILD`函数和所有功能，但它需要这样，因为它必须将对象实例化函数(`new`)绑定到实例变量。[去过那里，做过那个](https://dev.to/jj/assuming-roles-2dog)。没什么大不了的。但是现在我们可能需要将一些我们事先不知道其类的对象转换成字符串。多重派遣救援:

```
# Stringifier for stuff above
proto stringify-chunk( | )                          {*}
multi stringify-chunk( $chunk where Quoted | Span ) {
    return $chunk.to-string( " " );
}
multi stringify-chunk( Heading $heading )           {
    return $heading.to-string();
} 
```

我们使用与之前在语法中完全相同的语法[。A `proto`说“嘿，这就是多重调度函数的样子”。Proto 是指`protocol`，或者可能是 prototype，但是 protocol 很适合我，因为这是他们在 Elixir 中的叫法，我们以前也见过。然而，Elixir 使用了一个占位符来说明实现将使用一个单词。Perl6 更灵活:](https://dev.to/jj/role-ing-on-the-grammars-1bd5) [|是一个`Capture`](https://docs.perl6.org/type/Signature#index-entry-parameter_%7C-Capture_Parameters) 它说“我不管你在这里扔什么，我会处理它的”。
proto(type | col)的实例使用`multi`，就像在`multi` ple dispatch 中一样。他们不需要声明他们是那个协议的一部分，只是它的一般形状照顾到这一点。但是第一个又以不同的方式使用了`|`。我们得到了`Span`和`Quoted`，但是它们几乎是一样的，所以它们可以使用相同的实现。`$chunk where Quoted | Span`使用了 Perl6 中的两个特性。`Quoted | Span`是一个[接点](https://docs.perl6.org/type/Junction)，其值为非此即彼。Perl6 允许通过`where`向类型声明添加约束。`$chunk`既可以是`Quoted`也可以是`Span`，我们以非常简洁的方式声明它。

> Perl6 完成任务的另一个证据。这里我们有一个`multi`函数，它也处理多种类型。

我们将在这里使用该函数:

```
class Paragraph {
    has stringifiable @!chunks;

    method new( *@chunks ) {
    return self.bless(:@chunks);
    }

    submethod BUILD( :@!chunks ) {}

    method to-string() {
    return "\n" ~ (@!chunks.map: { stringify-chunk( $^þ ) }).join(" " );
    }
} 
```

这是一个带有`new`的类。这种函数是一个方法(可以被继承)而不是一个子方法(不能被继承),它允许我们做一个不使用命名参数的更紧凑的定义。`*@chunks`是一个 *slurpy* parameters(使用*)获取所有数据并将其放入一个数组`@chunks`，然后我们对其进行处理并将其转化为实例变量。但是`@chunks`会做`stringifiable`。我们事先不知道每一段文字会是什么，但我们知道我们可以`string`它。这就是我们后来称之为`{ stringify-chunk( $^þ ) }`的原因。

我们如何使用它？

> 和往常一样，完整的代码是 GitHub 中的

```
my $heading  = Heading.new( words => "Here we are".comb(/\w+/) );
my $span     = Span.new( words => "This goes first");
my $quoted   = Quoted.new( words => "This is last",
              quote => "em" );

my $sentence = Paragraph.new( $heading, $span, $quoted );

say $sentence.to-string(); 
```

我们定义了三种不同的对象，它们在定义一个`Paragraph`时被忽略，最终通过使用`map`调用它们的 multi `stringify-chunk`被打印出来。结果是一个不可思议的美丽，但实用

```
<h1>Here we are</h1>
 This goes first <em>This is last</em> 
```

## 未完待续

语法是 Perl6 中所有新的美丽事物的纽带。我们将继续用新的特性扩展这个语法，使它几乎，但不完全是一个减价语法。