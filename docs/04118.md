# JavaScript 中的箭头函数

> 原文：<https://dev.to/tylermcginnis/arrow-functions-in-javascript-ec6>

箭头函数是构建现代 web 应用程序的新的基本构件。在这篇文章/视频中，你将了解到 Arrow 函数如何使你的代码更加简洁，同时也使“this”关键字更易于管理。您还将了解隐式返回、使用箭头函数进行日志记录，以及将隐式返回与对象相结合。

### 视频

### 岗位

与常规函数相比，箭头函数有两个主要优点。首先，它们更简洁。其次，它们使管理“this”关键字变得稍微容易了一些。

我在学习 Arrow 函数的新开发人员身上看到的是，很难理解的并不是这个概念本身。很可能你已经熟悉了函数、它们的好处、用例等等。然而，出于某种原因，当你第一次接触它们时，是实际的语法让你的大脑陷入了一个循环。正因为如此，我们将慢慢来，首先介绍语法如何与你熟悉的典型函数进行比较。

这里我们有一个非常基本的函数声明和一个函数表达式。

```
// fn declaration
function add (x,y) {
  return x + y;
}

// fn expression
var add = function (x,y) {
  return x + y;
} 
```

现在，如果我们想把这个函数表达式变成一个箭头函数，我们可以这样做。

```
var add = function (x,y) {
  return x + y;
}

var add = (x,y) => {
  return x + y;
} 
```

同样，开始使用箭头函数最困难的部分是习惯语法。一旦你冷静下来，继续前进，我们会潜得更深。

在这一点上，你可能想知道所有关于箭头函数的炒作是什么。事实上，上面的例子并没有很好地发挥他们的优势。我发现，当你使用匿名函数时，箭头函数真的很有用。我们可以通过查看另一个使用`.map`的基本例子来让我们的大脑对语法有更多的了解。

```
users.map(function () {

})

users.map(() => {

}) 
```

好了，热身够了。让我们深入研究一下。

假设我们有一个`getTweets`函数，它接收一个用户 id，在遇到一个设计糟糕的 API 后，返回给我们所有超过 50 颗星的用户推文和转发。使用承诺链，该函数可能如下所示:

```
function getTweets (uid) {
  return fetch('https://api.users.com/' + uid)
    .then(function (response) {
      return response.json()
    })
    .then(function (response) {
      return response.data
    }).then(function (tweets) {
      return tweets.filter(function (tweet) {
        return tweet.stars > 50
      })
    }).then(function (tweets) {
      return tweets.filter(function (tweet) {
        return tweet.rts > 50
      })
    })
} 
```

嗯，这很有效。但这不是世界上最漂亮的功能，ðÿ。尽管这种特定的实现有点复杂，但这种想法太普遍了。让我们看看到目前为止我们所知道的箭头函数是如何改进我们的`getTweets`函数的。

```
function getTweets (uid) {
  return fetch('https://api.users.com/' + uid)
    .then((response) => {
      return response.json()
    })
    .then((response) => {
      return response.data
    }).then((tweets) => {
      return tweets.filter((tweet) => {
        return tweet.stars > 50
      })
    }).then((tweets) => {
      return tweets.filter((tweet) => {
        return tweet.rts > 50
      })
    })
} 
```

好吧，酷。它看起来基本上是一样的，我们只是不需要输入`function`。有益，但没什么值得发微博的。让我们看看箭头函数的下一个好处，“隐式返回”。

对于箭头函数，如果你的函数有一个“简洁的体”(一种说单行函数的奇特方式)，那么你可以省略“return”关键字，值将被自动(或隐式)返回。

所以前面的`add`例子可以更新成这样，

```
var add = function (x,y) {
  return x + y;
}

var add = (x,y) => x + y; 
```

更重要的是，`getTweets`示例可以更新成这样，

```
function getTweets (uid) {
  return fetch('https://api.users.com/' + uid)
    .then((response) => response.json())
    .then((response) => response.data)
    .then((tweets) => tweets.filter((tweet) => tweet.stars > 50))
    .then((tweets) => tweets.filter((tweet) => tweet.rts > 50))
} 
```

现在我们谈论的是ðÿ"ˆ.这些代码不仅更容易编写，更重要的是，更容易阅读。

现在，我们可以做的另一个改变是，如果 arrow 函数只有一个参数，您可以省略它周围的`()`。考虑到这一点，`getTweets`现在看起来像这样，

