# 五分钟可访问性:输入和标签

> 原文：<https://dev.to/kennethlarsen/five-minute-accessibility-input-and-labels-20e9>

<figure>[![](img/19861afa582f462c8aeb16eeb2639b88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y8ESkXcc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANlqpTTAM8DbGl4paBmjE_g.jpeg) 

<figcaption>摄影:克里斯托弗·高尔</figcaption>

</figure>

似乎很多前端开发人员要么不知道为易访问性编码，要么不关心。当然，在不关注可访问性的组织中很难实现。但是开发一个包容性的网站总是开发者(和设计者)的责任。

在系列*五分钟可访问性*中，我将介绍一个你可以做的简单的事情来提高你的项目的可访问性。在这篇文章中，你将学习如何使用输入和标签。

即使您多年来一直在创建输入表单，也可能没有一个表单是可访问的。谢天谢地，从今天开始改变这一点非常简单。

要创建可访问的输入字段，你必须小心标签。创建唯一绑定到输入字段的标签对于创建可访问的表单是必要的。注意，标签指的是输入的 id。屏幕阅读器不支持与同一个表单元素关联的多个标签。

因此，您通常会看到这样的输入字段:

```
<label>Name:</label>
<input type=”text” name=”textfield”> 
```

Enter fullscreen mode Exit fullscreen mode

正如您所料，这对于非辅助技术的用户来说非常有用，并且这是开发表单的一种非常常见的方式。然而，这将是不全面的。因此，通过简单地连接标签和输入，我们现在有了一个可访问的输入字段:

```
<label for=”name” >Name:</label>
<input id=”name” type=”text” name=”textfield”> 
```

Enter fullscreen mode Exit fullscreen mode

通过将 for="name "添加到标签中，我们将这个标签唯一地链接到输入的 id。这是现在可以访问的，也有一些其他的好功能。用户现在可以单击标签本身来将焦点设置到表单元素上。这对一些有运动障碍的人很有用，尤其是在选择小复选框和单选按钮时。

好的一面是，这也适用于其他类型的输入。让我们来看看复选框:

```
<fieldset>
<legend>Select your pizza toppings:</legend>
<input id=”ham” type=”checkbox” name=”toppings” value=”ham”>
<label for=”ham”>Ham</label><br>
<input id=”pepperoni” type=”checkbox” name=”toppings” value=”pepperoni”>
<label for=”pepperoni”>Pepperoni</label><br>
<input id=”mushrooms” type=”checkbox” name=”toppings” value=”mushrooms”>
<label for=”mushrooms”>Mushrooms</label><br>
<input id=”olives” type=”checkbox” name=”toppings” value=”olives”>
<label for=”olives”>Olives</label>
</fieldset> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，每个复选框都有一个与标签匹配的 id。通过添加这几行，你就有了可访问的表单——那么为什么不从今天开始呢？

* * *

查看我的[失败日志](http://kennethlarsen.org/fails/)或者订阅我的[失败邮件简讯](https://tinyletter.com/failmail)

你也可以在推特上关注我。

* * *