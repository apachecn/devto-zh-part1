# DjangoCon Europe 2017 回顾

> 原文：<https://dev.to/djangotricks/recap-of-djangoconeurope-2017>

[![](img/8c984ab725355799196c400269fa1ece.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_uwiJO5A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-MS6aJAZmetQ/WeGASiFh76I/AAAAAAAAB0E/ldBr4r515BQiBCRkN9-d9511rjX-1pIMwCLcBGAs/s1600/recap-of-djangocon-europe-2017.png)

"强哥康，为什么每个人都穿这件 t 恤？"四月初，意大利佛罗伦萨机场的安检人员疑惑道。原因是 2017 年 django con Europe 在那里举行了一周，在一个令人兴奋的地方充满了有趣的谈话。

我喜欢的是，这个会议不仅是关于 Django 世界的技术创新，也是关于程序员在日常生活中处理的人类问题。

## 有趣的非科技话题

根据一份声明，会议的目标是加强 Django 社区，并对 Django 的工作形成负责任的态度。

### 健康成功的社区

我们必须建立更强大的社区，包括所有希望不受歧视地参与其中的人。虽然，起初可能很难，因为人们有偏见，即支持或反对一个人或群体的偏见；通过强调，我们可以接受和包容每一个人，无论他们的性别认同或表达、性取向、种族、民族、神经多样性、年龄、宗教、残疾、地理位置、食物多样性、体型或家庭状况如何。

> 重视多样性和个体差异是一个健康、积极和成功的社区的关键，它赋予其成员权力，并帮助他们变得更加强大和幸福。
> 
> ### 对我们如何使用技术的责任

信息技术公司(苹果、Alphabet、微软、亚马逊和脸书)是世界上交易量最大的公司。它连接人和他们的东西，自动化流程，存储和处理历史数据。通常你不需要很多物质资源就可以开始一项 IT 业务。软件开发人员有能力塑造未来，但应该负责任地使用这种能力:

> 因此，我们肩负着重大责任:让未来变得更加美好，让未来分布更加均匀，跨越性别差距和歧视，打破经济、政治和地理障碍。
> 
> ### 业务

*   创建在线业务时，重要的是要考虑你的产品将给人们带来的商业价值，以及你用它赚钱的方式。不要在没有和客户交谈的情况下做出假设。
*   在为你的公司选择员工时，给他们自由去证明他们的知识:通过一个测验，或者白板面试，或者一个带回家的编程任务。不同的人有不同的方式来最好地展示他们的技能。
*   尽早发射。使用谷歌分析或其他分析服务跟踪使用统计数据。为邮件列表收集电子邮件。在博客和个性化邮件中写下你的产品。
*   Django 是一个基于许多专业人士辛勤工作的开源项目，如果你从中获得任何商业价值并欣赏这个框架，你应该[捐赠给 Django 软件基金会](https://www.djangoproject.com/fundraising/)。

## 有趣的科技话题

从技术角度来看，我喜欢会议中提到的几个想法:

### 自动化您的流程

*   开始新项目时，你可以准备好[样板文件](https://docs.google.com/presentation/d/15Jcf3HBko0qzE7HY411-4RjVDiCiPeEZlZbipTo7K50/edit#slide=id.g2124e76ce9_0_1607)，里面有已经准备好的最常用的功能。Django 管理命令`startproject`有一个参数`--template`,您可以将 URL 传递给一个 zip 文件。
*   开发人员应该有用于调试的故障诊断清单，就像飞机驾驶员一样。
*   有几种测试类型。单元测试检查单个函数或方法的功能。集成测试检查不同的单元如何一起工作。功能测试从头到尾检查业务需求的过程是如何工作的。最后，还有手工测试，要求人们点击网站并填写表格。一些测试，如涉及第三方认证或移动电话的测试，几乎不可能自动化。无论如何，手工测试在时间和资源上是最昂贵的(除了对测试人员来说很无聊)，功能测试紧随其后，然后是集成测试，最后是单元测试。虽然自动测试增加了开发时间，但从长远来看，它使系统更加稳定和防错。

### 姜戈怎么样了

*   您可以[用定制的查找、事务和过滤预取来扩展 Django ORM](https://speakerdeck.com/mjtamlyn/weird-and-wonderful-things-to-do-with-the-orm) ,使您的查询集更具可读性和功能性。
*   再次，PostgreSQL 比 MySQL 有更多的功能，也更稳定。使用`EXPLAIN ANALYZE ...` SQL 命令[找到数据库查询的瓶颈](https://fr.slideshare.net/LouiseGrandjonc/the-amazing-world-behind-your-orm)。您通常可以通过添加索引来修复它们。
*   您可以为数据库表定制[索引](https://speakerdeck.com/markush/to-index-or-not-thats-not-the-question-djangocon-europe-2017)，以优化 PostgreSQL(或其他供应商)数据库的性能。
*   Django 1.11 发布了，这是一个长期支持的版本。

### 第三方 Django 包怎么样

*   在从不同角度(数据库支持、django admin 和 forms 中的集成、性能等)分析了 6 个最流行的模型翻译包(parler、hvad、klingon、model translation、nece 和 i18nfield)之后，)， [django-hvad](https://github.com/KristianOellegaard/django-hvad) 似乎是获胜的方法。
*   你可以使用 [django-cms](https://www.django-cms.org/en/) 和 [djangocms-cascade](https://github.com/jrief/djangocms-cascade) 用很少的编码配置可视化地构建静态网站。djangocms-cascade 为 djangocms 提供了另一种嵌套插件系统。

### Django 项目怎么样

*   如果你为发展中国家开发 web 应用程序，你必须记住这些事情:人们可能使用手机而不是电脑(你需要有小图片或没有图片的响应式设计)，互联网连接缓慢且不稳定(网站必须很快，最好有离线版本)，用户并不总是懂英语(网站应该被翻译和改编)，人们居住的地方并不总是有街道地址。
*   一些有趣的用例:使用 Arduino 和 Django 跟踪心脏的健康状况，使用 Django 为整个欧洲提供天气数据，使用 Django 管理伯明翰的一个广播电台。

## 感谢

最后，感谢[组织者](https://twitter.com/DjangoConEurope)让这次会议如此成功。这座城市很美，食物和咖啡很美味，会谈的地点令人印象深刻。期待下一届 DjangoCon 欧洲！

*本帖最初发表于[djangotricks.blogspot.com](http://djangotricks.blogspot.com/2017/04/recap-of-djangoconeurope-2017.html)T3】*