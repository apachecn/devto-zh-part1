# Umbraco 的主页链接宏

> 原文：<https://dev.to/adamkdean/home-link-macro-for-umbraco-932>

今天又是一个关于 Umbraco 的帖子，又是一个宏。这次是关于为站点标题创建一个主页链接，比如当你点击一个博客的名字时，它会带你回到索引。

这需要一点配置，不幸的是，这不能全部通过编程来完成。第一步是从 developers 选项卡创建一个宏。要做到这一点，你要去*开发者*标签，右键点击`Scripting Files`，点击创建，然后给宏一个名字，比如“主页链接”。

[![Create the macro](img/f7cff23fbb5f36d0d4ec8fa7d5767742.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--54roqRZV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/OcZ4Woz.png)

这会为您创建两个文件，脚本文件下的`HomeLink.cshtml`和宏下的`Home Link`。转到宏选项卡，并选择宏主页链接。这将加载宏编辑器。

[![Created files](img/7beb87ba6e354632e882d3cf1c1465d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--61Dulrps--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/ucg6eh3.png)

*重要的一步！*现在我们想添加一个网站名称的参数，我们将把它从页面传递给宏。选择参数选项卡，添加一个别名为`SiteName`，名称为`SiteName`，类型为`text`的参数。记得点击添加。

[![Parameters](img/7c3ed71cad0fe266f87c55cf4e5a9f05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QIrq4Nu5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/Kk4G7RO.png)

现在回到`HomeLink.cshtml`，输入以下代码:

```
@{ 
    var homeNode = Model.AncestorOrSelf("Home");
    var siteName = Parameter.SiteName;
}

<a href="@homeNode.Url">@siteName</a> 
```

Enter fullscreen mode Exit fullscreen mode

我们首先获得我们想要链接到的 home 节点，从中我们可以获得`Url`。然后，我们通过使用`Parameter.NameOfParameterHere`来获取已经传递的参数。之后，它又回到 HTML，一个简单的链接就足够了。然后，我们可以将宏包含在我们想要的任何标签中。

宏完成了。现在使用它。在您的模板中，您需要做的就是使用这个片段，记住填写网站的名称:

```
<umbraco:Macro SiteName="Your Site Name" Alias="HomeLink" runat="server" /> 
```

Enter fullscreen mode Exit fullscreen mode

答对了，你站起来了。

# 更进一步

我不喜欢硬编码，我想让它容易改变。所以在创建我的文档类型树时，我让所有的文档类型都从一个主类型派生出来，在这个主类型中，我有一些可继承的属性，比如元描述、关键字等，还有一个`siteName`属性。

如果我们将这个属性传递给宏，那么无论何时站点名称改变，我们都不需要担心模板的改变。

要传递一个属性，可以使用一个叫做`bracket syntax`的东西，看起来像这样:

```
<umbraco:Macro SiteName="[$siteName]" Alias="HomeLink" runat="server" /> 
```

Enter fullscreen mode Exit fullscreen mode

您将参数的名称作为属性标题，属性名称以$为前缀，用方括号括起来作为属性值。`ParameterName="[$nameOfProperty]"`。因此，如果您想将您的*正文*作为*输入值*参数传递，您可以使用`InputValue="[$bodyText]"`。

非常简单。只要确保你给宏添加了参数，否则它们就不会通过！

# 快速笔记...

现在，我必须提到的是这些`bracket`属性有一些不同的前缀。

如果属性就在当前页面上，您可以简单地使用 hash/pound 字符:`[#propertyName]`来访问它，但是如果属性继承自父页面，您需要使用递归前缀，这是一个美元符号，就像这样:`[$propertyName]`。

这意味着，如果属性的值为空，它将查看父页面的值，以及该值之上的父页面。有关更多信息，请参见 our.umbraco.org 的 [umbraco:宏元素](http://our.umbraco.org/wiki/reference/templates/umbracomacro-element)。