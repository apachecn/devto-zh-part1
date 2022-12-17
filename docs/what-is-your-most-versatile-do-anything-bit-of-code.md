# 你最通用的代码是什么？

> 原文：<https://dev.to/traaidmark/what-is-your-most-versatile-do-anything-bit-of-code>

几乎在任何情况下，你都可以依靠这些代码来拯救自己，并把它们放在开发人员的工具箱中。

* * *

### 让我开始吧

这段代码至少在过去的 4 年里一直伴随着我。这里和那里有一点改变，但整体结构和目的始终保持不变。

```
jQuery(document).ready(function($) {
  $(".c-reveal").click(function(e) {
    var target = $(this).attr('href');
    $('c-toggle').addClass('c-inactive');
    if (!$(target).hasClass('c-inactive')) {
      $(target).addClass('c-inactive');
    } else {
      $(target).removeClass('c-inactive');
    }
    e.preventDefault();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

演示的最简单用例是简单地显示和隐藏一个 div。我们来看看下面的 HTML:

```
<a href="#my-div-name" class="c-reveal">Show / hide this div</a>
<div id="my-div-name" class="c-inactive">
    This is mind-blowing div contents
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在 css 中我有几个助手类，其中一个是`.c-inactive {}`,它基本上将附加的元素从屏幕上移除。再加上一些过渡，你就有了一个非常健壮的东西。

这个简单的 Jquery 可以支持几乎任何事情:

*   显示/隐藏 div
*   模态窗口
*   手风琴类型
*   等等