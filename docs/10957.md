# Jekyll 的 Disqus 注释设置

> 原文：<https://dev.to/desiredpersona/disqus-comments-setup-for-jekyll-227o>

我最近在我的 Jekyll powered 网站上添加了 Disqus。本指南将保持代码整洁，并尽可能地可重用。

打开`config.yml`并添加以下代码。记得把`my_disqus_shortname`改成你自己的 Disqus 简称。

```
# Disqus Comments
disqus:
    # Leave shortname blank to disable comments site-wide.
    # Disable comments for any post by adding `comments: false` to that post's YAML Front Matter.
    shortname: my_disqus_shortname 
```

Enter fullscreen mode Exit fullscreen mode

在 Jekyll 的`_includes`文件夹中创建一个名为`disqus_comments.html`的文件，添加以下代码并保存该文件。

```
{% if page.comments != false and jekyll.environment == "production" %}

  <div id="disqus_thread"></div>
  <script>
    var disqus_config = function () {
      this.page.url = '{{ page.url | absolute_url }}';
      this.page.identifier = '{{ page.url | absolute_url }}';
    };
    (function() {
      var d = document, s = d.createElement('script');
      s.src = 'https://{{ site.disqus.shortname }}.disqus.com/embed.js';
      s.setAttribute('data-timestamp', +new Date());
      (d.head || d.body).appendChild(s);
    })();
  </script>
  <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码块包括你的 [Disqus 通用嵌入代码](https://disqus.com/admin/universalcode/)。您可能已经注意到，我将代码包装在了`{% if page.comments != false and jekyll.environment == "production" %}`和`{% endif %}` liquid if 语句之间。这包括 if 语句将允许您禁用 Disqus 对任何博客帖子的评论。你只需在 YAML 的帖子前面加上`comments: false`。作为一个额外的奖励，当 Jekyll 的环境设置为开发时，它还可以防止 Disqus 加载。

最后，打开您的`post.html`文件，并在 end `</article>`标签后添加下面的 liquid include 标签。当 Jekyll 的环境被设置为生产时，这将在你的博客文章下面加载 Disqus 评论。

```
{% if site.disqus.shortname %}
  {% include disqus_comments.html %}
{% endif %} 
```

Enter fullscreen mode Exit fullscreen mode

要在生产环境中构建 Jekyll 站点，请使用以下命令。

```
JEKYLL_ENV=production bundle exec jekyll build 
```

Enter fullscreen mode Exit fullscreen mode

搞定了。

Disqus 评论现在应该可以正常工作了。

如果你觉得这个教程有帮助，请在评论中告诉我。