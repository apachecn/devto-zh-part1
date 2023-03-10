# CSS 101:结构化伪类

> 原文：<https://dev.to/damcosset/css-101-structural-pseudo-classes-924>

## 简介

CSS 允许你在元素状态改变时，文档改变时，或者在文档中发现某种模式时，对元素应用样式。我们称之为伪类。它们总是以冒号(:)开头。它们**总是**引用它们所附着的元素，而不是它们的后代。这是向前迈进时要记住的重要一点。

大多数伪类都是结构化的。它们处理你的标记的结构。我想针对第三段，第一个链接，最后一个列表项...

在本文中，我们将探索:

*   :空的
*   :独生子女
*   :仅类型的
*   :第一个孩子和:最后一个孩子
*   :第一个类型和:最后一个类型
*   :n-child()和:n-last-child()
*   :n-of-type()和:n-last-of-type()

### 得到了一些标记？

*:empty* 伪类目标空节点。这意味着文本和空白。

```
<p></p> => This is empty
<p> </p> NOT EMPTY
<p> 
</p>  NOT EMPTY
<p><!-- comment html --></p> Empty! 
```

Enter fullscreen mode Exit fullscreen mode

小心点。这个选择器还匹配像 img 和 input 这样的元素。

```
:empty {
    display: none;
    /* hides all your inputs, img and other empty nodes!!!*/
} 
```

Enter fullscreen mode Exit fullscreen mode

### 这里没有弟妹

如果元素是其后代的唯一子元素，则:only-child 伪类以这些元素为目标。

如果 span 元素是 p **的后代**的唯一子元素，那么`p span:only-child{}`将把它们作为目标。

```
<p>
    <strong>
        <span>I am the only child of strong which is a descendant of p ==> MATCH
        </span>
    </strong>
</p> 
```

Enter fullscreen mode Exit fullscreen mode

在我们的例子中，span 不必是段落的直接子元素。如果 span 是段落的后代，它也将成为目标。它必须是另一个元素的唯一子元素。如果你想确保这个跨度是这个段落的直接的唯一子段，你必须使用 *>* 子组合子:

`p > span:only-child{}`不会以我们上面的标记中的跨度为目标。

### 我有兄弟姐妹，但不像我。

让我们考虑一个不同的标记:

```
<p class="content">
    I am a <strong>dangerous</strong> individual when it comes to <em>CSS</em>. I can break <span>your design</span> in no time.
    <section>
        But I try my <span>best</span> to be responsible with <span>CSS</span>.
    </section>
</p> 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些跨度元素。只有当它是同级中唯一的 span 时，我才希望将它作为目标。我可以用 *:only-of-type* 来实现

如果任何 span 元素是其兄弟元素中唯一的 span 元素，那么`.content span:only-of-type{}`将把它作为 content 类元素内的任何 span 元素的目标。

这将针对我在段落中的跨度，而不是在部分中的跨度，因为有两个。

### 第一个和最后一个孩子

新标记:

```
<section>
    <h1>First!</h1>
    <p>
        <ul>
            <li>Hello</li>
            <li>List here</li>
        </ul>
    </p>
    <h2>Second!</h2>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

我要瞄准第一个 h1 和第一个 li。With :first-child，我可以使用下面的 CSS:

```
h1:first-child {}
li:first-child {} 
```

Enter fullscreen mode Exit fullscreen mode

一个常见的错误是认为这将选择 h1 元素的第一个子元素，或者 li 元素的第一个子元素。记住，伪类**总是**作用于它们所关联的元素。

这里，当 h1 和 li 元素是任何元素的第一个子元素时，我们将它们作为目标。在我们的例子中，父类型是不相关的。但是当列表项元素是无序列表元素的第一个子元素时，我们可以选择它们:

`ul li:first-child{}`

*:最后一个孩子*伪类与*:第一个孩子*相反，目标是...(要说出令人吃惊或高兴的事情)听着...最后一个孩子！！

