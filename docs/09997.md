# Perch CMS 中的自动版权年

> 原文：<https://dev.to/juanfernandes/automatic-copyright-year-in-perch-cms-5fi5>

我仍然被客户要求做很多事情，那些没有由 CMS 管理的网站的客户——是在页脚更新他们的版权年。

现在，这很容易使用 PHP 来完成，只需使用下面这行代码:

```
 <?php echo date("Y"); ?> 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果你允许你的 CMS 用户编辑版权文本，那么你就不能在一个高 HTML 模板中使用 PHP。

在阅读了这篇文章[将变量传递到模板](https://docs.grabaperch.com/templates/passing-variables-into-templates/)之后，我创建了这个代码片段，它用当前年份设置了一个变量，然后我将这个变量传递到 Perch 模板。

```
 <?php
        $curYear = date('Y');
        PerchSystem::set_var('curyear',$curYear);
        perch_content_custom('Footer', array(
            'template'=>'footer.html'
        ));
    ?> 
```

Enter fullscreen mode Exit fullscreen mode

这是我用来添加版权文本和年份的鲈鱼模板。

```
 <p class="copyright">©2015 - <perch:content id="curyear" /> <perch:content
    type="text" id="footer-copyright" label="Copyright Text" required="true" /></p> 
```

Enter fullscreen mode Exit fullscreen mode

这可能有点过了，我可以简单地在页脚模板中添加 PHP 代码，不允许用户更改版权文本，但我的版权声明是需要 CMS 管理的更大模板的一部分。

我希望这个技巧对你有用。让我知道，如果你看到任何我可以改进这个代码的方法。

你是如何设定版权年的？