# 编写一个 Hexo 标签插件

> 原文：<https://dev.to/danieljsummers/writing-a-hexo-tag-plugin>

在将丹尼尔的每周祈祷文迁移到 T2 的 Hexo 的过程中，我们遇到了一个问题，我们确信一个标签插件可以解决这个问题。

## 问题

[Jekyll](////jekyllrb.com) 的 Markdown 解析器遵循原来的解析器，其中不处理 HTML 标签中的文本。在许多情况下，这可能是所希望的行为，因为您可以将原本会被翻译为 Markdown 的内容放在 HTML 标记之间，解析器/呈现器不会去管它。在大多数帖子中多次使用的一个共同特征是指向经文参考和引用文本块的链接。我们有一个`include`来自动化链接，但是我们需要一个特殊的类在`<blockquote>`标签上，这意味着所有的经文引用不能使用 Markdown(或者以“智能化”的引用结束；我们必须在这些引用中使用 HTML 实体。)我们还在引用的文本中包含了作为上标的韵文编号；更多标签。

它看起来像这样...*(“ref”CSS 类将文本变成红色)*

```
<blockquote class="bible">
  <p>
    <sup>11</sup> &hellip;And Jesus said, <span class="ref">&ldquo;Neither do I condemn you;
    go, and from now on sin no more.&rdquo;</span>
  </p>
  <cite>&mdash; <a href="https://www.biblegateway.com/passage/?search=John+8:11&amp;version=ESV"
    title="Read John 8:11 (ESV) at Bible Gateway">John 8:11</a>b <em>(ESV)</em></cite>
</blockquote> 
```

Enter fullscreen mode Exit fullscreen mode

如果你曾经编辑过 Markdown，你会发现所有的 HTML 代码在正常的文本流中是多么的不和谐；看看所有这些实体！

## 解

我们浏览了一下 [Hexo 插件列表](////hexo.io/plugins/)以找到一些例子，并开始编写一个插件来处理链接(上面例子中的`<cite>`内的部分)以及整个引用文本块。有些标签，像`{{ "{% codeblock " }}%}`标签，有一个开始标签和一个结束标签(`{{ "{% endcodeblock " }}%}`)；其他的，像`{{ "{% youtube " }}%}`标签，只是用标签传递参数。 *( [这里可以看到所有默认标签](////hexo.io/docs/tag-plugins.html)。)* Hexo 向标签插件传递两个参数——(开始)标签内的参数，加上内容(对于没有结束标签的标签为空)。插件调用返回的值在文档中被替换。

对于生成一个链接来说，这是相当容易的；它可以是一个内联标签，只需要解析参数并形成一个链接。对于报价，我们需要确保包含内容，Hexo 提供了一种通过 Markdown 渲染器运行内容的方法。我们正在达成一个解决方案！

当 Hexo 生成文件时，它会拾取并执行站点的`scripts`目录中的任何`.js`文件，所以最初的努力只是在本地进行的。参考链接看起来像这样...

```
hexo.extend.tag.register('esv', (args, content) => {
  // option parsing with RegEx, similar to the way their tags do

  let reference = arg.trim()
  let urlReference = reference.split('  ').join('+')

  return `<a href="https://www.biblegateway.com/passage/?search=${urlReference}&amp;version=${version}" `
    + `title="Read ${reference} (${version}) at Bible Gateway">${reference}</a>${extraText}${versionText}`
}) 
```

Enter fullscreen mode Exit fullscreen mode

...让减价文件从...

```
<a href="https://www.biblegateway.com/passage/?search=John+8:11&amp;version=ESV"
  title="Read John 8:11 (ESV) at Bible Gateway">John 8:11</a>b <em>(ESV)</em> 
```

Enter fullscreen mode Exit fullscreen mode

...到...

```
{% esv John 8:11 extra:b show-version %} 
```

Enter fullscreen mode Exit fullscreen mode

我们将链接代码重构为版本不可知的，并从`tag.register`函数中提取出来，这样我们就可以在 blockquote 引用中重用它。这使得 blockquote 的本地版本看起来像这样:

```
hexo.extend.tag.register('bible', (args, content) => {
  let text = hexo.render.renderSync({ text: content, engine: 'markdown' })
  return `<blockquote class="bible">${text}<cite>&mdash; ${generateRef(args)}</cite></blockquote>`
}) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着块引用可以支持内联引用的所有参数。我们还把`marked` Markdown 处理器换成了`markdown-it`处理器，这样我们就可以通过使用`^`字符来写上标了。回到我们在“问题”下的例子，我们生成相同批量报价的降价源现在是:

```
{% bible John 8:11 extra:b show-version %}
^11^...And Jesus said, <span class="ref">"Neither do I condemn you; go, and from
now on sin no more."</span>
{% endbible %} 
```

Enter fullscreen mode Exit fullscreen mode

## 插件

该插件在 [npm](////www.npmjs.com/package/hexo-tag-scripture) 上可用，完全[测试](////ci.appveyor.com/project/danieljsummers/hexo-tag-scripture)，其源码为[开放](////github.com/danieljsummers/hexo-tag-scripture)。如果你使用 Hexo，并希望在你的帖子中引用经文参考，并提供链接，读者可以自己查看文本——享受！

*本帖最初发表于[techblog.djs-consulting.com](https://techblog.djs-consulting.com/2017/writing-a-hexo-tag-plugin.html)T3】*