# 让 Flash 在 Windows 10 上的 NWJS 0.25+上工作

> 原文：<https://dev.to/jtlunsford/getting-flash-to-work-on-nwjs-025-on-windows-10-6ok>

# [T1】简介](#intro)

您可能会认为 Node Webkit(或现在所称的 NWJS)支持开箱即用或简单直接的 flash，但事实并非如此。

你可以从[谷歌小组讨论](https://groups.google.com/forum/#!topic/nwjs-general/j7eCY7YJVcQ)中找到一些需要的信息。更多信息包含在 [github 问题](https://github.com/nwjs/nw.js/issues/5891)中

以下是我用来解决这个问题的步骤。

### 下载 Flash

*   用 PPAPI 选项安装`https://get.adobe.com/flashplayer`
*   导航至`C:\Windows\System32\Macromed\Flash`
*   在你的`nw`文件夹中复制`C:\Windows\System32\Macromed\Flash`到`.\PepperFlash\`

### 修改 NWJS 申请

*   在 NWJS 应用程序中添加以下代码

```
chrome.contentSettings.plugins.set({
    primaryPattern: "<all_urls>",
    resourceIdentifier: { id: "adobe-flash-player" },
    setting: "allow"
}); 
```

Enter fullscreen mode Exit fullscreen mode