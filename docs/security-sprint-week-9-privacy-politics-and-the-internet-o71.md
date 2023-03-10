# 安全冲刺周 9:隐私政治和互联网

> 原文：<https://dev.to/terceranexus6/security-sprint-week-9-privacy-politics-and-the-internet-o71>

一个偶然的机会，我发现了一个旧的万维网联盟(W3C) [项目](https://www.w3.org/TR/P3P11/)，它已经被废弃了一段时间。隐私偏好项目平台( **P3P** )应该是一个协议，该协议提供了在特定页面中声明收集的网络浏览器用户信息的预期用途的机会，以便制定保护用户隐私的标准化方法。起初，我认为这是一个很棒的主意，这让我想起了很多 cookies 政策的问题。这应该是政策声明语法的一个例子(摘自罗莉·费思·克兰诺的《P3P 的网络隐私》:

```
<STATEMENT>
  <EXTENSION optional="no">
    <SAFE-HARBOR eu-applicable="yes"
     />
  </EXTENSION>
  <PURPOSE><current/></PURPOSE>
  <RECIPIENT><ours/></RECIPIENT>
  <RETENTION><stated-purpose/></RETENTION>
  <DATA-GROUP>
    <DATA ref="#user.home-info.postal"/>
  </DATA-GROUP>
</STATEMENT>
<STATEMENT>
  <EXTENSION optional="no">
    <SAFE-HARBOR eu-applicable="no"
      />
  </EXTENSION>
  <PURPOSE><current/><profiling/></PURPOSE>
  <RECIPIENT><ours/><other-recipient/></RECIPIENT>
  <RETENTION><indefinitely/></RETENTION>
  <DATA-GROUP>
    <DATA ref="#user.home-info.postal"/>
  </DATA-GROUP>
</STATEMENT> 
```

Enter fullscreen mode Exit fullscreen mode

随着我阅读和发现越来越多的文档，以及这个项目停止的主要原因，我发现了围绕这个项目的一堆优点和缺点以及类似的问题。

**优点？**

用户隐私，一种保护信息并使网上冲浪更加安全的标准化方法。这种协议可以阻止任何用户可能不希望在他的计算机上的 cookies！像 Privacy Badger 这样的工具可以通过这种方式直接实现，而不需要附加组件。

**缺点？**

显然，这个项目停止的主要原因之一是因为它对普通用户来说似乎“太复杂了”。我觉得这太奇怪了，以至于皱起了我的鼻子:在没有经验的人看来，计算机开发上的许多事情不都很“复杂”吗？另一个问题是，它的使用不是强制性的，因此需要阻止的实际页面不会使用它。对我来说，这似乎更合理。

幸运的是，我们有几个用户友好的插件和程序来保护自己，但尽管我认为这样的想法最终还是夭折了，这很可悲。在过去的几年里提出了一些其他类似的项目，但似乎没有一个成功，或者至少我还没有发现它们。

你们对这类项目有什么看法？你认为使用新协议在互联网上创造隐私意识值得一试吗？还是你认为其他方法更有用？