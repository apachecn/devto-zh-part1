# WebdriverIO 如何让测试变得值得

> 原文：<https://dev.to/klamping/how-webdriverio-makes-testing-worth-it>

*原贴于[blog.kevinlamping.com](https://blog.kevinlamping.com)T3】*

自动化 UI 测试并没有得到很多人的喜爱。

当然，它们很受欢迎，可以用来做演示和写博客，但是根据我的经验，没有多少公司有可靠的 UI 测试套件。

让我们将 UI 测试定义为任何控制类似浏览器界面的自动化测试。

关键是测试使用的是传统意义上的网站。我们打开一个页面，单击一些元素，并验证一些功能。

那么为什么缺乏使用呢？似乎这些类型的测试会非常有用。

当你能让它们工作时，它们就是。

我遇到了四个常见的问题:

1.  大多数工具使用 Java，这对于测试来说太冗长了
2.  用 NodeJS 编写的工具通常局限于 PhantomJS，阻止你在真正的浏览器中进行测试。
3.  为 Selenium 编写命令既复杂又容易出错
4.  工具工作得很好，直到你超越它们的极限

谢天谢地，WebdriverIO 有所有这些疾病的解药。

## WebdriverIO 是“前端友好”

与许多 Selenium 工具不同，WebdriverIO 完全是用 JavaScript 编写的。甚至 Selenium 的安装也是通过 NPM 模块完成的。

我一直认为浏览器自动化意味着弄清楚如何运行一些复杂的 Java 应用程序，这听起来对我没有吸引力。还有 Selenium IDE，但是通过页面记录编写测试让我想起了太多像微软 FrontPage 这样的 WYSIWYG web 编辑器。

相反，WebdriverIO 让我用我熟悉的语言编写，并集成了我用于单元测试的相同测试工具(Mocha 和 Chai)。

从用 JavaScript 编写实际功能转换到用 JavaScript 编写测试代码的精神负担很小，我喜欢这个因素。

另一件很棒的事情是，我可以使用高级 CSS 选择器来查找元素，这比 WebdriverIO 更值得称赞。

xPath 无缘无故地让我害怕。一些关于斜线而不是空格的东西让我不寒而栗。

但是我不用学 xPath。

使用 WebdriverIO，我只需传入我熟悉的 CSS 选择器，它就知道我在说什么。

我认为前端开发人员应该为他们的代码编写测试(包括单元测试和功能测试)。WebdriverIO 让这变得非常容易。

## 它具有硒的力量

当我用 PhantomJS 编写测试时，我总是感觉受到了阻碍，因为我知道它永远无法在 IE 等流行但有缺陷的浏览器中验证功能。

但是因为 WebdriverIO 使用了 Selenium，所以我可以在各种浏览器中运行我的测试。

[![](img/6bdf9dca80bac4006735a83323b91959.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hAKYQ2FT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.kevinlamping.com/conteimg/2016/08/image.jpeg)

Selenium 是一个非常健壮的平台，是运行浏览器自动化的行业领导者。WebdriverIO 是骑在 Selenium 的上面，站在巨人的肩膀上。没有编写基于 Java 的测试的开销，Selenium 的所有优点都是可用的。

## 它力求简洁

您在 WebdriverIO 测试中使用的命令是简明的和常识性的。

我的意思是，WebdriverIO 不会让你编写代码来将两个显然是相互关联的部分连接在一起。

例如，如果我想通过普通的 Selenium 脚本点击一个按钮，我必须使用两个命令。一个用来获取元素，另一个用来单击元素。

为什么？很明显，如果我想点击某个东西，我需要识别它。

WebdriverIO 通过接受命令中的元素选择器简化了“click”命令，然后将其转换为所需的两个 Selenium 操作。这意味着不用写这个:

```
driver.findElement(webdriver.By.id('btnG')).click(); 
```

Enter fullscreen mode Exit fullscreen mode

我可以这样写:

```
browser.click('#btnG') 
```

Enter fullscreen mode Exit fullscreen mode

在编写测试时，这只是少了很多令人麻木的重复...

说到简单，我喜欢 WebdriverIO 集成到 Selenium 的方式。他们没有创建自己的 Selenium 集成，而是使用 Selenium 2.0 提供的公共 REST API，并对其进行普通的请求调用。

这里有一个来自`elementActive`协议的核心 WebdriverIO 代码的例子:

```
let elementActive = function () {
    return this.requestHandler.create({
        path: '/session/:sessionId/element/active',
        method: 'POST'
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。这基本上是文件/函数的全部。

理解这里发生的事情非常简单。我们向“元素/活动”端点发送 POST 请求并返回响应。

我还在学习 Node.js，所以看到我对正在发生的事情有所了解的软件是令人耳目一新的。

这个库的大部分是由这些小命令组成的，这些小命令位于它们自己单独的小文件中。这意味着更新更容易，集成到像 Sauce Labs 或 BrowserStack 这样的云 Selenium 服务非常简单。

有太多的工具试图重新发明轮子，只是为了它。我很高兴 WebdriverIO 让它变得简单，反过来帮助我轻松理解幕后发生的事情。

## 很容易扩展/升级

作为一个在大型组织中工作了很长时间的人，我所使用的工具易于扩展对我来说很重要。

为了减轻我所支持的开发人员的负担，我将有定制需求，并想编写自己的抽象。

WebdriverIO 在两个方面做得很好:

### 自定义命令

默认情况下，WebdriverIO 提供了大量的命令，但是很多时候，您希望为您的应用程序编写一个自定义命令。

WebdriverIO 让这变得非常简单。

只需调用“addCommand”函数，并传入您的自定义步骤。

下面是他们文档中的一个例子:

```
browser.addCommand("getUrlAndTitle", function (customVar) {
    return {
        url: this.getUrl(),
        title: this.getTitle(),
        customVar: customVar
    };
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当我需要测试的 URL 和标题时，我都可以使用一个命令来获取数据。

### 页面对象

随着 web driverio 4 . x 版本的发布，他们引入了一种编写页面对象的新模式。对于那些不熟悉这个术语的人来说，页面对象是表示与页面或组件交互的一种方式。

您可以编写一个页面对象来引用该组件，而不是在整个测试套件中为一个公共页面元素重复使用同一个选择器。

然后，在您的测试中，您只需要向页面对象询问您需要什么，它就会为您处理它。

这意味着测试更易于维护和阅读。

它们更易于维护，因为更新选择器和动作发生在一个文件中。

当登录页面的一个简单的 HTML 更改破坏了一半的测试时，您不必在代码中找到每个对`input#username`的引用。您只需更新登录页面对象，就可以再次开始了。

它们更容易阅读，因为测试变得更少关注页面的具体实现，而更多关注页面做什么。

例如，假设我们需要登录我们的网站进行大部分测试。如果没有页面对象，我们所有的测试都将从:
开始

```
browser.url("login-page")
browser.setValue("#username", "testuser")
browser.setValue("#password", "hunter2")
browser.click("#login-btn") 
```

Enter fullscreen mode Exit fullscreen mode

对于页面对象，这可以变得简单到:

```
LoginPage.open();
LoginPage.login('testuser', 'hunter2'); 
```

Enter fullscreen mode Exit fullscreen mode

没有引用特定的选择器。不知道网址。只是自文档化的步骤，读出来更像指令而不是代码。

现在，页面对象并不是 WebdriverIO 引入的新想法。但是他们设置使用普通 JavaScript 对象的方式是明智的。不需要理解外部库或自定义领域语言。它只是 JavaScript 和一点原型继承。

## 总结一下

我不认为自己是一个测试者。

我太笨手笨脚了，不能负责确保无错误的发布。

然而，我情不自禁地爱上了 WebdriverIO 为我提供的东西，我真的是他们所做的事情的粉丝，希望这个项目继续发展壮大。

如果你是前端开发人员，或者只是熟悉 JavaScript，看看 [WebdriverIO](http://webdriver.io/) ，看看你是否和我一样觉得它很棒。