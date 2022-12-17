# 导轨配置

> 原文：<https://dev.to/eclecticcoding/rails-configuration-ijm>

所以这篇文章你可以作为我今天学到的东西归档保存。这让我非常惊讶，我写信来分享。这是专门写关于 [Ruby on Rails](https://rubyonrails.org/) 和初始设置的。

## 详细信息

当我用 CLI generate 启动一个新项目时，我会花几分钟时间设置环境，就像我喜欢它开始开发一样，我想任何开发人员都会这样做。

例如，当我使用 Create React App 启动一个 React 项目时，它总是相同的:

*   制作`src/components`目录
*   复制我的点文件，得到我喜欢的 Eslint 和更漂亮的配置。
*   复制我的`renovatebot.json`配置文件
*   删除 CSS 模块，安装`sass`，复制我的 Sass 7 合 1 样板。

这太烦人了，我创建了自己的 React 样板文件来流行这个过程。我将很快写下这一点。

### 红宝石轨道

我在[熨斗学校](https://flatironschool.com/)开始使用 [Ruby on Rails](https://rubyonrails.org/) 作为独立的后端 API。我开发了一种方法，并发现自己也在这个平台上设置了环境。

今天我读了 Samuel Mullen 的一篇文章[配置新的 Rails 项目](https://samuelmullen.com/articles/configuring_new_rails_projects_with_railsrc_and_templates/)，了解到你可以设置`.railsrc`和模板文件来简化设置。我做了更多的研究，并为自己开发了一个工作进展配置。

首先，请确保在您的用户帐户路径中创建一个点文件:`.railsrc`。

### 数据库

当我第一次开始使用 Rails 时，我使用了 Rails 默认的一个 [SQlite3](https://www.sqlite.org/index.html) 数据库。它很容易安装和使用，但是在几个具有挑战性的部署到 [Heroku](https://www.heroku.com/) ，支持 [Postgres](https://www.postgresql.org/) 之后，我把我所有的项目都移植到了 Postgres。所以，我添加到`railsrc`文件的第一个开关是 Postgres DB 的开关(见下文)。其次，我添加了一个模板文件，我将在下面详述:

```
--database=postgresql
--template=/home/webrev/development/my-docs/rails-template.rb 
```

Enter fullscreen mode Exit fullscreen mode

## 模板

让我们来看看下面编号的一些设置:

1.  我正在安装一些 gem，然后运行捆绑安装。
2.  将生成器设置为在生成资源时不安装样式表，因为我将使用 SCSS 样板文件。
3.  创建 Postgres 数据库，这样我在打开项目后就不必创建了。
4.  初始化一个存储库并进行初始提交。这一步并不完美。它按预期运行，但是随着安装的继续，并不是所有的更改都被提交。这一步我还在努力。

```
#add guard-minitest and spring to dev - 1
gem_group :development do
  gem 'guard-minitest'
end

gem_group :test do
  gem 'minitest-rails-capybara'
end

run "bundle install"

#config generator defaults - 2
environment "config.generators do |g| \n g.stylesheets false \n end"

#create postgres DB for postgress.app - 3
run "psql -c 'CREATE DATABASE #{app_path}_development;'"
run "psql -c 'CREATE DATABASE #{app_path}_test;'"

#Initialize local Git repository and Initial Commit - 4
git :init
git add: "."
git commit: "-m 'Initial commit'" 
```

Enter fullscreen mode Exit fullscreen mode

创建新项目:
`rails new my-rails-project`

就是这样...它创建了我的数据库，没有自我类型的命令行开关。魔法。我能做的远不止这些，但这只是一个开始。