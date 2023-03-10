# 网站 UI 测试

> 原文：<https://dev.to/funkysi1701/website-ui-testing-2a9n>

上周我看了测试移动应用程序的用户界面，本周让我们看看如何为网站做类似的事情。

测试用户界面不是缺少[单元测试](https://dev.to/funkysi1701/writing-your-first-test-53gi-temp-slug-2645725)的借口。测试用户界面需要更长的时间，所以继续创建你的小单元测试，这些测试可以在构建之后运行。也就是说，让我们看看如何创建一个 UI 测试。

像平常一样创建一个单元测试项目。现在安装下面的 nuget 包

```
Selenium.WebDriver.ChromeDriverSelenium.WebDriverSelenium.WebDriver.PhantomJS.Xplatform 
```

我将使用 Selenium 来完成我的网站测试，我将专注于 Chrome 浏览器。然而，其他浏览器也有包，所以看看下面，我希望还有其他的。

```
Selenium.WebDriver.IEDriverSelenium.Firefox.WebDriver 
```

Selenium 最初是 Firefox 的一个插件，用来帮助创建自动化测试，然而在我写这篇文章时，最新版本的 Firefox 与这个插件不兼容。我不需要在我的浏览器上安装任何插件或扩展来完成我的测试。

说够了，让我们写一个测试。首先，我们创建两个实例变量来存储 baseURL 和您正在使用的浏览器的驱动程序。

```
private string baseURL = "https://www.example.com/";private RemoteWebDriver driver; 
```

接下来，我们需要为测试运行做好准备。这将创建一个 chrome 驱动程序的实例，最大化窗口并设置它在超时前等待 30 秒。

```
[TestInitialize()]public void MyTestInitialize(){    driver = new ChromeDriver();    driver.Manage().Window.Maximize();    driver.Manage().Timeouts().ImplicitlyWait(TimeSpan.FromSeconds(30));} 
```

现在真正的考验来了。我们导航到一个 URL，然后将加载了一个已知值的页面标题与一个 Assert 语句进行比较，就像在单元测试中一样。

```
[TestMethod]public void CheckBrowserTitle(){    driver.Navigate().GoToUrl(this.baseURL);    Assert.AreEqual("Home Page", driver.Title);} 
```

最后，我们需要收拾自己。

```
[TestCleanup()]public void MyTestCleanup(){    driver.Quit();} 
```

如果你习惯于编写测试，你会知道测试通常由三部分组成:安排、动作和断言。排列是在 initialize 方法中完成的，这使得实际测试更加简单，第一行执行 Act，最后一行执行 Assert。

现在我们已经写了一个简单的测试，让我们看看更复杂的东西。

```
driver.FindElementByLinkTest("click") 
```

这将查找页面上被点击的任何链接。请注意，字符串需要与屏幕上显示的完全一致，通过 id 或类或不经常改变的东西来指定可能更容易。通过添加。单击该命令末尾的()，Selenium 将单击该链接，您可以导航到一个新页面。

**提交表单数据怎么办？**你可以找到你想要填充和添加的元素。SendKeys(“示例文本”)或。Submit()这将填写并提交表单数据。

**截图呢？**

```
Screenshot ss = driver.GetScreenshot();ss.SaveAsFile("test.jpg",System.Drawing.Imaging.ImageFormat.Jpeg); 
```

我只是刚刚开始玩 web UI 测试，但是你可以看到你可以做很多事情。

网站用户界面测试的帖子最先出现在 Funky Si 的科技谈话中。