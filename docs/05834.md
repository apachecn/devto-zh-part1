# 在 React 中将非受控输入变为受控误差

> 原文：<https://dev.to/monknomo/changing-an-uncontrolled-input-to-controlled-error-in-react>

React 希望表单输入等保持它们在 React 内部的状态，在输入内部。这种风格被称为“受控输入”。它们还提供“不受控制的输入”,其中输入管理它自己的状态。

React 显然不喜欢输入从受控切换到不受控。我正在开发一个表单，文档在我的编辑器旁边打开，我一直得到一个`Warning: CustomInput is changing an uncontrolled input of type text to be controlled. Input elements should not switch from uncontrolled to controlled (or vice versa). Decide between using a controlled or uncontrolled input element for the lifetime of the component. More info:`[https://fb.me/react-controlled-components](https://fb.me/react-controlled-components)

据我所知，我的输入被设置为总是受控的。在实现之前，我修改了 javascript 并在谷歌上搜索了很长时间

## 我正在将一个表单值从`''`切换到`null`

这显然是 React 的受控组件领域的禁忌。我的初始状态设置都是空字符串，唯一的后续状态操作是从 webservices 获取数据。我正在使用的 web 服务从数据库中返回一个相当忠实的数据表示——空值等等。

我通过在 render 方法中将我的状态变量转换为`const`解决了这个问题——有点奇怪:

```
const nullable = this.state.nullable == null ? '' : this.state.nullable; 
```

Enter fullscreen mode Exit fullscreen mode

最后一分钟的转换器，一切都很好。

[每月收到一封电子邮件，其中包含来自网络的优秀技术和技术领导文章](http://www.gunnargissel.com/pages/email-signup-1.html)

*谢谢[塞巴斯蒂安·多里斯](https://www.flickr.com/photos/sebastiandooris/)的[洒](https://flic.kr/p/61ZqoU)T5】*

如果你喜欢这个，[访问我的博客了解更多信息](http://www.gunnargissel.com)