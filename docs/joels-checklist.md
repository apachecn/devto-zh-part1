# 乔尔的清单

> 原文：<https://dev.to/joelbennett/joels-checklist>

引用已故的兰迪·波许的话，“经验是当你没有得到你想要的东西时你所得到的东西”。大多数时候，您希望您的软件产品能够顺利构建，您的部署能够顺利进行，并且您的客户能够满意。但这并不总是发生。在过去的几年里，我已经有了相当多的“经验”。每次我碰到什么事，我都试着记下来，这样就不会再碰到了。你知道-愚弄我一次，你真可耻。愚弄我两次，我感到羞耻...

这已经慢慢演变成了“乔尔的清单”。清单不是处理事情的最佳方式，因为它们不会抓住每一个案例，但至少通过检查清单上你搞砸的事情，你可以检查以确保你不会再次遇到同样的问题。飞行员查看清单是有原因的。我们的特定团队并没有很努力地遵循这一点，但我仍然发现它有时很有帮助，所以我认为它值得分享。

请记住，这个清单主要是针对我所做的——运行一个专门从事农业建模(使用 Python)的后端开发团队，上面有一个 web 前端。所以这里有一些非常具体的东西，但它仍然可能对其他人有所帮助。

# 乔尔的清单

## 设计

在您编写一行代码之前，应该先阅读这一部分！

*   你真的了解这个问题吗？
*   你和将要使用这个系统的用户谈过了吗？他们理解你的设计吗？用户能理解你的理论设计吗？
*   你的模拟用户界面允许用户完成所有用例吗？
*   该应用程序将使用多种语言吗？如果是，你是如何处理的？
*   提议的更改是添加还是删除现有系统的功能？如果添加了新功能，它们真的是想要的吗？
*   如果功能被移除，用户会有多生气？用户目前正在使用它们吗？你有证据支持这种说法吗？是否有执行相同操作的替代方法？
*   谁拥有最终的权力来决定哪些功能可以通过筛选，哪些不可以？他们和你一起审查设计了吗？

## 后端:

*   数据库模型真的需要吗？有没有应该是数据库模型却不是的东西？
*   数据库模型是否正常化了？如果不是，那么它们没有被正常化是否有正当的理由？
*   可空字段实际上应该是可空的吗？它们可空有逻辑上的原因吗？
*   数据库字段名匹配模型字段名和 UI 标签吗？
*   数据库表中的所有列都准确地反映了它们将要存储的数据类型吗？例如:十进制数有正确的小数位数吗？
*   什么使得给定的数据库行是唯一的？这会对其他地方产生影响吗？
*   数据库/表结构看起来设计得好吗？有什么特别奇怪的事情吗？
*   是不是所有的模型都有办法打印出来变成一串？例如:`__unicode__(self)`还是`.ToString()`？
*   所有的 API 端点都包装在 try/except 块中吗？任何 API 调用会显示内部服务器错误或未处理的异常吗？
*   有几乎相同的代码段吗？能去掉吗？
*   代码中的注释实际上反映了代码的功能吗？
*   系统的所有输入都经过适当的消毒了吗？
*   所有结果都是按逻辑顺序返回的吗(例如:排序)？
*   每当我们写一个文件时，我们是否从文件路径和名称中去掉了无效的/坏的字符？
*   当我们拥有多个事物时(例如:多个相关的外来对象)，事物会如何表现？
*   每当我们从数据库中获取对象集合时，我们会处理空集合的情况吗？
*   所有返回给客户端的异常都是人类可读的吗？
*   你曾经比较过浮点值吗？如果是的话，你考虑到浮点不精确了吗？

## 用户界面

*   所有的 UI 字段在数据库中都有合适的存储位置吗？
*   UI 可以在低分辨率屏幕上工作吗？(或者，您是否有最低分辨率，在该分辨率下是否有效？)在高分辨率或高 DPI 显示器上看起来还好吗？
*   必填字段是否对用户标记为必填？
*   在进行服务器调用之前，用户是否被强制输入必填字段？
*   向用户显示的所有错误信息是否清楚地表明了问题的实质？
*   字段的所有标签都正确吗？
*   必要时，是否有适当的工具提示或帮助图标？
*   如果使用任何种类的窗口系统，窗口标题是否正确？窗口可以调整大小吗？所有控件是否都正确停靠/调整大小？窗口有最小尺寸吗？
*   尽可能重复使用添加/编辑对话框吗？(它们往往是相同的，为什么要重复代码呢？)
*   用户 A 可以编辑用户 B 当前显示的数据吗？如果有，是如何处理的？当用户 A 保存数据时，用户 B 能看到更新的数据吗？
*   所有对系统的调用都包装在 try/catch 块中吗？客户端会看到未处理的异常吗？
*   真的需要可以弹出的消息框吗？(有没有更优雅的处理弹出消息框的方式)？(与模态对话框相同)
*   所有可能需要长时间运行的操作都向用户说明了吗？(是否有适当的“加载...”哪里需要指标？)
*   所有验证消息是否都出现在导致它们的数据所在的同一区域？
*   系统的所有输入是否都有前导/尾随空格？
*   在不允许值< 0 的情况下，是否检查任何整数输入是否为-1？
*   是否检查任何字符串输入是否为 null/空/空白？
*   是否检查字段的最大长度？如果有一个最大长度，超过时会发生什么？(这个也适用于后端！)
*   当显示一个用户界面时，如果没有显示结果会很明显吗？例如:显示当前显示的项目数？
*   如果某个东西在用户界面上被禁用，应该有某种直观的原因(例如:工具提示说“禁用是因为……”
*   所有的上下文菜单都有一个在别处可以找到的等效按钮吗？(例如:是否只有在上下文菜单中才能找到功能？如果是这样，那就不太理想了)。
*   会被连续使用的 UI 元素(例如:重复点击)会移动位置吗？(它们应该在同一个地方，这样用户就不必移动鼠标来点击下一个东西)

## 测试和性能

*   所有单元测试都通过了吗？
*   当每个数据库模型有不止一个实例时，所有的特性还能工作吗？
*   系统在目标硬件上是否以合理的速度运行？
*   在处理合理数量的数据时，系统是否以合理的速度运行？
*   以不同用户身份登录时，所有功能是否都能工作(如果适用)？
*   是否有可能通过 UI 创建损坏的/坏的数据？

## 部署

*   部署到生产环境时，所有调试设置是否都正确设置为 false？
*   是否部署了任何非精简文件？如果是，这样做有正当理由吗？
*   所有的测试仍然通过部署的版本吗？