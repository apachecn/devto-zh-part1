# 文件排序:Leaf 中的一个真实程序

> 原文：<https://dev.to/mortoray/sorting-a-file-a-real-program-in-leaf>

最后，Leaf 迈出了一大步:可以称之为实际程序的东西。周末，我完成了一个程序，它加载一个文件，对行进行排序，并将其写入控制台。这听起来可能不多，但它代表了该语言的一个重要里程碑。

```
var main = -> {
    var args = sys.get_args()
    var in_file = args#0

    var qi = shared_file.open_read(in_file)
    var s = qi.read_text()

    var all = split( s, '\n' )
    std.quicksort_comp( all, 0, all.size -1, string_less )

    for i in std.range(0,all.size) {
        std.print( [ all.data#i, "\n" ] )
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些函数也是用 Leaf 编写的，尽管在代码深处有一些嵌入式操作系统调用。

## 事情待办

代码中充斥着各种各样仍然需要完成的事情，或者做得不好的事情。让我们来看看其中的几个。

函数`quicksort_comp`实际上应该只叫做`quicksort`。从库中导出的参数重载函数不太正常。这个版本需要一个比较器，`string_less`。注意目前没有标准的字符串类型，所以我们需要一个自定义的比较器。

```
defn string_less = ( a : arrayï½¢charï½£, b : arrayï½¢charï½£ ) -> ( :boolean) {
    var at = 0
    loop at < a.size and at < b.size ? {
        //TODO: shouldn't need {}'s on return
        code_val(a#at) < code_val(b#at) then { return true }
        code_val(a#at) > code_val(b#at) then { return false }
        at = at + 1
    }

    return at >= a.size
} 
```

Enter fullscreen mode Exit fullscreen mode

`shared_file`也是`TODO`文件顶部的一部分:

```
//TODO: sys.file needs to be a "service" type that implies "shared"
alias shared_file : sys.file shared 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Leaf 中的所有类型都是通过引用来分配的，这当然不是`file`所希望的——这甚至没有意义。我将为此引入一个`service`类型，这将是一个自动共享的`class`，并且不能被复制。这就是 Leaf 如何揭示从根本上说是值的类型和从根本上说是资源包装器的类型之间的区别。

循环的最后一部分可能已经被清除了。我会创建一个`join`函数来连接所有的元素，然后执行一个`print`语句。

## 标准库

Leaf 基本上仍然缺少任何一种标准库。组成上述程序的位将开始迁移到这项工作中。请记住，几乎所有东西都需要编程，考虑一下上面对`split`的调用:

```
defn split = ( s : arrayï½¢charï½£, c : char ) -> {
    var o : std.vectorï½¢arrayï½¢charï½£ï½£ shared

    var at : integer = 0
    var last = at

    loop at < s.size ? {
        do s#at ==  c ? {
            o.push( arrsub(s, last, at - last) )
            last = at + 1
        }

        //TODO: incr/decr operators, or at least +=, -=
        at = at + 1
    }

    return o
}

defn arrsub = ( s : arrayï½¢anyï½£, offset : integer, length : integer ) -> {
    var o = arrayï½¢type_infer(s#0)ï½£(length)

    for i in std.range(0,length) {
        o#i = s#(i+offset)
    }

    return o
} 
```

Enter fullscreen mode Exit fullscreen mode

`file.read_text`需要创建一个`buffer`类来累积文件中的所有数据。也必须编写`vector`类，但是现在已经非常少了。

## 什么不是叶子？

`sys.file`、`std.print`、`std.quicksort`用树叶书写。最终，这些必须与操作系统对话。我使用 LibC 来执行实际的操作系统级活动，但是尽量减少调用次数，因为这会影响可移植性。

调用 LibC 函数是通过函数的`@import`声明来完成的。

```
@import("open") multi os_open : ( pathname : raw_array<:abi_char:>, flags : abi_int, mode : abi_int ) -> ( : abi_int ) raw no_throw 
```

Enter fullscreen mode Exit fullscreen mode

本例中最终使用的 LibC 函数是:

*   文件功能:`open`、`close`、`read`
*   记忆功能:`malloc`、`free`
*   控制台功能:`putchar`
*   `argv`和`argc`使用特殊的链接机制，而不是成为`main`的参数
*   一个最小的 C++导出函数来获取`_errno`,因为在 Leaf 中还不直接支持它

高级示例和这些低级函数之间最大的一层是 unicode 处理。所有的 UTF-8 解码和编码都在 Leaf 代码中处理。这些函数并不令人愉快:它们是位代码操作的集合。

我现在开始清理垃圾。