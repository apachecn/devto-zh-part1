# 用于 Umbraco 的简单导航宏

> 原文：<https://dev.to/adamkdean/simple-navigation-macro-for-umbraco-2039>

我已经开始和 Umbraco 一起工作，所以至少在接下来的几个月里，我的很多帖子可能都是关于这个的。

这个片段可能是很多 Umbraco 开发人员的常识，但对我来说，我不得不在互联网上找了差不多 30 秒才找到它，所以为了让我将来更容易找到它，我把它贴在这里。不客气，未来的我。

```
<ul>
    @{ 
        var homeNode = Model.AncestorOrSelf("Home"); 
        var homeClass = Library.If(homeNode.Id == Model.Id, "selected", "");
    }

    <li><a href="@homeNode.Url" class="@homeClass">@homeNode.Name</a></li>      

    @foreach(var pageNode in homeNode.Children.Where("Visible"))
    {
        var pageClass = Library.If(pageNode.Id == Model.Id, "selected", "");

        <li><a href="@pageNode.Url" class="@pageClass">@pageNode.Name</a></li>
    }
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

没有嵌套支持，但很容易添加，[看这里](http://umbraco.com/help-and-support/video-tutorials/umbraco-fundamentals/razor-recipes/navigation.aspx)。