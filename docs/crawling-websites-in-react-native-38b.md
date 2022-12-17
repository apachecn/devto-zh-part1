# 在 React-Native 中抓取网站

> 原文：<https://dev.to/kayis/crawling-websites-in-react-native-38b>

从多年的网络开发经验来看，React-Native 对我来说是一个全新的开始。您可以更好地访问本机功能，并且对您的应用程序施加的规则更少。例如，你可以使用`fetch()` toy 获得任何你想要的网站。这实现了客户端站点 web 爬行。

## 为什么

也许你需要来自服务的数据，但是他们没有公开 API，或者 API 没有给你你需要的所有数据，或者 API 很糟糕。通常情况下，您必须设置一个服务器来抓取目标网站，并将其转换为您可以使用的 API，但是当您可以访问客户端内所有网站的所有数据时，您可以节省时间。

让我们以亚马逊网站为例。您希望显示一个页面的所有产品和加载下一个页面的方式，但是您希望它在我们自己的数据结构中，这样您就可以围绕它构建自己的 UI。

## 如何

1.  从服务器获取 HTML
2.  从 HTML 中提取所需的数据
3.  重塑数据以供我们使用

### 1 从服务器获取 HTML

这是容易的部分。

```
async function loadGraphicCards(page = 1) {
  const searchUrl = `https://www.amazon.de/s/?page=${page}&keywords=graphic+card`;
  const response = await fetch(searchUrl);   // fetch page

  const htmlString = await response.text();  // get response text
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

使用搜索模式获取 URL 会返回一个包含一些项目的 HTML 页面。

### 2 从 HTML 中提取需要的数据

这有点棘手。数据在 HTML 中，但它是一个字符串。

最简单的方法是使用正则表达式来解析字符串并获取数据，但是 HTML 没有正则语法，所以这是行不通的。

更好的方法是使用 HTML 解析器和 CSS 选择器。

[Cheerio](https://github.com/cheeriojs/cheerio) 就是这个解决方案。它附带了一个 HTML 解析器和 jQuerys 核心功能的重新实现，因此您可以在 Node.js 上使用它。

问题是，React-Native 缺少大多数 Node.js 包，所以它不工作。

我花了很长时间寻找 Cheerio 在 React-Native 上的重新实现，这个包的名字有点奇怪，哈哈。

但是有了这个，数据的提取现在也是小孩子的游戏了。

```
async function loadGraphicCards(page = 1) {
  const searchUrl = `https://www.amazon.de/s/?page=${page}&keywords=graphic+card`;
  const response = await fetch(searchUrl);      // fetch page 

  const htmlString = await response.text();     // get response text
  const $ = cheerio.load(htmlString);           // parse HTML string

  const liList = $("#s-results-list-atf > li"); // select result <li>s
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### 3 重塑数据以备后用

从 HTML 中提取数据后，我们可以开始为我们的用例重塑数据。提取和整形在这里有点模糊，我们选择的`<li>`充满了标记，从中获取正确的数据也是提取，但这两个步骤通常是携手并进的。

```
async function loadGraphicCards(page = 1) {
  const searchUrl = `https://www.amazon.de/s/?page=${page}&keywords=graphic+card`;
  const response = await fetch(searchUrl);  // fetch page 

  const htmlString = await response.text(); // get response text
  const $ = cheerio.load(htmlString);       // parse HTML string

  return $("#s-results-list-atf > li")             // select result <li>s
    .map((_, li) => ({                      // map to an list of objects
      asin: $(li).data("asin"),                   
      title: $("h2", li).text(),                
      price: $("span.a-color-price", li).text(),
      rating: $("span.a-icon-alt", li).text(),
      imageUrl: $("img.s-access-image").attr("src")
    }));
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个强有力的例子，但是我想你已经明白了。现在，我们可以在应用程序中使用新的对象列表来为 Amazon 结果创建我们自己的 UI。

```
 class App extends ReactComponent {
  state = {
    page: 0,
    items: [],
  };

  componentDidMount = () => this.loadNextPage();

  loadNextPage = () =>
    this.setState(async state => {
      const page = state.page + 1;
      const items = await loadGraphicCards(page);
      return {items, page};
    });

  render = () => (
    <ScrollView>
      {this.state.items.map(item => <Item {...item} key={item.asin}/>)}
    </ScrollView>
  );
}

const Item = props => (
  <TouchableOpacity onPress={() => alert("ASIN:" + props.asin)}>
    <Text>{props.title}</Text>
    <Image source={{uri: props.imageUrl}}/>
    <Text>{props.price}</Text>
    <Text>{props.rating}</Text>
  </TouchableOpacity> ); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

和大多数问题一样，如果你有合适的工具，解决方案会变得简单。通常问题更多的是找到这些工具:D

这种客户端爬行方法可以用来构建快速原型，而不需要 API。亚马逊很好地提供了不错的静态 HTML，所以它在他们的网站上工作得相当好。