# Perl6 多方法签名:自我反省

> 原文：<https://dev.to/scimon/perl6-multi-method-signatures--self-introspection-5aog>

# Perl6 多方法签名:自我反省

我是 Perl6 的忠实粉丝。它的一个更强大的特性是多子程序(和方法),允许你根据调用的特征以不同的方式处理调用。

虽然许多语言都具有多分派的能力，但 Perl6 不仅可以对参数的数量或类型进行分派，还可以根据参数值选择要遵循的代码路径。例如，经典的阶乘是递归的 multi sub。

```
multi sub fac(0) { 1 }
multi sub fac(UInt $f where $f > 0) { $f * fac($f-1) } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了两条代码路径。一种是用 0 调用 sub，另一种是用大于 0 的无符号整数调用。这很好，但是如果您有一个对象，并且希望基于对象字段值以不同的方式处理方法调用，该怎么办呢？

例如你有以下方法:

```
method retrieve-archived-data() {   
    # This won't work if our object has not be archived yet.
    # Fail this call
    fail "Can't Retrieve Data for an object that's never been archived" 
        unless $.archived;

    # Code here that gets archived data from some source. 
    # It takes a while and the above check was added because it used to take a 
    # while and then fail.
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，在某些情况下，这种方法会花费大量时间来查找存档的数据，如果从未存档过，就会失败。所以我们勇敢的开发者在脚本顶部做了一个检查来避免这种情况。这类事情的问题是它很快就会增加。如果能让它成为多种方法，那就太好了，但是怎么做呢？$变量不在签名中。

简单。我们补充一下！

通常当你调用一个对象方法时，你调用的对象在方法中是可用的，如`self`或`$`。但是你也可以给它一个不同的名字来使用这个方法，例如:

```
method set-name( $pet : $name ) {
    $pet!name = $name
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们通常用一个`:`而不是一个`,`来分隔对象名和其余的参数。像其他参数一样，我们可以在名称和分隔符之间添加一个`where`子句。有了这些知识，我们可以重写我们的方法:

```
multi method retrieve-archived-data( $ where ! $.archived: ) {
    # This won't work if our object has not be archived yet.
    # Fail this call
    fail "Can't Retrieve Data for an object that's never been archived";
}

multi method retrieve-archived-data() {
    # Code here that gets archived data from some source. 
} 
```

Enter fullscreen mode Exit fullscreen mode

注意尾部的`:`,如果你漏掉了它，它就不会工作。

通过这种方式，我们可以删除很多已经存在了一段时间的特殊情况。保持我们的代码简洁易读。

我希望你觉得有趣。我希望在这里写更多关于 Perl6 的帖子，以及为什么我认为它值得你花一些时间。

## 注

我已经更新了第二个例子，删除了多余的第二次检查。我还应该提到，`$`是匿名标量变量(当它用于签名时)。当你在一个方法中时`$.foo`是`self.foo`的另一种说法。

如果你愿意，你可以在`self`中命名一个引用的变量，例如:

```
method named-access( $name: ) {
    $name.field;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个方法中，`$name.field`、`self.field`和`$.field`都将引用同一个对象。

抱歉造成任何困惑。