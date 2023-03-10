# 当应用“位置:固定”时，继承父元素的宽度

> 原文：<https://dev.to/phillt/inherit-the-width-of-the-parent-element-when-position-fixed-is-applied>

今年早些时候，我在 SOF 上遇到一个问题，问`fixed`的子元素如何继承其父元素的宽度。我震惊地看到一个建议使用`inherit`的解决方案被多次否决！这不是正确的解决方法。所以我写了一篇关于如何实现它的技术性文章。这是针对开发人员的答案。

事实是，当子元素的宽度固定时，您不能可靠地使用`inherit`来设置它的宽度。这与对`fixed`实际工作方式的误解或不理解有关。

### 理解固定

与`absolute`、`fixed`、**不同的是，`fixed`、**不会从其最近的亲戚**父节点中定位自己。相反，`fixed` **相对于视窗**定位自身。视口将始终保持固定，这就是为什么你会得到这样的效果。**

也就是说，**无论何时你“继承”任何宽度，它都将与视窗**相关。因此，当我们试图将目标元素的宽度设置为其父元素的宽度时，没有任何好处。

*详细了解[职位](https://css-tricks.com/absolute-relative-fixed-positioining-how-do-they-differ/)的不同行为。*

## 快速解答

现在我们对`fixed`有了更好的理解，这里有两种方法可以实现这一点

### 纯 CSS

我们可以使用**纯 CSS** 来解决这个问题，但是**我们需要预先知道宽度**。假设它的父元素是`300px;`

```
.parent{
    width: 300px;
}

.parent .fixed_child{
    position: fixed;
    width: 300px;
} 
```

Enter fullscreen mode Exit fullscreen mode

### JS

现在有了**移动设备**，我们**没有**真正**拥有**设置**宽度**的奢侈，尤其是超过`300px`的任何东西。使用百分比也不行，因为它相对于视口，而不是父元素。**我们可以使用 JS** ，在本例中是使用 jQuery 的**来实现这个**。让我们来看一个**函数**，该函数**将总是在给定时刻设置 paren** t 的宽度:

```
 function toggleFixed () {
      var parentwidth = $(".parent").width();      
      $(".child").toggleClass("fixed").width(parentwidth);        
  } 
```

Enter fullscreen mode Exit fullscreen mode

css:

```
.fixed{
    position:fixed;
} 
```

Enter fullscreen mode Exit fullscreen mode

*在 [CodePen](http://codepen.io/Philll_t/pen/XMVYEa) 中查看 T3*

### 动态宽度

这很好，但是如果当用户还在页面上时，窗口的宽度发生了变化(T2 ),改变了父元素，会发生什么呢？虽然父对象可以调整其宽度，但子对象将保持该函数设置的宽度。**我们可以用 [jQuery 的](https://api.jquery.com/resize/) `resize()`** 事件监听器来解决这个问题。首先，我们需要将我们创建的函数分成两部分:

```
function toggleFixed() {
   adjustWidth();
   $(".child").toggleClass("fixed");
 }

 function adjustWidth() {
   var parentwidth = $(".parent").width();
   $(".child").width(parentwidth);
 } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经分离了每个部分，我们可以单独调用它们，我们将包括我们原来的按钮方法，它切换固定和宽度:

```
$("#fixer").click(
     function() {
       toggleFixed();
     }); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们还将 resize 事件侦听器添加到窗口中:

```
 $(window).resize(
     function() {
       adjustWidth();
     }) 
```

Enter fullscreen mode Exit fullscreen mode

*在 [CodePen](http://codepen.io/Philll_t/pen/dvJKqQ) 中查看 T3*

那里！现在我们有了一个固定的元素，当窗口调整大小时，它的大小也会随之调整。

## 结论

我们已经通过理解`fixed`位置及其局限性来应对这一挑战。与 Absolute 不同，`fixed`仅与视口相关，因此不能继承其父级的宽度。

为了解决这个问题，我们需要使用一些 JS 的魔法来实现这个目标，这在 jQuery 中并不常见。

在某些情况下，我们需要一种动态方法，使用不同宽度的缩放设备。同样，我们采用了 JS 方法。