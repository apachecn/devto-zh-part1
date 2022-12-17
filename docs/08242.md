# 语法 CSS 列表

> 原文：<https://dev.to/bronzehedwick/grammatical-css-lists-10on>

## 形势

您有一个需要呈现的项目列表，以逗号分隔，末尾有一个“and”。

例如:

`Cookies, rice, and farts`。

## 问题

这种显示传统上是在业务逻辑中完成的，比这个简单的输出更复杂。

## 解

我们可以使用纯 CSS，使用支持良好的伪类和伪内容。看哪！

### HTML

```
<ul class="list">
  <li class="item">Cookies</li>
  <li class="item">rice</li>
  <li class="item">farts</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

### CSS

```
/* Boilerplate to inline the list. */
.item {
  list-style-type: none;
  display: inline-block;
}

/* Add a comma after each item. */
.item:after {
  content: ', ';
}

/* Add the word "and" between the last two items. */
.item:last-of-type:before {
  content: ' and ';
}

/* Remove the comma after the last item. */
.item:last-of-type:after {
  content: '';
} 
```

Enter fullscreen mode Exit fullscreen mode

## 外卖

这个解决方案的美妙之处在于它简单、习惯性和声明性，并且具有对牛津逗号采取强硬立场的额外好处。

我在这个网站的“主题”显示中使用了这个解决方案。如果它适用于我的小博客，它也适用于其他任何东西！ <sup id="fnref:1">[1](#fn:1)</sup>

* * *

1.  我在博客上表达的观点不一定得到我的认可。<sup>【返回】</sup>

语法 CSS 列表的帖子最早出现在 T2 的克里斯·迪鲁卡的博客上。