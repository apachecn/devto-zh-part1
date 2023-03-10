# 通过 Omnibar 搜索你的站点——甚至是静态站点

> 原文：<https://dev.to/rpalo/search-your-site-via-the-omnibar---even-static-sites-41m>

不久前， [Tiffany White](https://dev.to/twhite) 在 Dev.to 上发布了一个更新，记录了一个新功能:Chrome Omnibar 搜索。

[![Searching with the omnibar](img/e886640aadcfe36c3cf06404fedc6fb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PIdVsFqm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assertnotmagic.com/img/omnibar-in-action.gif)

在我使用的许多流行网站上，这是我认为理所当然的功能之一，没有考虑到我可以在自己的网站上拥有这一功能！所以我研究了如何实现它，我想和其他人分享我的发现。这篇文章的部分观点是，我从我找到的一堆不同的搜索结果中拼凑出我的建议，所以如果我错过了一些重要的东西，让我知道，我会把它添加到指南中(并在我的博客上更新我是如何做到的)。

# 第一部分:开放搜索

你需要做的第一件事(也是几乎唯一的一件事)是添加一个包含以下内容的`/opensearch.xml`文件(在你网站的根目录下)。我在下面的链接中使用我的博客。你可能会想改变一些东西来匹配你的网站，除非你真的喜欢我，想把你的搜索流量转给我。

[![XML Snippet.  For full code see https://github.com/rpalo/rpalo.github.io/blob/master/opensearch.xml](img/25a0df9adcfd6b924417db6884d69daa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pFbXmV_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qpmr14qropy4e1yhbjxz.png)

出于某种原因，我无法获得 markdown XML 代码栏来呈现，所以要查看代码的常规版本，请访问我的博客“回购”。

每个项目的快速描述:

**简称**:您(和浏览器)用(< 16 个字符)指代您的搜索引擎的简称。

**描述**:较长的描述(< 1024 个字符)。

**InputEncoding** :期望字符的编码。

**图片**:您选择的图标的绝对路径(或 Base64 数据表示),该图标会显示在某些客户端的搜索栏旁边(见下图)。确保`type`与图像的文件类型相匹配(无论是`.png, .jpg, or .ico`)。

**Url** :这是当用户完成搜索时被 pinged 的 Url。注意，它使用了一个“神奇的”{searchTerms}。它会用用户输入的任何内容填充 URL。

**Url(第二个)**:你可以包含这个，当你对`opensearch.xml`进行修改时，如果，一些浏览器会自动更新自己的~~。一会儿会有更多的内容。~~

SearchForm/moz:SearchForm :保存搜索页面链接的标签。我相信这是因为如果你的用户对 Omnibar 感到失望，他们会说“呸。只要带我去他们的搜索页面，我就在那里做。”

如果你对我的解释不满意，Firefox OpenSearch 文档对这些术语、涉及的内容和要求有一些可靠的指导。如果您想使用 Omni-bar 来 ping 一个 JSON 端点，它们也会提供帮助。

### 可能的问题:在开发期间更新

当你开始设置时，你可能会在某个地方打错字或出错。然后你会想你怎么清除@！# $打开缓存，以便浏览器重新加载新配置。对于 Chrome，你可以右击 Omnibar，选择“编辑搜索引擎”。在菜单中找到你工具栏并删除它，这样你的浏览器就可以获取你最近的更新。理论上，上面的自动更新网址应该可以让你免于这种情况，但这可以成为你的备用核选项。

[![How to clear the OpenSearch cache](img/0c260c62c2ebeac77bfc4cff6b98cae1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HF_qIdiq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assertnotmagic.com/img/omnibar-cache.png)

## 第二部分:链接标签

接下来，您需要做的就是将一个`link`标签放入您的`head`部分。我觉得只要放在你的主页上就够了。有人纠正我，如果这不是真的。

```
<!-- index.html -->
<!DOCTYPE HTML>
<html lang="en">
  <head>
    <!-- ... All the rest of your head tags -->
    <link rel="search"
          type="application/opensearchdescription+xml"
          title="Search assert_not magic"
          href="/opensearch.xml">
  </head>
  <!-- ... the rest of your site -->
</html> 
```

Enter fullscreen mode Exit fullscreen mode

你应该可以走了！如果您有自己的后端，您可以处理上面的 XML 文档中的`GET`请求中的`q={searchTerms}`到您的搜索结果页面。但是如果你没有*有*后端呢？

## 奖励部分:静态站点

没有后端？别担心！我将向您展示如何让它与您的动态 JavaScript 搜索表单一起工作。在[之前的一篇文章](https://assertnotmagic.com/2017/11/11/static-site-search-with-vue/)中，我讲述了我是如何给我的静态生成的博客添加搜索功能的。我将使用这篇文章末尾的代码作为起点。对于那些懒得点链接的:

```
const app = new Vue({
  el: '#app',
  data: {
    search: '',
    post_list: posts
  },
  computed: {
    filteredPosts() {
      return this.post_list.filter( post => {
        return `${post.tags}  ${post.title}`.toLowerCase().includes(this.search.toLowerCase());
      });
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们将要使用的技巧是，当我们创建 Vue 支持的搜索组件时，我们将检查我们的 URL 参数。将它放在上面的代码之前。

```
const params = new URLSearchParams(location.search); 
```

Enter fullscreen mode Exit fullscreen mode

`URLSearchParams`是解析 url 中参数(在`?`之后的所有内容)的一种便捷方式。虽然对浏览器的支持不是 100%，但我会假设 a)你已经搞清楚了 transpilation，b)你知道[如何@我](https://twitter.com/paytastic)，我会帮你的。

现在我们已经有了参数(如果它们存在的话)，让我们使用它们让我们的搜索组件预加载被搜索的项目。

```
const params = new URLSearchParams(location.search);

const app = new Vue({
  el: '#app',
  data: {
    search: params.has('q') ? params.get('q') : '',  // <- This is the key part
    post_list: posts
  },
  computed: {
    filteredPosts() {
      return this.post_list.filter( post => {
        return `${post.tags}  ${post.title}`.toLowerCase().includes(this.search.toLowerCase());
      });
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们的 URL 有一个`q`参数(如果它来自全向搜索，就会有这个参数)，我们将它加载到搜索框中，这会导致我们的搜索结果更新。否则，我们从一个空白搜索框开始，照常进行。

[![Here's how it works on my site](img/e886640aadcfe36c3cf06404fedc6fb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PIdVsFqm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assertnotmagic.com/img/omnibar-in-action.gif)

就是这样！就像我说的，如果你知道我遗漏的任何 OpenSearch 的最佳实践，或者我可以更好/更酷地使用它的方法，请告诉我，我会尽力更新帖子。

**额外参考:**

*   [使用 open search-Wynn Netherlands 增强您的网站](https://wynnnetherland.com/journal/enhance-your-site-search-with-opensearch/)
*   [Mozilla OpenSearch 文档](https://developer.mozilla.org/en-US/docs/Web/OpenSearch)
*   [关于 OpenSearch 的重要性——斯科特·汉瑟曼](https://www.hanselman.com/blog/OnTheImportanceOfOpenSearch.aspx)

* * *

*原帖 [`assert_not magic?`](https://assertnotmagic.com/2017/12/05/search-your-site-via-omnibar-even-static-ones/)*