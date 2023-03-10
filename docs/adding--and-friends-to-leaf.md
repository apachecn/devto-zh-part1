# 将+=和朋友添加到 Leaf

> 原文：<https://dev.to/mortoray/adding--and-friends-to-leaf>

昨天我完成了 Codewars 中一个相当简单的形:一系列数字的总和。它很容易实现，但我觉得它好像缺少了什么。看起来是这样的:

```
defn sequence_sum = (start : integer, end : integer, step : integer ) -> ( total : integer ) {
    for v in std.incl_range( start, end, step ) {
        total = total + v
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

那件事让我很困扰。我已经习惯看到其他语言的`total += v`。虽然这只是一个简单方便的语法，但现在感觉有必要了。是时候将它添加到编译器中了。

> 我还添加了`incl_range`函数来清理循环。Leaf 没有传统的 C 风格 for-loop，我只有在找到需要它的用例时才会添加它。`incl_range`是标准库的一部分，用 Leaf 编写。

## 解析器构造

首先是解析，一如既往。我添加了操作员`+=`、`-=`、`*=`、`/=`、`++=`。这些都与`=`赋值操作符具有相同的优先级:首先将两边的所有内容组合在一起。简单地说，它有熟悉的命令语义。

我开始将支持作为内置功能添加进来，作为输入阶段的一部分。感觉有些不对劲，好像事情比它需要的更复杂。我放弃了这项工作，然后选择在解析器中翻译语法，所以:

```
a += b 
```

Enter fullscreen mode Exit fullscreen mode

变成:

```
a = a + b 
```

Enter fullscreen mode Exit fullscreen mode

然后让打字者正常处理，不知道新的语法。它节省了大量的工作，避免了任何新颖的打字规则。

> 转换作为树重写的一部分发生。你在上面看到的并不是真正的那种形式，而是树的形式，然后那种变形的扁平的树被`[funccall [â‘assign_op_add] [value-list [a] [5]]]]`转换成`[funccall [â‘assign] [value-list [a] [funccall [add] [value-list [a] [5]]]]]`。

## 分享整个故事

那很简单，但是那样重写是无效的。被我复制到两边的表情`=`可能会发生不好的事情。考虑这样的事情:

```
pine(a).b += c 
```

Enter fullscreen mode Exit fullscreen mode

如果`pine`有副作用怎么办？如果我像以前一样展开表单，它会对`pine(a)`求值两次。这显然是不可取的。

这种双重评价出现在几种情况下。typer stage 已经知道怎么处理了。可以创建一个共享表达式，该表达式允许使用两次，但只能计算一次。这不同于变量赋值，后者可能涉及类型推断和转换。共享表达式本身不进行转换。

> 这个共享表达式只不过是一个重用同一个子树的无操作转换。如果你愿意，我可以写一篇关于这部分的文章。我警告你，这将是一个有点枯燥的材料，因为许多关于编译器的东西往往是。

这是一个几乎微不足道的改变，使它可以从解析器中使用，允许解析器将表达式转换成这样:

```
shared_expr(pine(a).b) = shared_expr(pine(a).b) + c 
```

Enter fullscreen mode Exit fullscreen mode

其中两个`shared_expr`组件是同一个子树，导致只有一个评估。

我在下面迟钝的测试用例中提到了这一点。在实践中，它会自然地出现，但是需要额外的类型、函数和类。这个单元测试是最小的。

```
/* EXPECT(61) */
//ensures the expression is not being reevaluated
var x : [ a : integer, b : integer ] = [ 4, 5 ];

var count = 0
var data : shared [ a : integer ] := [ 5 ]

defn pine = -> {
    count += 1
    return data
}

pine().a += 1
trace(data.a)
trace(count) 
```

Enter fullscreen mode Exit fullscreen mode