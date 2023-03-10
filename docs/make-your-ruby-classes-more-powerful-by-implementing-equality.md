# 通过实现等式让你的 Ruby 类更强大

> 原文：<https://dev.to/jesus_castello/make-your-ruby-classes-more-powerful-by-implementing-equality>

*本帖最初发表于[rubyguides.com](https://www.rubyguides.com/2017/03/ruby-equality/)T3】*

在 Ruby 里怎么比较两个东西？如你所知，使用`==`...但是你知道吗,`==`是一种方法&,而不仅仅是语法。

您可以在自己的类中实现这个方法，使它们更加强大。这就是我想在这篇文章中谈论的。

## 平等基础知识

如你所知，你可以这样比较两个字符串:

```
"foo" == "foo" 
```

Enter fullscreen mode Exit fullscreen mode

如果**内容**相等，那么这将评估为`true`。这是因为`String`类实现了一个知道如何比较字符串的`==`方法。

但是如果`String`没有实现`==`呢？

然后 Ruby 将使用`Object`的`==`实现，默认为测试[对象标识](http://ruby-doc.org/core-2.3.0/Object.html#method-i-object_id)，而不是对象内容。

**例子** :

```
Object.new == Object.new # false
String.new == String.new # true 
```

Enter fullscreen mode Exit fullscreen mode

`Object`返回`false`的原因是因为两个新对象有不同的对象 id。

在`String`的情况下，因为它基于内容进行比较，并且两个新字符串具有相同的内容(它们是空的)，所以它返回`true`。

## 实现平等

现在，让我们利用您刚刚学到的知识，通过对它们进行比较，使您自己的类更加强大。

多亏了`==`方法，你可以精确地定义你自己的类的两个实例相等意味着什么。

**例子** :

```
class Product
  attr_reader :name, :price

  def initialize(name, price)
    @name, @price = name, price
  end

  def ==(other)
    self.name  == other.name &&
    self.price == other.price
  end
end

p1 = Product.new('book', 49)
p2 = Product.new('book', 49)

p1 == p2 # true 
```

Enter fullscreen mode Exit fullscreen mode

`==`方法指出，两个`Product`对象的名称和价格必须相同，才能被认为是相等的。

**记住**:

如果你不实现这个方法(或者使用`Comparable`模块，我在[我的 Ruby book](https://www.rubyguides.com/ruby-book/) 中解释过)这两个对象将使用它们的对象 id 来比较，而不是它们的值。

我还应该提到，如果你使用一个`Struct`，它已经为你实现了`==`。

## 三重等于呢？

你可能会疑惑`==`是方法的话，`===`也是方法吗？答案是肯定的:)

那么这两者有什么区别呢？

在 Javascript 中有一个明显的区别，如果对象类型不同，`==`会尝试将它们转换成相同的类型(`1`与`'1'`)。`===`代表“严格的”平等。

但是在 Ruby 中没有这样的东西。`===`的意思取决于实现它的类。

在很多情况下，它只是`==`的别名。

像`String`和`Object`里的。

这里有一个内置类的表格，它们赋予了`===`一个特殊的含义:

| 班级 | 意义 |
| --- | --- |
| 范围 | 如果 obj 是范围的元素，则返回 true，否则返回 false。 |
| 正则表达式 | 根据字符串匹配正则表达式。 |
| 组件 | 如果 obj 是 mod 或 mod 的某个后代的实例，则返回 true。 |
| 激活、触发 | 像`Proc#call`一样调用 obj 作为 proc 参数的块。它允许 proc 对象成为 [case 语句](https://www.blackbytes.info/2015/10/ruby-case/)中`when`子句的目标。 |

## 结论

在这篇文章中，你学习了如何通过实现`==`方法使你的类更加强大。你还了解了`==`和`===`的区别。

如果你喜欢这篇文章[请点击这里查看我的博客](https://www.blackbytes.info/) &订阅我的时事通讯:)