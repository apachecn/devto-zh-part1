# 我最喜欢的红宝石

> 原文：<https://dev.to/marcroberts/my-favorite-ruby-gems-50b>

许多原本需要花费数小时或数天才能完成的任务，只需在项目中包含相关的 Ruby Gem 就可以轻松实现。以下是我挑选的有趣、有用、最重要的节省时间的宝石:

> 什么是红宝石？Ruby，尤其是 Rails，有一个优秀的开发人员社区，提供了许多开源(通常)库，称为 Gems。Ruby，Gem，geddit？

首先，如果没有出色的 Jekyll(一个支持博客的静态站点生成器)，这个博客就不会存在。我也经常使用 [Sinatra](http://www.sinatrarb.com/) 来开发不需要像 [Rails](http://rubyonrails.org/) 这样大型框架的小型网站。

说到身份验证，我首先想到的总是[设计](https://github.com/plataformatec/devise)。这一点，以及它的各种扩展宝石，涵盖了你想要的任何种类的认证:用户名/电子邮件&密码，Oauth(脸书，Twitter 等)，Mozilla Persona 等等。

允许您在 ActiveRecord 中的一个数字数据库字段中存储大量布尔字段，并像访问单个字段一样访问它们。 [Kaminari](https://github.com/amatsuda/kaminari) 已经取代了 will_paginate 成为我的分页选择。

[cache_digests](https://github.com/rails/cache_digests) 在 view 中启用了俄罗斯玩偶风格的嵌套缓存机制，可以大幅提高速度。在信号与噪音博客文章[上阅读更多关于 basecamp next 如何变得如此之快](https://signalvnoise.com/posts/3112-how-basecamp-next-got-to-be-so-damn-fast-without-using-much-client-side-ui)，在第 2 点下。

ActiveAdmin 快速简单地构建有价值的管理界面，这是构建任何 web 应用程序所需的后端服务的一个很好的起点。 [lograge](https://github.com/roidrage/lograge) 将大多数请求的 Rails 日志输出减少到一行，很容易解析，非常适合搜索和排除故障。

我是 [Heroku](http://heroku.com/) 的忠实粉丝，所以很明显我在这里使用了一些特别有用的宝石: [rails_12factor](https://github.com/heroku/rails_12factor) 强制执行了[12 Factor 应用程序](http://12factor.net/)的几个方面，Rails/Heroku 默认情况下不会这样做， [font_assets](https://github.com/ericallam/font_assets) 确保你的字体资产被提供了正确的标题，以使浏览器(主要是 Firefox)能够正确使用它们，[Heroku-de pirater](https://github.com/romanbsd/heroku-deflater)gzips 只是将受益于这种压缩而不会浪费的内容 gz 文件)。

我最喜欢的许多宝石都是在开发过程中单独使用的。 [quiet_assets](https://github.com/evrone/quiet_assets) 将那些嘈杂的资产相关的请求行从您的开发日志文件中删除。 [meta_request](https://github.com/dejan/rails_panel) 和它的 RailsPanel Chrome 扩展让你可以在 Chrome 的开发者工具中访问关于每个请求的信息，下面是它的应用:

[better_errors](https://github.com/charliesome/better_errors) 通过在浏览器中的错误点或任何父框架处为您提供一个完全交互式的控制台，使内置的 rails 错误页面变得更好。去年，我录制了一小段演示视频:

这些只是冰山一角，[RubyGems.org](http://rubygems.org/)有近 90，000 颗宝石上市。你最喜欢的宝石是什么？让我们知道。