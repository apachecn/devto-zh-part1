# Firefox 的天气标签

> 原文：<https://dev.to/peiche/weathertab-for-firefox-cnp>

还记得 [WeatherTab](https://dev.to/peiche/weathertab-a-new-tab-extension-for-chrome) 吗？这是我为 Chrome 创建的一个新的标签扩展，我刚刚将其移植到 Firefox。

根据 Mozilla 的文档，Firefox 几乎完全兼容 Chrome 和 Opera 支持的扩展 API(并且，通过扩展(双关语！)，我最喜欢的落水狗维瓦尔第)。他们有一个专门帮助开发者将 Chrome 扩展移植到 Firefox 的页面。他们不是在开玩笑。我几乎不需要做任何修改就可以让 WeatherTab 在 Firefox 中工作。

该扩展使用`geolocation`权限，在 JavaScript 中这意味着使用`navigator.geolocation`。我不需要做任何事情就能让它工作。

它还使用了`storage`权限，这意味着使用`chrome.storage`。尽管这似乎是 Chrome 特有的，但 Firefox 声明，在大多数情况下，它不需要任何开发人员的干预就能工作。在我的情况下，这被证明不是真的，但不是因为我认为的原因。

Firefox 确实支持`chrome.storage`的用法，但是不支持没有应用 ID 的`chrome.storage.sync`。将其改为`chrome.storage.local`允许扩展再次工作，但这意味着不同浏览器的温度设置不会同步。

我最终用`chrome.storage.local`推出了端口的第一个版本，然后一旦扩展上线，我就从开发者页面获取 UUID，用它更新清单，更新代码以使用`chrome.storage.sync`，并重新发布。现在，版本 1.4.3 已经可以使用工作同步临时设置了。(我改动了 Chrome 扩展的版本，以保持两者之间的平衡。)

查看闪亮的新扩展！
[判断我在 GitHub 上疯狂嵌套的雅虎 API 调用。](https://github.com/peiche/weathertab-firefox)

*这篇文章最初发表于[eichefam.net](https://eichefam.net/2017/11/17/weathertab-for-firefox/)。*