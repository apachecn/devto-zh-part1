# 如何制作一个带有边框和阴影的 CSS 语音气泡

> 原文：<https://dev.to/teroauralinna/how-to-make-a-css-speech-bubble-with-borders-and-drop-shadow-50l0>

这是一个如何创建带有边框和阴影的 CSS 语音气泡的例子。

## 简单的讲话泡泡

让我们开始创建一个简单的只有圆角和箭头的。

[![Speech bubble](img/40af14023f7c9e32971f83d64f93d12d.png)T2】](//images.contentful.com/nj2caiz7hkjw/6XUvlRC9HiYM62ameWM6gk/cc869ec173b44095efe3aecd09302b39/speech-bubble.png)

### HTML 代码

我们只需要一个 div 来创建一个简单的语音气泡。虽然`<blockquote>`是更多的语义元素，以防你用 bubble 来表示某人的评论。

```
<div class="speech-bubble">
    <p><strong>Demo speech bubble</strong></p>
    <p>This is a simple CSS speech bubble.</p>
</div> 
```

### CSS

```
.speech-bubble {
    background: #efefef;
    -webkit-border-radius: 4px;
            border-radius: 4px;
    font-size: 1.2rem;
    line-height: 1.3;
    margin: 0 auto 40px;
    max-width: 400px;
    padding: 15px;
    position: relative;
}

.speech-bubble p {
    margin: 0 0 10px;
}
.speech-bubble p:last-of-type {
    margin-bottom: 0;
}

.speech-bubble::after {
    border-left: 20px solid transparent;
    border-top: 20px solid #efefef;
    bottom: -20px;
    content: "";
    position: absolute;
    right: 20px;
} 
```

## 边框和阴影

现在我们有了一个基地。接下来让我们添加边框和阴影。我们需要为箭头添加新的 HTML 元素，因为我们需要用另一个元素模拟边框和阴影。

[![Speech bubble drop shadow](img/f443641378e9798dfbd6bb353ff8c3b4.png)T2】](//images.contentful.com/nj2caiz7hkjw/6puvO98aliM0ksY22K2OAI/07ebcc20e8fb18aafc7f114fee80b4ab/speech-bubble-ds.png)

### HTML 代码

让我们为箭头添加 HTML 元素`<div class="speech-bubble-ds-arrow"></div>`。

```
<div class="speech-bubble-ds">
    <p><strong>Demo speech bubble</strong></p>
    <p>This is CSS speech bubble with borders and drop shadow.</p>
    <div class="speech-bubble-ds-arrow"></div>
</div> 
```

### CSS

CSS 在这里稍微复杂一点，因为我们需要使用`::before`和`::after`伪元素。类别`.speech-bubble-ds-arrow`将用于箭头的阴影，`.speech-bubble-ds-arrow::before`将用于边界。伪素`.speech-bubble-ds-arrow::after`将箭。

```
.speech-bubble-ds {
    background: #efefef;
    border: 1px solid #a7a7a7;
    -webkit-border-radius: 4px;
            border-radius: 4px;
    -webkit-box-shadow: 4px 4px 0 rgba(0, 0, 0, 0.2);
            box-shadow: 4px 4px 0 rgba(0, 0, 0, 0.2);
    font-size: 1.2rem;
    line-height: 1.3;
    margin: 0 auto 40px;
    max-width: 400px;
    padding: 15px;
    position: relative;
}

.speech-bubble-ds p {
    margin-bottom: 10px;
}
.speech-bubble-ds p:last-of-type {
    margin-bottom: 0;
}

.speech-bubble-ds-arrow {
    border-left: 21px solid transparent;
    border-top: 20px solid rgba(0, 0, 0, 0.2);
    bottom: -25px;
    position: absolute;
    right: 15px;
}
.speech-bubble-ds-arrow::before {
    border-left: 23px solid transparent;
    border-top: 23px solid #a7a7a7;
    bottom: 2px;
    content: "";
    position: absolute;
    right: 5px;
}
.speech-bubble-ds-arrow::after {
    border-left: 21px solid transparent;
    border-top: 21px solid #efefef;
    bottom: 4px;
    content: "";
    position: absolute;
    right: 6px;
} 
```

好了，准备好了。您可以修改箭头边框值或绝对位置来更改箭头大小或位置。

这里是在 [CodePen](https://codepen.io/teroauralinna/pen/JMKyeB) 上的一段代码片段，用来玩泡泡风格。

[https://codepen.io/teroauralinna/embed/JMKyeB?height=600&default-tab=result&embed-version=2](https://codepen.io/teroauralinna/embed/JMKyeB?height=600&default-tab=result&embed-version=2)

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2017/how-to-make-a-css-speech-bubble-with-borders-and-drop-shadow)