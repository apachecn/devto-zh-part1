# 利用 bro 进行网络安全监控

> 原文：<https://dev.to/terceranexus6/network-security-monitoring-with-bro>

最近我一直在研究网络安全。一年来，我一直在使用 ettercap 进行测试和调查，我甚至为它编写了一个 [Lex app](https://github.com/terceranexus6/proyecto_lex) ，但最近我发现了一个非常相似但更好的工具，日志非常有组织，命令也更好。我必须说我将使用 linux，在 Windows 中有网络监控的选项，但是我不打算在这篇文章中谈论它们。

首先，网络安全监控允许我们检测和响应入侵。NSM 数据的范围是:

*   **全部内容**:所有穿越网络的信息
*   **提取内容**:高级数据流，如图像等。
*   **会话数据**:两个节点之间的对话记录(这是 bro 生成的日志，也是我将要关注的部分)。
*   **事务数据**:类似于会话数据，但侧重于理解请求和回复。
*   **统计数据**:一个活动各个方面产生的流量。
*   **元数据**:进一步研究监测的数据结果并理解。
*   **报警数据**:入侵检测。

现在我要介绍兄弟工具。兄弟，这很容易安装。在终端上下载并打开目录后，我们可以简单的做`cat INSTALL`就可以阅读安装说明，但是是经典的`./configure`、`cmake`、`make`。如果你有问题，你应该检查你的网络是否是 wlan0，如果不是，就换一个。我花了一段时间才明白。

一旦我们安装了它，我们去我们保存它的目录。在我的例子中，它是/usr/local/bro，但在任何地方。在……里.../bro/bin 您应该会看到很多可执行文件。我们会把注意力放在布罗克特身上，苏多那东西！`sudo ./broctl`。

您应该会看到类似这样的内容:

```
Welcome to BroControl 1.7

Type "help" for help.

[BroControl] > 
```

Enter fullscreen mode Exit fullscreen mode

现在我们开始了。在 bro 终端上，我们将编写`start`开始扫描。

```
[BroControl] > start
starting bro ...
[BroControl] > 
```

Enter fullscreen mode Exit fullscreen mode

在另一个终端中，我们再次找到我们的 bro 目录，然后我们转到.../bro/logs。我们应该看到一个日志，上面有日期和一个名为*当前*的目录。我们将检查*电流* ( `cd current`)。在那个目录中我们应该会找到很多目录，但是我们将把中心放在`http.log`中。目录是压缩的，所以首先我们使用`sudo gunzip *.log.gz`来解压它们。现在让`cat http.log`。

在那里我们可以看到许多信息被分成段落。我们应该清楚地看到网络中的 GET 请求。例如，如果我们在浏览器中输入[www.testmyids.com](//www.testmyids.com)，并再次执行`cat http.log`操作，我们应该可以清楚地看到对 favicon 的请求失败了。一个很好的技巧是监视 x509.log 目录。它包含了访问过的地方的证书信息，告诉我们用户正在进入 twitter，facebook，google...等等。

当我们想要停止时，我们只需在 bro 终端
中写入“stop”

```
[BroControl] > stop
stopping bro ... 
```

Enter fullscreen mode Exit fullscreen mode

bro 还有更多[命令](https://www.bro.org/sphinx/components/broctl/README.html)可供我们检查。你可以花一些时间分析所有的数据，我向你保证，你可以很容易地得到结论，因为它是一个组织良好的系统。