当 span 元素是段落的最后一个子元素时，将选择它们。

*注:*如果把*:最后一个孩子*和*:第一个孩子*组合起来，得到的结果和*:独生子女*的结果一样

`p span:first-child:last-child{} === p span:only-child{}`

### 相同，但同类型？

伪类*的第一类*和*的最后一类*的工作方式相同，只是元素类型不同。

请标注:

```
<body>
    <h1>Selected: first inside body</h1>
    <section>
        <h1>Selected: first inside section</h1>
        <h1>Not selected</h1>
    </section>
    <h1>Not selected, second inside body</h1>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

这个规则`h1:first-of-type{}`将选择另一个元素中的第一个 h1 元素。它以在另一个元素中找到的第一个类型为目标。在我们的例子中，在每个元素内部，因为我们没有在规则中指定祖先。我们会忘记其他同类型的兄弟姐妹。

```
<body>
    <section>
        This is not targeted
        <section>This is targeted! Last child inside section.</section>
    </section>
    <section>I am targeted!!</section>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

`section:last-of-type{}`将目标定位到另一个元素内的最后一个 section 元素。

使用*:最后一种类型*和*:第一种类型*，您将定位到它的兄弟元素中的一个元素。您查找元素的子元素，检查它们的类型，并选择第一个(或最后一个)。你当然可以比我上面写的两条规则更具体:

```
body section:last-of-type{}
section h1:first-of-type{} 
```

Enter fullscreen mode Exit fullscreen mode

*注意:*就像之前一样，你可以组合 *last-of-type* 和 *:first-of-type* 来获得与 *:only-of-type* 相同的结果。

### 每第 n 个孩子

瞄准第一个或最后一个东西很酷，但有局限性。第二个，第四个，第八个呢...

有了*:n-child()*，我们可以针对任何我们想要的孩子。这个伪类接受一个整数(1，2，56)或一个简单的代数计算。

所以这个`li:nth-child(1){}`相当于`li:first-child{}`

`p:nth-child(2)`将把任何元素的第二个子元素作为目标，只要这个子元素是一个段落。

只要 h2 元素中的第四个子元素是类 *main* 的，那么`.main h2:nth-child(4)`就会指向这个子元素。

你可以通过一个简单的操作来增强 n-child 的能力。它采用的形式是 *an + b* 。a 和 b 是整数。部分(+ b)是可选的，它定义了起始点。你可以这样使用它:

`li:nth-child(4n + 1){}`

这将从第一个(+ 1)开始选择每第四个列表项(4n)。没有第零个索引，它从 1 开始。所以它的目标是第 1，5，9，13 项....

如果我省略了第二部分(+ 1)，我会把目标放在第 4、8、12、16 项上...

要选择奇数和偶数的孩子，可以使用关键字*偶数*和*奇数*。

`tr:nth-child(odd){}` === `tr:nth-child(2n + 1)`
`tr:nth-child(even){}` === `tr:nth-child(2n)`

您也可以使用*:n-last-child()*，其工作方式与*:n-child()*相同，但它从兄弟列表中的最后一个元素开始向后计数。

如果你想确保最后一项总是被锁定，你可以做:`li:nth-last-child(odd){}`。这针对从最后一个项目开始的项目 n 1、3、5、7。

您还可以在这两个伪类的可选部分使用负数:

`tr:nth-child(2n - 1)`

### 第 n 种类型

以类似的方式，你有*:第 n 个类型*和*:第 n 个最后类型*。概念完全相同，除了您只处理元素类型。

`.danger p:nth-of-type(2){}` = >将从具有类危险的元素开始向下的第二段作为目标。

`span:nth-of-type(3n + 2){}` = >将从找到的第二个跨度开始，以每第三个跨度为目标，从任何元素开始递减。

## 结论

结构化伪类允许您充分利用标记的结构。你不必总是滥用类来确保你的标记像你想要的那样有针对性。聪明地使用伪类会好得多。