# 需要子弹吗？

> 原文：<https://dev.to/calebhearth/need-a-slug-9dd>

曾经需要一个文章、事件等标题的 URL。在你的 Rails 应用里？Rails 为创建这种类型的 slug 提供了一个免费的方法:`String#parameterize`。

您可以调用方法，该方法将按如下方式进行降排、断字、音译和删除标点:

```
> "Need a Slug?".parameterize
=> "need-a-slug" 
```

Enter fullscreen mode Exit fullscreen mode

这对于创建 URL 非常有用，但是请记住，它不是唯一的，所以两个字符串可能返回相同的参数化输出，如果使用不当会导致问题:

```
> "Need a Slug?".parameterize
=> "need-a-slug"
> "Need a slug.".parameterize
=> "need-a-slug" 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要在 Rails 应用程序之外从字符串中快速抓取一段代码，这也很有用。我只是用它来发布一个带有事件标题的参数化路径的事件。

```
$ heroku run console
> "Title of my Meetup, which was pretty long".parameterize
=> "title-of-my-meetup-which-was-pretty-long" 
```

Enter fullscreen mode Exit fullscreen mode

健康使用。

> 兰迪像鼻涕虫一样躺在那里！这是他唯一的辩护！
> 
> <figcaption>
> 拉尔夫成年后，在<cite>叙述一个圣诞故事</cite>
> </figcaption>