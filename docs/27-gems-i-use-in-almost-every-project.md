# 我几乎在每个项目中都使用 27 颗红宝石

> 原文：<https://dev.to/rob__race/27-gems-i-use-in-almost-every-project>

### ...几乎离不开

Rails 社区因其开源宝石而繁荣。实现一个写得好的、测试好的和审查过的 gem 可以使新特性的开发在几天和几周之间产生差别。

并不是每个宝石都很棒，但是我想分享一个我在最近参与的项目中使用过的宝石列表。

* * *

注意:这篇文章摘自我即将出版的书[在 Rail 5](https://buildasaasappinrails.com) 上构建 SaaS Ruby on Rails 中的一章。这本书将指导你从卑微的开始，通过部署一个应用到生产。如果你觉得这类内容有价值，这本书现在就在预售中！

* * *

以下是 27 种宝石:

*   **[aasm](https://github.com/aasm/aasm)**——原名，充当 _ 状态 _ 机器。这个 gem 将帮助您处理复杂或高度分支的工作流的状态管理。尤其是当您处理条带或其他可能经过一系列阶段的对象时。
*   **[LoDash-rails](https://github.com/rh/lodash-rails)**-LoDash 非常有助于在 javascript 和大多数 Rails 应用程序的 CoffeeScript 中添加可枚举函数。
*   **[local_time](https://github.com/basecamp/local_time)** -这是一个 gem，包括一个视图助手和 javascript 助手来显示相对时间(即“2 分钟前更新”)。它不仅会格式化字符串，还会随着时间的推移在客户端更新字符串。
*   **[设计](https://github.com/plataformatec/devise)和[设计 _ 可邀请](https://github.com/scambra/devise_invitable)** -设计是过去几年 Rails 中使用最多的认证库。虽然有一些新兴的库提供认证服务，但对 Devise 的支持和它的易用性使得它非常适合在您的 SaaS 应用程序上快速移动。设计可邀请的是一个附加的宝石，将允许您有一个邀请系统，让用户邀请其他用户到您的应用程序。
*   **[rolify](https://github.com/RolifyCommunity/rolify) 和 [cancancan](https://github.com/CanCanCommunity/cancancan)** - Rolify 允许应用程序在用户对象上分配角色，如用户或管理员。然后，CanCanCan 允许您扮演这些角色并应用授权策略。
*   **[paper _ trail](https://github.com/airblade/paper_trail)**——这将允许你在特定的模型上设置审计日志，这样你就可以看到哪些用户做了更改。使用该信息在活动日志中显示在您的应用程序中，或者仅用作对更改进行故障排除的一种方式。
*   **[-ranked-model](https://github.com/mixonic/ranked-model)**-允许对兄弟元素进行快速排序。它不使用顺序位置整数，而是使用大数字来更快地排序。
*   **[bootstrap-sass](https://github.com/twbs/bootstrap-sass)** -允许你使用 Bootstrap HTML/CSS 框架为应用程序快速构建一个可用的界面。
*   **[friendly _ id](https://github.com/norman/friendly_id)**——用于混淆来自 URL 的 rails 对象中使用的自动递增的主 ID。
*   Slim-rails——Slim 是另一种模板语言，它不使用嵌入了 Ruby 代码的 HTML 标记，它允许你编写简单的标记，而不需要结束标记。例如:

```
<%- if @user.admin? %>
  <h1>Welcome back admin</h1>
<%- else %>
  <h1>Welcome back pal</h1>
<%- end %> 
```

...会变成

```
- if @user.admin?
  h1
    | Welcome back admin
- else
  h1
    | Welcome back pal 
```

*   这个 gem 允许你在 Redis 队列的支持下运行后台任务。而不是在主请求循环中运行像发送电子邮件这样的任务。您可以将它放入 Sidekiq 作业中，在后台运行。
*   -这个宝石处理分页和它的边缘情况。它还包括已经与 Bootstrap 一起工作的模板。
*   **[回形针](https://github.com/thoughtbot/paperclip)和[AWS-SDK](https://github.com/aws/aws-sdk-ruby)**——结合这两个宝石将允许您通过您的应用程序将头像图像等资产上传到 AWS S3。
*   这将有助于围绕计算金钱时发现的常见问题进行标准化，并提供一些模板助手。
*   这是一个搜索抽象，允许你创建简单的表格来搜索表格。例如用户索引页面上的搜索表单。
*   **[-Slack-notifier](https://github.com/stevenosloan/slack-notifier)**-简单的 Slack API 集成工具，快速向 Slack Webhook 发送消息。当有新用户注册或在其他地区注册时，Tiy 可以用它来发送通知到您自己的 Slack。如果 Slack 需要更多的 Webhook 集成，您可以使用更深入的 gems。
*   **[预热器轨道](https://github.com/fphilipe/premailer-rails)** -这有助于将 CSS 包含到邮件模板中。Gmail 通常会根据他们可以忽略包含的 CSS 来破坏电子邮件模板。这个 gem 自动将样式合并到标记标签中，以便在 Gmail 中使用。
*   这个工具允许你解析通过邮件发送 API 收到的邮件。我使用了这个 gem 和它的 Sendgrid 伙伴 gem，但是你也可以选择其他的邮件提供商。
*   ——这个 gem 允许你向第三方异常聚合器发送异常。否则，您将需要建立自己的异常通知流程，或者花费所有时间查看 Rails 的日志。
*   **[rspec-rails](https://github.com/rspec/rspec-rails) 、 [factory_girl_rails](https://github.com/thoughtbot/factory_girl_rails) 、[simple cov](https://github.com/colszowka/simplecov)**——我个人喜欢在默认的 rails 测试框架之上使用 rspec。我发现，对于更复杂的模型关联，推理模型工厂比推理固定设备更容易、更快。SimpleCov 是一种跟踪测试覆盖率的简单方法。
*   -这个 gem 将在浏览器中打开邮件，而不是在开发环境中尝试发送邮件。
*   **[rack-cors](https://github.com/cyu/rack-cors)**——这块宝石将帮助建立 CORS 规则。如果从不是当前 Rails 应用程序配置的域名的域访问 JSON 请求，这将很有用。如果前端代码向 Rails 应用程序发送请求，这将非常有用。

这些是我挑选的宝石，你的呢？