# 用水豚测试你的 Jekyll 网站

> 原文：<https://dev.to/phansch/testing-your-jekyll-website-with-capybara>

从我的个人博客上传的十字:[链接](http://phansch.net/2017/03/13/testing-jekyll-websites/)

即使它只是一个像这样的静态网站，知道最重要的东西工作总是好的。

我最近在将我的博客从 Jekyll 2.5 迁移到 Jekyll 3.3 时遇到了问题。升级后永久链接被破坏了，我只是在更改生效后才注意到这一点。一些基本的测试可以很容易地防止这种情况。

在我修复了损坏的永久链接后，我决定添加一些基本的水豚测试，因为我也每天为 Ruby on Rails 应用程序编写这些测试。

## 设置

现在的设置非常基本，有`capybara`、`rack`、`rake`和最重要的`rack-jekyll` :

```
# Gemfile
source 'https://rubygems.org'

gem 'jekyll', '3.3.1'

group :test do
  gem 'capybara'
  gem 'rack'
  gem 'rack-jekyll'
  gem 'rake'
end 
```

Enter fullscreen mode Exit fullscreen mode

`test_helper`告诉`Capybara.app`使用`Rack::Jekyll`为我们的测试提供 Jekyll 站点的框架版本。

```
# test/test_helper.rb
require 'minitest/autorun'

require 'bundler/setup'
require 'capybara/dsl'
require 'rack/jekyll'

Capybara.app = Rack::Jekyll.new(force_build: true)

class CapybaraTestCase < MiniTest::Test
  include Capybara::DSL

  def teardown
    Capybara.reset_sessions!
    Capybara.use_default_driver
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

配置好`test_helper`之后，我们现在可以像往常一样开始编写水豚测试:

```
# test/features/this_blog_post_test.rb
require 'test_helper'

class ThisBlogPostTest < CapybaraTestCase
  def test_describes_how_to_write_tests
    visit '2017/03/02/testing-jekyll-websites/'
    assert page.has_content?('configured we can now start '\
      'writing Capybara tests as usual:')
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 考什么

这个当然要看你的网站。对我来说，我确定了几件事:

*   首页(有点过时)应该有我的[简历](https://gitlab.com/phansch/latex-resume/raw/master/resume-philipp-hansch.pdf)的链接
*   点击存档链接应该会把我带到存档
*   [/archive](http://phansch.net/archive) 应该包含第一篇和最后一篇文章
*   [/存档](http://phansch.net/archive)上的日期应该是正确的格式
*   在[/写作](http://phansch.net/writing)上的“阅读更多”链接应该会把我带到正确的博文
*   一篇博客文章应该有正确的阅读时间
*   一篇博客文章应该有正确格式的日期
*   主要导航项目会转到包含正确内容的页面
*   [原子提要](http://phansch.net/atom.xml)应该是有效的原子提要

除了 atom feed 验证之外，这都是使用`has_content`和一些基本的 Ruby 进行时间检查的简单水豚。你可以在这里找到所有的测试[。](https://gitlab.com/phansch/phansch.gitlab.io/tree/master/test/)