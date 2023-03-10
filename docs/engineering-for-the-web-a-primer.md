# 网络工程:入门

> 原文：<https://dev.to/jesalg/engineering-for-the-web-a-primer>

在过去十年左右的时间里，为网络构建软件是一段有趣的旅程。完成工作所需的技能和技术已经随着平台本身发生了巨大的变化。过去，一个单独的“网站管理员”角色描述的是在共享主机提供商上建立一个五页网站的人，现在已经扩展到专业的团队运动，角色范围更广，包括前端、后端、开发运维、全栈等等。

考虑到这种演变，思考构成这些角色的潜在技能和技术是什么，以及我们在哪里为每个角色划定领域边界是很有趣的。

因此，让我们从前端开发人员的角色开始。他们应该具备哪些技能？

## 前端

[![](img/77863f46ffef42439e7a633b4d72211a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ziYVSJbG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.giphy.com/oScoODXwcQrpC.gif)

一般来说，我遇到过两种类型的前端开发人员。那些更专注于前端 UI/UX 设计方面的人与那些更专注于让这些元素发挥作用的人。当然，也有人两者兼而有之，但他们是稀有品种。

### 设计重点

*   用户界面设计和线框 PhotoShop，Illustrator，素描等
*   HTML 和相关的[模板引擎](https://github.com/sorrycc/awesome-javascript#templating-engines)——HAML、杰德、小胡子等
*   CSS 和[它是预处理程序](https://github.com/showcases/css-preprocessors)–SASS 或更少
*   可访问性和浏览器兼容性怪癖

### 发展重点

*   JavaScript 和[它的超集](https://github.com/jashkenas/coffeescript/wiki/list-of-languages-that-compile-to-js)——ClojureScript、CoffeeScript、TypeScript 等
*   [性能&关于浏览器和 DOM 渲染的优化概念](https://github.com/davidsonfellipe/awesome-wpo)
*   NodeJS 与[包管理器](https://github.com/sorrycc/awesome-javascript#package-managers) & [构建工具](https://github.com/sorrycc/awesome-javascript#bundlers)–NPM、Gulp、WebPack 等接口

最后一点，当我们过渡到后端时，界限已经开始模糊

## 后端

[![](img/8629aea19cf17b26cfc3e3707dcfd3ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gTlf1bKF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.giphy.com/D0EjguuQzYr9m.gif)

*   [软件设计模式&实践](https://en.wikipedia.org/wiki/Software_design_pattern#Classification_and_list)
*   面向对象/函数式编程
*   数据建模
*   API 设计–REST/hate OAS
*   SDK/第三方服务集成–条带、SendGrid、Twilio 等
*   [软件安全](https://github.com/paragonie/awesome-appsec)–XSS、SQL 注入等
*   软件可扩展性和性能

同样，最后一点涉及到 DevOps，因为它涉及到对网络基础、内存管理、进程/线程等的一些理解

## DevOps

[![](img/037ff58f78d4f4eba928f764e2e9d116.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CwmvGmun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.giphy.com/a5ptfHj2GqOmk.gif)

*   云和 PaaS 环境–AWS、Heroku 等
*   [虚拟化](https://github.com/n1trux/awesome-sysadmin#virtualization) &集装箱化——码头工人、流浪汉、OpenStack 等
*   [供应](https://github.com/n1trux/awesome-sysadmin#configuration-management) & [编排](https://github.com/n1trux/awesome-sysadmin#cloud-orchestration)——厨师、木偶、替身等
*   [持续整合](https://github.com/ciandcd/awesome-ciandcd) & [交付](https://github.com/n1trux/awesome-sysadmin#deployment-automation)——Jenkins、TeamCity 等
*   [伐木](https://github.com/n1trux/awesome-sysadmin#log-management) & [监控](https://github.com/n1trux/awesome-sysadmin#monitoring)——纳吉奥斯、莫尼特、纽舍利、麋鹿等
*   [安全](https://github.com/sbilly/awesome-security) & [加固](https://github.com/enaqx/awesome-pentest)—绊网、Snort、Netcat 等

## 全栈

[![](img/e17f3795262f6057c3f70e18ed69c610.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9ECsFr-J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.giphy.com/3ornjMHvsBLsHqfCh2.gif)

这涵盖了 web 堆栈三位一体中的一些常见技能，尽管这绝不是一个详尽的列表。当然，并不是每个人都能看到自己的技能完全符合其中一个或多个条件。根据组织和项目的不同，角色往往略有不同。

能够超越这三个角色界限的工程师属于全栈类。他们能够学习并跟上这些领域的大多数主要发展，根据手头的任务有不同程度的关注。

随着 NodeJS 和同构应用架构的兴起，这将变得越来越普遍。

## 10x /忍者/摇滚明星

[![](img/f95bfbaa6462391dfaa9710e60da5a1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--69HnMxdc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.giphy.com/3o85xlaupheJ5o4Gm4.gif)

这些角色之间有一些共同的技能，这些技能通常不是明确要求的，但最终可能会决定一名工程师是否被赋予神话般的 10x /忍者/摇滚明星标签。当然，这些标签都是很松散的，甚至这样一个东西的存在也被激烈地争论着，但是让我们说那是一个东西，它会或者应该意味着什么？也许这些技能是一个开始:

*   熟练使用现代 DVCSs(如 git、hg 等)进行资源管理
*   利用文本编辑器/ide，如 Vi、Emacs、SublimeText、Atom 等，最大限度地提高效率
*   能够灵活地对任务进行优先排序和管理
*   能够收集相关利益相关者的要求，并提出正确的问题
*   项目规划和评估
*   继续教育
*   软技能/人际技能

目前就这些。很想听听你对此的看法。这是一本好的入门书还是缺少一些基础知识？你在这个行业有什么经验？

*这篇文章最初发表在[我的博客](https://jes.al/2016/07/engineering-for-the-web-a-primer/)上。如果你喜欢这篇文章，请在社交媒体上分享，并在推特上关注我！*