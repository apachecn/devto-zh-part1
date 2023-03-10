# 具有等宽步骤和最后一步灵活宽度的导航向导示例

> 原文：<https://dev.to/teroauralinna/navigation-wizard-example-with-equal-width-steps-and-flexible-width-of-last-step-4g04>

这篇博文的标题有点怪异，但我会尽力解释。不久前，我在工作中遇到了一个有趣的挑战。我的任务是创建一个导航向导，除了最后一步，所有的步骤都应该有相同的宽度。最后一步的宽度应取决于文本宽度。然而，最后一步的宽度也不应该超过其他步骤的宽度。

[![Navigation wizard example](img/c275e8fa0ffe741824a0b900e9ce64c3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uB54gZzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.contentful.com/nj2caiz7hkjw/1EHOomRESIMkYSaUiKm6g2/8474d014240b815bd08dd831f22e2648/navigation-wizard-example.png)

最后，用 CSS Flexbox、calc 和 counters 实现起来相当容易。下面的演示可能比一千个单词更好地解释了这一点。这个例子并不适合小尺寸的手机屏幕。它可以扩展，但在某种程度上它不再适合。

[https://codepen.io/teroauralinna/embed/MrmryX?height=600&default-tab=result&embed-version=2](https://codepen.io/teroauralinna/embed/MrmryX?height=600&default-tab=result&embed-version=2)

## 1。导航向导的第一个版本

### HTML

让我们首先为向导步骤创建一个简单的列表和链接。

```
<ul class="wizard">
  <li class="wizard-step">
    <a href="#1" class="wizard-step-link">
      This is the first step
    </a>
  </li>
  <li class="wizard-step">
    <a href="#2" class="wizard-step-link">
      This is the second step
    </a>
  </li>
  <li class="wizard-step">
    <a href="#3" class="wizard-step-link">
      This is the third step
    </a>
  </li>
  <li class="wizard-step">
    <a href="#4" class="wizard-step-link">
      This is the fourth step
    </a>
  </li>
  <li class="wizard-step">
    <a href="#5" class="wizard-step-link">
      This is the last step with flexible width
    </a>
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

## CSS

```
.wizard {
  counter-reset: stepNo;
  display: -webkit-flex;
  display: -ms-flex;
  display: flex;
  margin: 0;
  padding: 0;
}

.wizard-step {
  -webkit-flex: 1 0 0px;
      -ms-flex: 1 0 0px;
          flex: 1 0 0px;
  list-style: none;
  padding: 0 2% 0 0;
  position: relative;
}
.wizard-step::before {
  border-top: 3px solid #333;
  content: "";
  left: 0;
  position: absolute;
  right: 0;
  top: 25px;
}
.wizard-step:last-child {
  border: 0;
  margin: 0;
  padding: 0;
}
.wizard-step:last-child::before {
  border: 0;
}

.wizard-step-link {
  color: #333;
  display: block;
  font-size: 1rem;
  font-weight: bold;
  position: relative;
  text-decoration: none;
  z-index: 10;
}
.wizard-step-link:before {
  background: #333;
  color: #fff;
  counter-increment: stepNo;
  content: counter(stepNo) ".";
  display: block;
  font-size: 2rem;
  height: 50px;
  line-height: 50px;
  margin-bottom: 12px;
  text-align: center;
  width: 50px;
}
.wizard-step-link:hover,
.wizard-step-link:focus {
  color: #000;
  text-decoration: none;
}
.wizard-step-link:hover:before,
.wizard-step-link:focus:before {
  background: #fff;
  border: 1px solid #333;
  color: #333;
} 
```

Enter fullscreen mode Exit fullscreen mode

[CSS 计数器](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Lists_and_Counters/Using_CSS_counters)功能用于生成步进编号。这很容易保持步骤数的动态性，而不管有多少个步骤。

## 2。为最后一步添加灵活的宽度

现在我们有了等宽步长的导航向导。我们需要稍微改变最后一步的样式，使其基于文本宽度更加灵活。

### CSS

```
.wizard-step:last-child {
  -webkit-flex: 0 0 auto;
      -ms-flex: 0 0 auto;
          flex: 0 0 auto;
}
.wizard-step:last-child:nth-child(2) {
  max-width: calc(100% / 2);
}
.wizard-step:last-child:nth-child(3) {
  max-width: calc(100% / 3);
}
.wizard-step:last-child:nth-child(4) {
  max-width: calc(100% / 4);
}
.wizard-step:last-child:nth-child(5) {
  max-width: calc(100% / 5);
}
.wizard-step:last-child:nth-child(6) {
  max-width: calc(100% / 6);
}
.wizard-step:last-child:nth-child(7) {
  max-width: calc(100% / 7);
}
.wizard-step:last-child:nth-child(8) {
  max-width: calc(100% / 8);
}
.wizard-step:last-child:nth-child(9) {
  max-width: calc(100% / 9);
}
.wizard-step:last-child:nth-child(10) {
  max-width: calc(100% / 10);
} 
```

Enter fullscreen mode Exit fullscreen mode

浏览器很好地支持了 Calc ,但是为了扩大浏览器的支持，你当然可以自己计算`max-width`。

以下 SCSS `for`循环片段可用于生成 CSS 规则和计算`max-width`值。

```
@for $i from 2 through 10 {
  .wizard-step:last-child:nth-child(#{$i}) {
    max-width: (100% / $i);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您对实现有任何想法或如何改进的提示，请告诉我！

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2018/navigation-wizard-example)