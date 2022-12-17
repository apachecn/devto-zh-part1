# 铁路错误

> 原文：<https://dev.to/evantypanski/a-rails-mistake>

我的网站是我创建的第一个面向公众的应用程序，因此它肯定会有一些幼稚的错误。

我犯的主要的、明显的错误是开始使用 Rails。我认为学习如何使用 Ruby on Rails 将会提供工作保障，以及更多将来会用到的实用知识，比如我经常说的“学习如何学习”。但是，对于一个小规模的个人网站来说，使用 Rails 不仅是矫枉过正，而且还阻碍了我学习 Rails 功能的能力，让我主动寻找如何减少功能，而不是学习如何使用它们。

可能最突出的问题是我决定学习如何使用数据库，至少是稍微学习一下。我为我的投资组合中的帖子和项目制作了模型，从数据库中处理和提供，带有标题、内容等等。因此，我需要一种编辑数据库的方法。所以，我用 active admin 创建了一个管理站点，但出于安全原因，我希望这个管理站点是隐藏的，除非我需要发帖。这样的代码结果是:

```
 get '/admin', to: 'application#page_not_found'
  get '/admin/login', to: 'application#page_not_found'
  get '/admin/dashboard', to: 'application#page_not_found'
  get '/admin/admin_users', to: 'application#page_not_found'
  get '/admin/admin_users/:id', to: 'application#page_not_found'
  get '/admin/admin_users/:id/edit', to: 'application#page_not_found'
  get '/admin/admin_users/new', to: 'application#page_not_found'
  get '/admin/comments', to: 'application#page_not_found'
  get '/admin/posts', to: 'application#page_not_found'
  get '/admin/posts/:id', to: 'application#page_not_found'
  get '/admin/posts/:id/edit', to: 'application#page_not_found'
  get '/admin/posts/new', to: 'application#page_not_found'
  get '/admin/posts/delete', to: 'application#page_not_found'
  get '/admin/projects', to: 'application#page_not_found'
  get '/admin/projects/:id', to: 'application#page_not_found'
  get '/admin/projects/:id/edit', to: 'application#page_not_found'
  get '/admin/projects/new', to: 'application#page_not_found'
  get '/admin/projects/delete', to: 'application#page_not_found' 
```

Enter fullscreen mode Exit fullscreen mode

这是一些令我非常惭愧的代码。我试图通过多次搜索“rails routing 中的 Regex”和类似的查询以及浏览文档来补救这种情况(可能没有我应该做的那么努力)，但我找不到任何补救这种情况的东西，所以我坚持使用 17 行代码完全用于从管理页面重新路由，这样就没有人能看到我的管理页面并利用我没有发现的一些奇怪的安全漏洞。我相信我的知识库是私有的。

所以没错，我用个人网站学习 Rails 是犯了一个错误。目前，我的网站是用 Jekyll 制作的，从 rails 移植过来用了不到一个小时，写文章和作品集条目变得非常容易(理解 AWS 上的设置比理解 Heroku 要花一个多小时，但那是另一回事)。此外，我不再需要处理数据库，我曾试图将它用于帖子中的图片，但结果并不太好。

我不再担心写文章，因为它只是简单的降价文件。我不再用 Rails 来改变网站的布局和格式，因为 Jekyll 没有那么臃肿，只是在我的电脑上写文本文件，而不用 ckeditor 来写帖子，在那里我几乎看不到我写的东西，因为页面太臃肿了，制作得很差。使用 Vim 来写文章要简单得多，也有趣得多。

那么，我学到了什么？不要用矫枉过正的方法去学习一项技能。不仅阻碍了我网站的初期表现，也阻碍了我正确学习 Rails 的能力。这应该是不言而喻的，但一个单人的，淡化版本的 Rails 只是一个恼人的臃肿版本的 Jekyll。这很糟糕。