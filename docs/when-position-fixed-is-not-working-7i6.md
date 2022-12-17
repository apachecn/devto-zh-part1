# 当`位置:固定；`不工作。

> 原文：<https://dev.to/takaaki_suger/when-position-fixed-is-not-working-7i6>

你好，这是我第一次发帖:)

如果一个父元素有`transform`属性，子元素的`position: fixed;`就不起作用。

在这种情况下，子元素的行为类似于`position: absolute;`。

# 解决问题的方法

如果父元素使用变换动画，则替换而不使用变换动画。或者移除变换属性或设置`transform: none;`。