```
function getTweets (uid) {
  return fetch('https://api.users.com/' + uid)
    .then(response => response.json())
    .then(response => response.data)
    .then(tweets => tweets.filter(tweet => tweet.stars > 50))
    .then(tweets => tweets.filter(tweet => tweet.rts > 50))
} 
```

总的来说，我认为这几乎在每个类别中都是一个巨大的进步。

箭头函数的下一个好处是它们如何管理“this”关键字。如果你不熟悉“这个”关键词，我推荐看 [WTF 就是这个](https://tylermcginnis.com/videos/wtf-is-this/)。

让我们看看一些使用 ES5 的典型 React 代码。

```
class Popular extends React.Component {
  constructor(props) {
    super();
    this.state = {
      repos: null,
    };

    this.updateLanguage = this.updateLanguage.bind(this);
  }
  componentDidMount () {
    this.updateLanguage('javascript')
  }
  updateLanguage(lang) {
    api.fetchPopularRepos(lang)
      .then(function (repos) {
        this.setState(function () {
          return {
            repos: repos
          }
        });
      });
  }
  render() {
    // Stuff
  }
} 
```

当组件挂载时，它发出一个 API 请求(到 Github API)来获取 JavaScript 最流行的库。当它获得存储库时，它获取它们并更新组件的本地状态，或者至少这是我们希望它做的。不幸的是，它没有做到这一点。相反，我们得到一个错误。你能发现这个漏洞吗？

上面的代码将抛出的错误是“无法读取未定义的 setState”。现在，**为什么**会发生这种情况已经超出了这篇文章的范围(再次提醒，如果你需要的话，看看 [WTF 是这个](https://tylermcginnis.com/videos/wtf-is-this/))但是一个典型的 ES5 解决方案使用`.bind`，看起来像这样

```
class Popular extends React.Component {
  constructor(props) {
    super();
    this.state = {
      repos: null,
    };

    this.updateLanguage = this.updateLanguage.bind(this);
  }
  componentDidMount () {
    this.updateLanguage('javascript')
  }
  updateLanguage(lang) {
    api.fetchPopularRepos(lang)
      .then(function (repos) {
        this.setState(function () {
          return {
            repos: repos
          }
        });
      }.bind(this));
  }
  render() {
    // Stuff
  }
} 
```

这是第二个主要的好处，也是为什么箭头函数如此伟大的原因，它们不会创建自己的上下文。这意味着通常情况下,`this`关键字是有效的——你不必担心特定的函数将在什么上下文中被调用。因此，通过在`updateLanguage`方法中使用箭头函数，我们不必担心`this`，这意味着我们不必再调用`.bind`。

```
updateLanguage(lang) {
  api.fetchPopularRepos(lang)
    .then((repos) => {
      this.setState(() => {
        return {
          repos: repos
        }
      });
    });
} 
```

ðÿ"ˆðÿ"ˆðÿ"ˆ

#### 很高兴知道

至此，我们已经涵盖了所有关于箭头函数的“需要知道的事情”。然而，我认为有两个不同的“值得一提”值得一提。

再看一下`updateLanguage`方法，如果我们想隐式返回 setState 回调中的对象，我们该如何做呢？您的第一直觉会是删除 return 语句，只返回一个对象。

```
api.fetchPopularRepos(lang)
  .then((repos) => {
    this.setState(() => {
      repos: repos
    });
  }); 
```

正如您可能猜到的那样，这样做的问题是语法与创建函数体完全相同。JavaScript 无法神奇地区分何时想要创建函数体和何时想要返回一个对象，所以它会抛出一个错误。为了解决这个问题，我们可以将对象包装在`()`中。

```
api.fetchPopularRepos(lang)
  .then((repos) => {
    this.setState(() => ({
      repos: repos
    }));
  }); 
```

现在，有了这个语法，我们可以使用一个箭头函数来隐式地返回一个对象。

下一个提示。假设我们想通过记录来检查 setState 中组件的先前状态。如果这是您的 setState 函数，您将如何处理日志记录`nextState`？

```
this.setState((nextState) => ({
  repos: repos
})); 
```

显而易见的做法是将隐式返回改为显式返回，创建一个函数体，然后在函数体内进行日志记录。

```
this.setState((nextState) => {
  console.log(nextState)
  return {
    repos: repos
  }
}); 
```

嗯，那很烦人。不过有一种更好的方法，它是使用`||`操作符完成的。您可以这样做，而不是搞乱所有的代码

```
this.setState((nextState) => console.log(nextState) || ({
  repos: repos
})); 
```

如此聪明的ðÿ'“ðÿž

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们在 T2 现代 JavaScript 课程的一部分。