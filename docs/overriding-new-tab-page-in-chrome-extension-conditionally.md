# 有条件地覆盖 Chrome 扩展中的新标签页！

> 原文：<https://dev.to/chinchang/overriding-new-tab-page-in-chrome-extension-conditionally>

如果你使用 Chrome 扩展，如 Momentum、Panda 等，你知道 Chrome 扩展有能力覆盖你的新标签页，即当你在浏览器中打开一个新标签时看到的页面。他们通过 [*覆盖页面*](https://developer.chrome.com/extensions/override) API，通过在清单文件:
中这样做

```
{  "name":  "My extension",  ...  "chrome_url_overrides"  :  {  "newtab":  "theNewPage.html"  },  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

这种扩展的问题是，您只能使用一个这样的扩展，因为如果您有多个扩展，每个扩展都试图覆盖新的选项卡页面，那么只有其中一个最终可以覆盖。此外，这些扩展没有提供任何可配置的设置来使新选项卡的覆盖可选。但是，我在 [Web Maker](https://webmakerapp.com) 中使用了一个非常简单的技巧来创建新的标签覆盖条件。

首先，如上所述，您不需要在扩展清单中做任何事情。然后，您可以拥有一个监听新选项卡创建事件的背景页面。每当一个新标签被创建，并且新标签的 URL 是`chrome://newtab/`，我们可以做我们的条件检查并相应地替换 URL。你可以这样做:

```
chrome.tabs.onCreated.addListener(function(tab) {
    if (tab.url === 'chrome://newtab/') {
        if (shouldReplaceNewTabSetting === true) {
            chrome.tabs.update(
            tab.id,
            {
                url: chrome.extension.getURL('theNewPage.html')
            }
        );
        }

    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就对了——有条件的新标签页替换！你也可以在这里看到我在 Web Maker 中使用的实际代码。