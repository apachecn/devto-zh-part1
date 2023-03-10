# 在萨斯筑巢

> 原文：<https://dev.to/sarah_chima/nesting-in-sass-bme>

还记得 HTML 中的嵌套吗？当我们像这样把一个`<p>`放在一个`<div>`中，把一个`<span>`放在`<p>`中时，我们一直在做的事情？

```
 <body>
        <div>
            <p>
                <span></span>
            </p>
        <div>
    </body> 
```

Enter fullscreen mode Exit fullscreen mode

我打赌你知道。SCSS 允许我们以类似的方式嵌套 CSS 选择器。嵌套是创建 CSS 规则的捷径。因此，我们不需要写这么多行 CSS 来明确我们想要应用于某个元素的样式，而是简单地嵌套它。我们来举个例子。在 CSS 中，我们希望以特定的方式设计特定侧边栏的`li`,下面是我们的目标。

```
 #sidebar ul li {
      background-color: #F2F2F2;
      color: #404040;
    } 
```

Enter fullscreen mode Exit fullscreen mode

使用嵌套，我们将简单地执行以下操作并获得相同的结果。

```
 #sidebar {
       ul {
           li {
               background-color: #F2F2F2;
               color: #404040;
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这样更有条理，也更容易看出层次。它产生相同的结果。您可以将属性添加到选择器本身，而不仅仅是嵌套的选择器。

```
 #sidebar {
        position: fixed;
        height: 100%;

        ul {
            list-style-type: none;
            padding: 0;

            li {
                background-color: #F2F2F2;
                color: #404040;
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 #sidebar {
        position: fixed;
        height: 100%; }
    #sidebar ul {
        list-style-type: none;
        padding: 0; }
    #sidebar ul li {
        background-color: #F2F2F2;
        color: #404040; } 
```

Enter fullscreen mode Exit fullscreen mode

您也可以**嵌套媒体查询**。

```
 #sidebar {
        position: fixed;
        height: 100%;

        @media (max-width:768px) {
            left: -9999;
        }

        ul {
            list-style-type: none;
            padding: 0;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 #sidebar {
        position: fixed;
        height: 100%; }
    @media (max-width: 768px) {
        #sidebar {
            left: -9999; } }
    #sidebar ul {
        list-style-type: none;
        padding: 0; } 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以**嵌套 CSS 属性**。

```
 li {
       background: {
           image: url("image.jpg");
           position: fixed;
           }
       margin : {
           top: 1rem;
           left: 2rem;
           right: 1rem;
           }
      } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 li {
        background-image: url("image.jpg");
        background-position: fixed;
        margin-top: 1rem;
        margin-left: 2rem;
        margin-right: 1rem; } 
```

Enter fullscreen mode Exit fullscreen mode

真的，筑巢让生活变得更加容易。接下来让我们考虑如何使用&符号`&`来嵌套伪类和元素，并并排放置选择器。

**嵌套中的&符号**
在 Sass 中，`&`是嵌套中非常有用的特性。如果您希望选择器之间不留任何空间，我们必须使用&符号。当我们必须嵌套伪类和伪元素时，这尤其有用。如果你想了解什么是伪类和伪元素，你可以阅读[这篇文章](http://www.growingwiththeweb.com/2012/08/pseudo-classes-vs-pseudo-elements.html)。当我们必须并排放置类来选择包含这些类的元素时,`&`也很有用。我们用一个例子来理解这一点。

```
 .list {
       background-color: white;
       color: #404040;

       /* Pseudo-class */
       &:hover {
           background-color: blue; 
           color: white
       }

       /* Pseudo-element */
       &:before {
            content: "List ";
       }  

       /* Placing selectors side by side */
       &.active {
            border-left: 3px solid blue;
            color: blue;
       }
   } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 .list {
       background-color: white;
       color: #404040; }

   .list:hover {
       background-color: blue;
       color: white; }

   .list:before {
       content: "List ";
       width: 30px; }

   .list.active {
       border-left: 3px solid blue;
       color: blue; } 
```

Enter fullscreen mode Exit fullscreen mode

看到它们是如何并排连接在一起的了吗？这就是`&`的力量。

**小心！！！**
嵌套非常有用，让我们的 CSS 有条理，但我们不想误用它。我们滥用它的一种方式是过度嵌套选择器。看一个过度嵌套的例子。

```
 div {
       .sidebar{
            position: fixed;
            height:  100%;

            ul {
                list-style-type: none;
                padding: none;

                .list {
                    background-color: white;
                    color: black;

                    a {
                        text-decoration: none;
                        i{
                           &:hover {
                                background:  transparent;
                            }
                        }
                    }
                }
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 div .sidebar {
       position: fixed;
       height: 100%; }
   div .sidebar ul {
       list-style-type: none;
       padding: none; }
   div .sidebar ul .list {
       background-color: white;
       color: black; }
   div .sidebar ul .list a {
       text-decoration: none; }
   div .sidebar ul .list a i:hover {
          background: transparent; } 
```

Enter fullscreen mode Exit fullscreen mode

看最后一个选择器，那是六层嵌套。那太多了。这有很多问题。

1.  文件大小的增加:看看它添加到 CSS 文件中的代码量。这反过来会增加文件的大小并影响性能。
2.  具体性问题:过于具体本身就是一个问题。选择器中非常特殊的样式很难被另一个选择器覆盖。
3.  可维护性:这也是一个问题。顶层选择器中任何一个选择器的变化都会影响其他选择器。
4.  样式也不是那么可重用。

一个经验法则是不要嵌套超过 4 层。少于四级是优选的。与其有这么多层次的嵌套，你可以用它来命名你的选择器，并且仍然是特定的。参见下面的示例。

```
 .sidebar {
       position: fixed;
       height: 100%;

       &-list {
           background-color: white;

           &-link {
               text-decoration: none;
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将编译成

```
 .sidebar {
      position: fixed;
      height: 100%; }
   .sidebar-list {
      background-color: white; }
   .sidebar-list-link {
      text-decoration: none; } 
```

Enter fullscreen mode Exit fullscreen mode

看看这有多好。我们利用了嵌套的力量，并且仍然保持了代码的可维护性和可重用性。所以关键是以不影响性能和可维护性的方式使用嵌套。

就这样结束了。感谢您的阅读。:)
有任何问题或补充吗？留下评论。