# 在 Magento 中解开谜团

> 原文：<https://dev.to/cwardzala/solving-mysteries-in-magento-3hmg>

不久前，我们的 Magento 电子商务网站开始记录一个奇怪而神秘的异常。

```
PHP Fatal error: Uncaught exception 'Exception' with message 'Serialization of 'Mage_Core_Model_Con
fig_Element' is not allowed' in [no active file]:0\nStack trace:\n#0 {main}\n thrown in [no active file] on line 0 
```

Enter fullscreen mode Exit fullscreen mode

不知道这意味着什么，我求助于谷歌。几乎没有结果有助于找出问题所在。因为它似乎从来没有影响到我们的用户，我把它推到一边，改天再解决。

在异常开始的时候，我注意到警告消息从未被清除。消息会一直存在，直到用户注销并清除他们的会话。

几个月后，我找到时间深入研究消息问题。我开始研究 Magento 内置消息类的扩展。一旦我排除了这个问题的根源，我就开始采取更积极的措施来寻找问题。我打开了所有我能找到的调试选项。随着调试所有的事情模式打开，我看到我们的旧的神秘信息再次弹出。这让我再次深究这个问题。Google 没有提供太多的解决方案，但是它给我指明了会话存储的方向。我开始四处打探，寻找任何我们可以投入使用的地方。一旦我排除了`string`和`int`类型的问题，我开始关注其他数据类型。找到了。我发现一个函数试图存储一个`Findaway_MultiCart_Model_Quote`类的实例。注释掉代码并重新加载证明了这是源代码。

故事的寓意，测试并寻找与新代码相关的异常。令人惊讶的是，这是如此容易修复，因为它是如此难以追查。如果 PHP 公开一个更有用的消息，或者 Magento 抛出一个异常，这个过程可能会更快。