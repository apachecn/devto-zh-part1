# Laravel 包使它成为每个项目的一部分

> 原文：<https://dev.to/michaelobi/laravel-packages-that-make-it-into-everyproject>

一直以来都有很多关于 PHP 的讨论，很多人声称它是一种“垃圾”语言。这是我的第一门编程语言。我知道它有一些缺点——和所有语言一样——但它一直是我在服务器端操作中的首选语言。我一直告诉人们我在 PHP 中学会了几乎所有我知道的 OOP 概念和设计模式。我最近写了很多 Java，但在办公室里我仍然被称为“PHP 爱好者”。我所有的个人项目现在和将来都是使用 Laravel 框架用 PHP 编写的。这是我喜欢的一个。解决问题令人兴奋，但没有重新发明轮子的奖励。这些是一些 Composer 软件包，它们几乎融入了我开始的每一个 Laravel 项目，每一个都让生活变得简单了一点。

## Laravel idea Helper

有人说 Laravel 的运作很有“魔力”。LOL。对于外观和雄辩模型的工作方式，ide 可能不知道如何提供语法高亮和其他好处。Barry Heuvel 的这个包生成一个您的 IDE 能够理解的文件，以提供正确的自动完成功能。它与 PHPStorm 和用于 IntelliJ IDEA 的 Laravel 插件一起工作很好，后者为刀片语法高亮显示和路由提供了帮助。

## 拉拉特鲁斯

Laratrust 是 Laravel 的一个基于角色的访问控制包。它由 Santiago GarcÃ维护，是 Zizaco 委托包的一个分支。Laratrust 是因为原始项目面临的维护和文档问题而创建的。这是我基于角色和权限的访问控制的首选包。它很容易设置，有很好的文档记录(一个全面的 wiki ),并为简单的内容显示逻辑提供了刀片指令。

## 可搜索

雄辩模型的搜索特征。它允许您对表执行简单的搜索，为每一列赋予不同的优先级。它还允许各种自定义查询。它使搜索表格变得轻而易举。nicols l pez Jullian 积极维护该网站。

## Laravel-订书机

Laravel-Stapler 是一个为 Laravel 创建的文件上传包，由 Code Sleeve 基于 PHP 包 Stapler 开发。如果您想上传一个文件并将其标记到一个模型实例，并且让您通过模型的属性轻松地显示或获得对文件的访问，这是一个很简单的例子，就像用户的头像一样。好的是，它可以根据您定义的不同质量水平来调整图像大小。它还支持文件上传到亚马逊 S3，而不是传统的文件系统。虽然它不是一个完全的救星，但是当我试图将多个文件固定到一个模型实例时，我遇到了问题。不过，它应该适用于更简单的用例，并且可以节省大量时间。文档非常简单，因此很容易上手。

## 大吃大喝

Guzzle 是一个使网络请求变得微不足道的包。这不是一个 Laravel 特定的包，但它值得一提。如果你不得不调用网络服务，你应该找 Guzzle。我们并不都喜欢卷曲。

## Laravel 数据表

一个用于 Laravel 的 jQuery 数据表 API。它提供了一个接口，jQuery Datatables 插件可以通过这个接口获取数据。它有助于消除获取集合中的所有数据并将其转储到视图中的需要。您只需提供一个 web 接口来返回这个包中的数据，并配置 Datatables 以挂钩到该路由。它将延迟加载数据，并允许数据表功能，如搜索和分页。从提供的文档开始很容易。它由 Arjay Angeles 维护

这些软件包已经为我节省了一段时间的时间和精力。如果你知道任何你认为应该在这个列表中的项目，请在下面的评论区留下，我会去看看的。我们可以就此召开一次会议。
干杯！