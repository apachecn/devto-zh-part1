# 我们是人吗？或者我们是在重温旧梦？

> 原文：<https://dev.to/swyx/are-we-human-or-are-we-recaptcha-blm>

# “你知道又讨厌的 2 个歪歪扭扭的字 captcha，到 2018 年 3 月 31 日就死了。”

网络是黑暗的，充满了机器人，有一个无可争议的领导者来抵御它们。你可能每天都在使用 reCAPTCHA，但你却不知道！Aaron Malenfant 是 [reCAPTCHA](https://www.google.com/recaptcha) 的首席软件工程师，他在纽约 GDG 开发者大会上解释了它的过去、现在和未来。reCAPTCHA 本质上是保密的，所以很少有人深入了解这种重要的网络技术是如何工作的。

# 第 1 部分:高层细节

## 我学到了什么

你可以在[https://www.google.com/recaptcha](https://www.google.com/recaptcha)注册 reCAPTCHA，并在这里通过 CodeLab [了解更多信息。](http://g.co/recaptcha/codelab)

### 卷

ReCAPTCHA

*   200 万周活跃网站
*   每周 10 亿个验证码解决方案
*   Nocaptcha 每天节省数百万小时

### 难度等级

reCAPTCHA 机器学习引擎根据一系列难度级别对传入的请求进行分类，从“只是一个复选框”到“选择所有带汽车的图像”(图像分类)，到“选择所有带汽车的方块”(图像定位)，再到“好吧，你肯定是个机器人”。

### 融入你的网站

前往[https://www.google.com/recaptcha/admin#list](https://www.google.com/recaptcha/admin#list)，回答几个简单的问题！

您将有几个选择:

*   可见:脚本标签和一个 div
*   不可见:脚本标签和一个带回调的按钮
*   Invisible:用一个 div 编写脚本标签，以便在执行时拥有控制权

没错，有隐形 reCAPTCHA 这种东西！更多见下文。也可以在 [DevGuide](http://g.co/recaptcha/devguide) 上查找更多文档。

不要忘记与服务器端集成

*   使用从 reCAPTCHA 获得的 POST 参数`secret`和`response`创建 HTTP POST to

# 第二部分:过去、现在和未来

### RIP 2 字验证码(reCAPTCHA v1)

**你知道又讨厌的 2 字验证码将于 2018 年 3 月 31 日消亡。** ( [来源](https://www.programmableweb.com/news/google-recaptcha-v1-api-shutting-down-march-2018/brief/2017/10/24)和[上的常见问题](https://developers.google.com/recaptcha/docs/faq#what-happens-to-recaptcha-v1))

人工智能已经发展到可以以 99.8%的准确率解决最难的验证码的地步，但人类只能解决 33%的时间。所以是时候睡觉了。

### [reCAPTCHA v2](https://developers.google.com/recaptcha/docs/display)

您已经点击了几十次的“我是人类”复选框——这实际上被称为“[NoCAPTCHA](https://www.theverge.com/2014/12/3/7325925/google-is-killing-captcha-as-we-know-it)”——有关更多细节，请参见上面第 1 部分中的实现选项。

### [隐形 reCAPTCHA](https://developers.google.com/recaptcha/docs/invisible)-2017 年 3 月 8 日推出

对于低风险流量，根本不需要用户交互来检测您是否是一个机器人！

### reCAPTCHA Android API

作为 [Google Play 服务安全网](https://developer.android.com/training/safetynet/recaptcha.html)的一部分-同样，不需要用户交互来验证你是人类。

### reCAPTCHA 的未来(v3)

v3 现在处于封闭测试阶段:

*   让你控制我们何时展示挑战
*   集成类似于 V2 隐形
*   在管理控制台中，查看您的流量的风险

在[http://g.co/recaptcha/v3](http://g.co/recaptcha/v3)注册 reCAPTCHA v3 测试版公告！