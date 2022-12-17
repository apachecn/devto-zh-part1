# Django 中的自定义模板标签

> 原文：<https://dev.to/sroy8091/custom-template-tags-in-django>

## 模板标签

Django 有一个惊人的特性，就是将静态 HTML 文件模板化，作为动态页面。如果我想在网页上添加标题。

```
....
 Homee 
.... 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我想为不同的页面添加不同的标题呢？像**首页**为首页，购物车页面为**购物车**。

为此，我们使用 Django 中已经定义的模板标签。我用

```
{% block title %}
Home
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

用于将从基础模板扩展的每个页面中的标题块替换为空白标题块，如下所示

```
{% block title %}
{% endblock %} 
```

Enter fullscreen mode Exit fullscreen mode

使用另一个预定义的模板标签

```
{% extends 'base.html %} 
```

Enter fullscreen mode Exit fullscreen mode

## 如何创建一个？

现在，有时这些预定义的模板标签不足以一起解决你的问题。假设你想要一个标签，它可以给你一个被传递的对象和任意整数的元素个数的模。如果您希望 bootstrap 根据元素的数量有不同的列偏移量，这可能会很有用。

这些是你必须遵循的几个步骤

*   在项目设置文件的 INSTALLED _ APPS arrray 中的一个应用程序中创建一个 **templatetags** 文件夹
*   创建一个空白 ****init** 。py** 文件在 *templatetags* 文件夹中，使 Python 将目录视为包含包。
*   创建自定义标记文件，如 **custom_tags.py**
*   其中，我们必须为自定义模板标签编写函数

```
from django import template

register = template.Library()

@register.filter
def remainder(value, arg):
    return value % arg 
```

Enter fullscreen mode Exit fullscreen mode

这里我们注册了一个带有名称余数的自定义标签，它从模板中获取值和参数，就像这样

```
{{ value|remainder:arg }} 
```

Enter fullscreen mode Exit fullscreen mode

并返回余数。

## 怎么用？

首先，我们必须加载自定义标记。然后我们可以随心所欲地使用它们。我正在检查硬币的数量是否是偶数。

```
{% load custom_tags %}
...
...
{% if coin|remainder:2 == 0 %}
<p> You have even number of coins </p>
{% else %}
<p> You have odd number of coins </p>
{% endif %}
...
... 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的使用定制模板标签的例子。你可以在这里阅读 Django [关于这个主题的全部文档，以获得一些关于如何使用模板标签的更高级的想法。](https://docs.djangoproject.com/en/1.11/howto/custom-template-tags)

| 喜欢我的帖子吗？ |
| --- |
| [![](img/f4f06ea384c5e485d3474b718bbe6f95.png)T2】](https://ko-fi.com/A0A5WBC1) |