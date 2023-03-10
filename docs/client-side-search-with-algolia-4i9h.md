# 使用 Algolia 进行客户端搜索

> 原文：<https://dev.to/teej/client-side-search-with-algolia-4i9h>

我将使用 [Algolia](https://www.algolia.com/) 创建一个搜索特性。我也将使用 JavaScript 的一些新特性，比如 async/await，但是没有它也可以完成，我将提供适用的替代方案。这将涵盖基本的大纲，并在文章的结尾提供了一个更完善的版本的链接。

它不会成为每个人的灵丹妙药，但看看它是如何工作的是很有趣的，它可以成为添加到您的工具包中的解决方案。

## 我需要什么？

*   一个 Algolia 帐户(有一个免费层，只要你把他们的标志添加到你的搜索区域)
*   您希望能够被搜索到的一些内容
*   将记录添加到索引中的方法(你可以手动完成，使用 API，或者如果你使用 CMS 或框架，有大量的解决方案随时可用)

“记录？指数？你在说什么？“索引是保存您希望可搜索的数据的东西，而记录是这些数据的一部分。例如，您可以有一个名为“文章”的索引，它由记录组成，每个记录代表一篇文章。有点像:

```
<ul data-index="posts">
    <li data-record="post">
        <h2>My Post Title</h2>
        <p>Oh hey lorem ipsum, dolor sit amet consectetur? Haha, good one adipisicing elit...</p>
    </li>
    <li data-record="post">
        ...
    </li>
    ...
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

或者是我毁了它。Nam facilis doloribus？基本上，你可以告诉 Algolia 它可以搜索你的帖子的哪些部分。这可以是标题，一些内容，标签，类别等…你可以根据重要性来决定它们的权重。所以一个与你的文章标题匹配的查询会把这个结果推到另一篇文章内容匹配的顶部。

在 Algolia 仪表板的 API 部分，您可以找到您的应用程序 ID、您的仅搜索 API 密钥和您的管理 API 密钥。如果你使用的是 CMS 或带有 Algolia 集成的框架，会有空格供你输入。您还可以限制 HTTP 推荐人，以确保搜索只在您选择的域上进行。

## 代码

我将使用 [JavaScript 搜索客户端](https://github.com/algolia/algoliasearch-client-javascript)，更具体地说，将使用限制为仅搜索的 lite 客户端，这将完成工作。它也将节省文件大小。

来装吧:

```
npm install algoliasearch --save 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们将设置我们的搜索输入:

```
<div class="c-search js-search-container">
  <div class="c-search__inner">
    <label class="c-search__label" for="s">Search:</label>
    <input type="search" class="c-search__input js-search-input" id="s">
    <img srcimg/algolia.svg" class="c-search__credit">
    <div class="js-search-results c-search__results"></div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

那些前缀类将成为我们的钩子。它们不是用来设计样式的，所以当你看到一些 JavaScript 代码时，意图就很明显了。

对于 JavaScript，我们将使用 lite 客户端，并移植一些代码:

```
import algoliasearch from 'algoliasearch/lite'

export const Search = {
  trigger: document.querySelectorAll('.js-search'),
  input: document.querySelector('.js-search-input'),
  resultsContainer: document.querySelector('.js-search-results'),
  index: null,

  init() {
      // bind to `this` so we reference this object rather than the input when it's called
    this.performSearch = this.performSearch.bind(this)

        // supply our application id and search-only api key
    let client = algoliasearch('APPLICATION_ID', 'SEARCH_ONLY_API_KEY')

        // connect to our index
    this.index = client.initIndex('INDEX_NAME')

        // perform a live search as the user types into the input field
    this.input.addEventListener('keyup', this.performSearch)
  },

  async performSearch(event) {},

  displayResults(results) {},

  emptyResultContainer() {},

    // we'll build up the HTML to inject into the container here
  getResultLink(result) {},

  displayNoResults() {}
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们在这里获取我们的`.js-`前缀元素，并用我们的凭证设置 Algolia 客户机，为搜索做准备。

当他们的`keyup`事件被触发时，它将调用`performSearch`方法。在这里对阿尔戈利亚进行查询:

```
async performSearch(event) {
    let query = event.target.value

    try {
      let content = await this.index.search({ query })

      if (content.hits && content.hits.length) {
        this.displayResults(content.hits)
      } else {
        this.displayNoResults()
      }
    } catch (e) {
      console.log('Error performing search: ', e)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里使用 async/await，但是你也可以使用 promises:

```
performSearch(event) {
    let query = event.target.value

    this.emptyResultContainer()

    this.index
      .search({ query })
      .then(content => {
        if (content.hits && content.hits.length) {
          this.displayResults(content.hits)
        } else {
          this.displayNoResults()
        }
      })
      .catch(e => {
        console.log('Error performing search: ', e)
      })
} 
```

Enter fullscreen mode Exit fullscreen mode

我们即将公布结果。首先，我们将概述流程是如何工作的。如果有结果，就显示出来，否则我们会让用户知道什么也没找到。在这之后，我们将看到如何构造搜索命中来注入结果容器:

```
displayResults(results) {
    results.forEach(result => {
      let resultLink = this.getResultLink(result)
      this.resultsContainer.appendChild(resultLink)
    })
},

emptyResultContainer() {
    while (this.resultsContainer.firstChild) {
     this.resultsContainer.removeChild(this.resultsContainer.firstChild)
    }
},

displayNoResults() {
    let title = document.createElement('h4')
    title.innerText = 'No results found'
    this.resultsContainer.appendChild(title)
} 
```

Enter fullscreen mode Exit fullscreen mode

在`displayResults`中，我们调用`getResultLink`，我们将使用它来追加结果容器:

```
getResultLink(result) {
    let link = document.createElement('a')
    let title = document.createElement('h4')

    link.setAttribute('href', result.url)
    title.innerText = result.title

    link.appendChild(title)

    return link
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，完整的片段如下:

```
import algoliasearch from 'algoliasearch/lite'

export const Search = {
  trigger: document.querySelectorAll('.js-search'),
  input: document.querySelector('.js-search-input'),
  resultsContainer: document.querySelector('.js-search-results'),
  index: null,

  init() {
    this.performSearch = this.performSearch.bind(this)

    let client = algoliasearch('APPLICATION_ID', 'SEARCH_ONLY_API_KEY')

    this.index = client.initIndex('posts')

    this.input.addEventListener('keyup', this.performSearch)
  },

  performSearch(event) {
    let query = event.target.value
    this.emptyResultContainer()

    this.index
      .search({ query })
      .then(content => {
        if (content.hits && content.hits.length) {
          this.displayResults(content.hits)
        } else {
          this.displayNoResults()
        }
      })
      .catch(e => {
        console.log('Error performing search: ', e)
      })
  },

  displayResults(results) {
    results.forEach(result => {
      let resultLink = this.getResultLink(result)
      this.resultsContainer.appendChild(resultLink)
    })
  },

  emptyResultContainer() {
    while (this.resultsContainer.firstChild) {
      this.resultsContainer.removeChild(this.resultsContainer.firstChild)
    }
  },

  getResultLink(result) {
    let link = document.createElement('a')
    let title = document.createElement('h4')

    link.setAttribute('href', result.url)
    title.innerText = result.title

    link.appendChild(title)

    return link
  },

  displayNoResults() {
    let title = document.createElement('h4')
    title.innerText = 'No results found'
    this.resultsContainer.appendChild(title)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，你就可以打电话`Search.init()`把这一切都踢开。

## 失物招领

你的高质量帖子不再需要被深埋，永远不会被看到。我们已经通过使用 lite 客户端来节省文件大小，但是您可以使用其他成熟的解决方案作为您选择的框架，以获得更开箱即用的体验。

这还没有考虑到我的[最新实现](https://github.com/tjFogarty/personal-site/blob/0e14c179461a5cce96102ceae4d9724265653b66/web/src/js/search.js)现在包含的一些可访问性更新。