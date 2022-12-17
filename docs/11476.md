# IIS。woff MIME 类型

> 原文：<https://dev.to/adamkdean/iis-woff-mime-type-8gc>

IIS 没有为 woffs 设置 MIME 类型。我不喜欢这样，它会抛出这样的错误:

> 获取 http://localhost/fonts/glyphicons-halflings-regular . woff404(未找到)

要解决这个问题，需要为`.woff`添加一个 MIME 类型作为`application/x-font-woff`。

不要只是添加`application/x-woff`，否则 Chrome 会唠叨那些被解释为字体，但用 MIME 类型的应用程序/x-woff 传输的资源。