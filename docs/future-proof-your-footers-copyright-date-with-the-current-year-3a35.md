# 您页脚的版权日期与当前年份的未来证明

> 原文：<https://dev.to/michael/future-proof-your-footers-copyright-date-with-the-current-year-3a35>

这是新的一年，让你的网站看起来过时的最快方法之一就是不要更新页脚的版权年。

为了解决这个问题，最简单的方法是让你的网站为你生成当前的年份。

以下是一些精选语言的代码片段。

#### JavaScript

```
<script type="text/javascript">
  document.write(new Date().getFullYear());
</script> 
```

#### 红宝石

```
<%= Time.now.year %> 
```

#### PHP

```
<?php echo date("Y"); ?> 
```

#### Ember.js

```
// You'd most likely want to create a component to handle this. In the component's JS file, create a property like `currentYear`
...
currentYear: new Date().getFullYear(),
... 
```

```
// Then in your component's handlebars file, reference the property like this.
<footer>&copy; 2014 - {{currentYear}}</footer> 
```

#### 哲基尔

未来验证 Jekyll 项目的版权需要更多的手工操作，但是这里有一步一步的说明来验证你的 Jekyll 版权日期。

* * *

如果你有任何其他的例子，请在评论中留下，我会更新这篇文章。

* * *

最初发布于[李名炀](https://michaelsoolee.com/future-proof-copyright-date/)