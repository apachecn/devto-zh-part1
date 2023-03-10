# 我的 CSS 愿望清单

> 原文：<https://dev.to/tomhodgins/my-css-wishlist>

## 选择器

*   `:previous`选择上一个元素同级
*   `:parent`选择父元素
*   `:closest(selector)`选择与选择器匹配的最近祖先
*   `:ancestor(selector)`选择所有与选择器匹配的祖先
*   `[attribute < number]`选择属性是否小于数字
*   `[attribute > number]`选择属性是否大于数字
*   `[attribute <= number]`选择属性是否小于或等于一个数字
*   `[attribute >= number]`选择大于或等于某个数字的属性
*   `:attribute(partial-name-*)`通过部分属性匹配进行选择
*   `:tag(partial-name-*)`通过部分标签名称匹配进行选择

## At-规则

*   `@element selector (condition) { stylesheet }`

## 单位

*   `ew`元素宽度的 1%
*   `eh`元素高度的 1%
*   `emin`等于`min(1ew, 1eh)`
*   `emax`等于`max(1ew, 1eh)`

## 值

*   `auto-expand`作为`width`和`height`属性的值
*   `offsetWidth`、`offsetHeight`、`offsetLeft`、`offsetTop`
*   `scrollWidth`、`scrollHeight`、`scrollTop`、`scrollLeft`
*   `innerHTML.length`，`value.length`
*   光标`X`位置，光标`Y`位置

## 属性

*   `aspect-ratio`根据元素的宽度和比率设置高度

## 功能

*   `clamp(min, mid, max)`用最小值和最大值限制可缩放值
*   `round(number)`给出与给定数字最接近的整数
*   `floor(number)`给出等于或小于给定数字的最大整数