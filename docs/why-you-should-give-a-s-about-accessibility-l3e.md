# 为什么你应该给出一个关于可访问性的答案

> 原文：<https://dev.to/adamaso/why-you-should-give-a-s-about-accessibility-l3e>

## 5700 万美国人有残疾。

根据 2012 年的人口普查报告，5670 万美国人有某种形式的残疾。作为开发人员和设计人员，我们有责任创建适合所有人的网站和应用程序。让超过 80%的人口能够访问网络，并就这些问题教育公众(尤其是其他工程师和设计师)，也是我们的社会责任。

> 考虑影响在线网站、应用程序和文档的可访问性的障碍。这项调查估计患有特定障碍的人数如下:
> 1990 万人(8.2%)在举起或抓握方面有困难。例如，这可能会影响他们使用鼠标或键盘。
> 520 万人(6.3%)患有认知、精神或情感障碍。
> 810 万人(3.3%)有视力障碍。这些人可能依赖屏幕放大镜或屏幕阅读器，或者可能有某种形式的色盲。
> 760 万人(3.1%)有听力障碍。他们可能会依靠音频和视频媒体的文字记录和/或字幕[(来源)](https://www.disabled-world.com/disability/accessibility/websitedesign/)。

## 可访问性如何影响业务

忽视可访问性会造成每年潜在利润的损失。想象一下，像文本颜色选择这样的小事会疏远一半的潜在客户。即使是与年龄相关的疾病，如老花眼(眼睛聚焦能力的丧失)也会阻碍阅读小文本或看到低对比度颜色的能力。

像[塔吉特](https://www.w3.org/WAI/bcase/target-case-study)、[捷蓝](http://dignifiedflyingfordisabled.blogspot.com/2012/04/jetblue-airways-fined-for-inaccessible.html)和 [Scribd](http://www.forbes.com/sites/ericgoldman/2015/03/26/scribd-must-comply-with-the-americans-with-disabilities-act/) 这样的顶级公司已经因为无障碍被罚款/起诉。

> “该案件由加州盲人委员会和三名盲人捷蓝客户提起，涉及捷蓝网站上的访问障碍以及捷蓝机场值机亭对视力障碍者的无障碍性。JetBlue 设计了自己的网站，以防止盲人客户独立和私下访问信息和使用网站的功能。捷蓝航空的机场信息亭不能被盲人使用，因为这些机器没有音频输出或输入信息的无障碍方式。”(来源)

### 美国橄榄球联盟颜色狂奔

易访问性疏忽的另一个例子是 2015 年 NFL 颜色抢购活动。为了庆祝联盟有史以来第一场彩色电视比赛 50 周年，比尔穿上了全红色，喷气机穿上了全绿色。具有讽刺意味的是，NFL 未能统计出 8%的红绿色盲男性。

[![NFL color rush image](img/b84a4af0226ada93a6981a9760c1d8db.png "What red/green color-blind fans saw on their TV.")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A0tHn0Ub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ac5tt25Dr6_XJvFgvnDAy6g.png)

之后，NFL 发布了一份声明,声称他们“今年夏天确实在球场和电视上测试了球衣。标准的电视测试没有考虑到国内粉丝的色盲，这一点变得很明显。

希望这个例子强调了颜色选择和包容性设计意识的重要性。

## 我们能做些什么

使用网页内容可访问性指南清单(WCAG)
使用此清单来实现可访问性合规性——WCAG 2.0 是当前的包容性网页标准。WCAG 适用于所有 web 内容，包括单页和动态 web 应用程序、多媒体和移动网站。

### 使用那些工具！

有各种网站和浏览器插件可以帮助您检查可访问性。

我最喜欢的是:

*   Chrome 开发者辅助工具
*   WAVE(网页可访问性评估工具)
*   ChromeVox 屏幕阅读器
*   参见 Chrome 插件

## 10 总体设计&发展考虑

*   正确使用标题(h1、h2)
*   使用 ARIA 地标(标记网页的部分)
*   向表单域添加标签
*   记住颜色对比
*   使用实时文本，而不是文本的图片(这也有助于搜索引擎优化！)
*   提供视频描述并使用隐藏式字幕
*   选择适用于所有用户的下拉菜单(有些人可能无法触发或访问子菜单)
*   不用键盘测试网站(很多人没有鼠标)
*   在移动和桌面上测试带有辅助技术的网站
*   给图片添加合适的替代文字[(关于替代文字的好文章)](http://webaim.org/techniques/alttext/)

易访问性是网络发展的新标准，我们不能落后。T3】