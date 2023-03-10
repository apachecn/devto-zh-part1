# 了解 CSS Calc()如何工作

> 原文：<https://dev.to/chuksfestus/understanding-how-css-calc-works-3lb1>

[![](img/3b914e9d5a6c0b853f6c4a257a8756bb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_HgGTFzY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/620/1%2ATwIyl61pW99byVdyxdNwEA.jpeg)

css3 calc()函数是一种执行简单数学运算的原生 css 方法。能够用代码来做数学是很好的，并且对于一门相当重数字的语言来说是一个受欢迎的补充。

但是有用吗？例如，我们可以使用 calc()来指定宽度是两个或更多数值相加的结果，而不是声明元素宽度的静态像素值。

```
.example {
    width: calc(100px + 50px);
} 
```

### 我就不能用前置处理器吗？

所有的 CSS 预处理程序都有数学函数，它们非常有用。如果你使用过像 SASS 这样的 CSS 预处理程序，那么你一定遇到过类似这样的事情

```
.example\_\_one {
    width: 100px + 50px;
}

// Or using SASS variables
$width-one: 100px;
$width-two: 50px;
.example\_\_two {
    width: $width-one + $width-two;
} 
```

上面的例子看起来很酷，但是 calc()函数提供了一个更好的解决方案，原因有二。首先是混合单位的能力。我们可以**将**百分比等相对单位与像素等绝对单位结合起来。预处理器不能这样做，因为这是渲染时必须发生的事情。

```
.foo {
    width: calc(100% - 3em);
} 
```

在此示例中。foo 元素的宽度总是比其父元素宽度的 100%小 3em。

第二，用 CSS 预处理器做数学表达式时，给浏览器的值就是表达式的结果值。

```
// Value specified in SCSS
.foo {
    width: 150px + 50px;
}

// Compiled CSS and computed value in browser
.foo {
    width: 200px;
} 
```

但是，使用 calc()时，浏览器解析的值是实际的 calc()表达式。

```
// Value specified in CSS
.foo {
    width: calc(100% - 50px);
}

// Computed value in browser
.foo {
    width: calc(100% - 50px);
} 
```

这意味着浏览器中的值可以更加动态，并随着视口的变化而调整。我们可以让一个元素的视口高度减去一个绝对值，它会随着视口的变化而改变。

### 使用 Calc()

calc()函数有四个简单的数学运算符:加(+)、减(-)、乘(*)和除(/)。基本上 calc() CSS 函数可以用在任何需要、、、<time>、或的地方。</time>

calc()函数也可以这样嵌套:

```
.foo {
    width: calc( 100% / calc(25px \* 8) );
} 
```

### 提供后援

对于不支持 calc()的浏览器，整个属性值表达式都会被忽略。这意味着我们可以轻松地提供一个后备静态值，供不支持的浏览器使用。

```
.thing {   
    width: 90%; /\* fallback if needed \*/   
    width: calc(100% - 3em); 
} 
```