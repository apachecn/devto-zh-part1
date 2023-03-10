# 音乐标记-网络音频的网络组件

> 原文：<https://dev.to/tanepiper/music-markup---web-components-for-web-audio-10l5>

今天，我将发布[音乐标记](https://github.com/tanepiper/mm-components),作为一套创建音频体验的普通网络组件。

最先发布的两个组件是`<mm-keyboard>`和`<mm-key>`。我用它创建了一个键盘组件:

[![A keyboard created with mm-components](img/898a8a9c4e523b86c7d1bce194cf9071.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EsNUYk9_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n0no4bpimqinv0ha12mu.png)

代码非常具有声明性:

```
 <mm-keyboard oscillator-type="sawtooth">
      <mm-key class-name="white b" frequency="174.614" time="1"></mm-key>
      <mm-key class-name="black as" frequency="184.997" time="1"></mm-key>
      <mm-key class-name="white a" frequency="195.998" time="1"></mm-key>
      <mm-key class-name="black gs" frequency="200.000" time="1"></mm-key>
      <mm-key class-name="white g" frequency="207.652" time="1"></mm-key>
      <mm-key class-name="black fs" frequency="233.082" time="1"></mm-key>
      <mm-key class-name="white f" frequency="246.942" time="1"></mm-key>
      <mm-key class-name="white e" frequency="261.626" time="1"></mm-key> <!-- Middle c -->
      <mm-key class-name="black ds" frequency="277.183" time="1"></mm-key>
      <mm-key class-name="white d" frequency="293.665" time="1"></mm-key>
      <mm-key class-name="black cs" frequency="311.127" time="1"></mm-key>
      <mm-key class-name="white c" frequency="329.628" time="1"></mm-key>
  </mm-keyboard> 
```

Enter fullscreen mode Exit fullscreen mode

显然，你可以很容易地用代码生成它，但是我还是为版本 1 创建了一个硬编码的键盘。它基于我为一个工作黑客日编写的一个名为 [Browser Band](https://github.com/tanepiper/browser-band) 的早期应用程序，我打算使用那里的一些现有代码。

我希望添加的主要功能有:

*   添加对 MP3 音频/ WAV 文件的支持
*   提供主题支持(设计不同的皮肤)
*   为控件添加可视化工具
*   支持提供节点传递的嵌套标记

任何想法或意见将不胜感激。