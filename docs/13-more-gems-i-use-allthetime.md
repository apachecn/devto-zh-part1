# 我一直使用的 13 个宝石

> 原文：<https://dev.to/rob__race/13-more-gems-i-use-allthetime>

[![](img/3492a163ef77024e9dea4828f074efb5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Da2J-K-0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b7fomj3nzpejsyjj2ptc.png)

## …我的意思是，当这些宝石有用或者需要的时候，我会去拿很多。

几个月前，我发布了一篇关于我在几乎每个 SaaS 项目中使用的 27 种宝石的文章/故事。这收到了大量的赞扬，以及围绕使用*这么多*宝石的一点点负面意见。

### 宝石。你的经历可能不一样

理所当然，盲目地安装一大堆 gem，而不考虑 gem 的状态、可维护性，甚至增加对 gem 的依赖性。当我写上一篇文章时，我可能有点夸张，因为我似乎会在每个项目中一直使用所有这些宝石。在编写自己的代码和使用 gem 之间总是有一个权衡。无论是功能的可维护性还是纯粹的时间/价值主张。

* * *

注:本文摘自我即将出版的新书[在 Rail 5](https://buildasaasappinrails.com/) 上构建 SaaS Ruby on Rails 中的一章。这本书将指导你从卑微的开始，通过部署一个应用到生产。如果你发现这种类型的内容有价值，你现在就可以获得一个免费的章节！

* * *

### 摩尔宝石

这里有更多的宝石可以帮助你！

**[Foreman](http://ddollar.github.io/foreman/)** —您可以使用这个 gem 从 Procfiles 运行您的应用程序。当您需要运行多个应用程序或服务器时，这非常方便。假设你运行你的 Puma webserver，ActionCable，Webpack 开发服务器等。有了福尔曼，你可以让它们同时燃烧起来。

**[VCR](https://github.com/vcr/vcr)** —这个 gem 允许您记录外部 web 请求，并在未来的测试运行中重放它们。这有两个好处，首先你将记录一个已知的外部请求的工作版本，然后将来的测试运行将只在本地加载 YAML 的 VCR 商店。

**[ChartKick](https://github.com/ankane/chartkick)** —当你需要在你的视图中快速获得图表/图形时，从 ActiveRecord 中提取数据，ChartKick 会让你用一行就搞定(大部分时间)。如果您的图表将包含更复杂的数据，或者如果您需要专门的图表，您可能需要考虑构建 json 响应并使用 javascript 库。

**[旁注](https://github.com/basecamp/marginalia)** —这个 gem 将在日志中为您的 SQL 条目添加注释，让您知道查询可能来自什么应用程序、控制器和操作。这对于调试日志中似乎与操作没有直接关联的查询非常有用。

**[布雷克曼](https://github.com/presidentbeef/brakeman)**——这是少数几种你可能不会通过你的 Gemfile 安装，而只是通过的宝石之一。BrakeMan 是一个安全分析工具，它会让你知道你的应用程序是否有任何安全漏洞。

 ****[RuboCop](https://github.com/bbatsov/rubocop)** —另一个分析工具，但这次是林挺你的 ruby 文件来检查它们是否符合 Ruby 风格指南。

**[并发 Ruby](https://github.com/ruby-concurrency/concurrent-ruby)** —这不一定是每个 gem 文件中都有的 gem，但是当我需要编写自己的并发代码(比如数据导入器)时，我会找到这个坏男孩，让我以自己觉得舒服的方式编写并发 Ruby。此外，许多流行的 gem 实际上使用 concurrent-ruby 作为主要依赖项。

—如果你需要处理第三方 API，你很可能需要处理 OAuth。如果必须处理 OAuth，应该使用 OmniAuth 和他们的开源策略列表，让 OAuth 变得简单，只需要几个配置变量和一些响应处理。

——有一些功能性的宝石，但这一个似乎总是对我最有效。软删除非常适合 SaaS 应用程序，在这些应用程序中，关联和审计可能仍然需要数据，但在其他情况下，对象会显示为已删除。

**[元标签](https://github.com/kpumuk/meta-tags)** —这个 gem 是这样一种情况，对于简单的元 HTML 标签，你可以跳过一个完整的 gem，只从控制器中获取一个值。但是，如果您需要更多的元标签，这可能是您的最佳选择。

**[权威人士](https://github.com/elabs/pundit)**——这个宝石在我的上一篇帖子中出现了很多。Pundit 采用简单的 Ruby 类方法在应用程序中进行授权。与喜欢更明确的代码库的开发人员相比，它没有那么神奇，而且被他们大量使用。这是 Ruby/Rails 开发人员基于隐式/显式偏好在工具上产生分歧的地方。

**[ActiveAdmin](https://github.com/activeadmin/activeadmin)** —如果你习惯了 Django 的管理界面，或者只是想要一个管理区域来管理数据等，那么 ActiveAdmin 可能正是你所需要的。

这些是我对宝石的更多选择，你有什么想要分享的吗(甚至是你自己的无耻之徒)？就在下面评论吧！**