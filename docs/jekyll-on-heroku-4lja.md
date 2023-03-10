# 杰基尔在赫罗库

> 原文：<https://dev.to/heroku/jekyll-on-heroku-4lja>

[Jekyll](https://jekyllrb.com/) ，用 Ruby 编写的静态网站生成器，由 GitHub 推广，是在 Heroku 上运行的一个很好的候选。最初是为了在 GitHub 页面上运行而构建的，在 Heroku 上运行 Jekyll 允许您利用 Jekyll 强大的插件系统做更多的事情，而不仅仅是将 Markdown 转换为 HTML。在我的博客上，我有插件来下载我的 [Goodreads](https://www.goodreads.com/) 当前和最近阅读的书籍，并为帖子生成 [Open Graph](http://ogp.me/) 图像。也就是说，不使用`jekyll serve`来完成繁重的工作，在 Heroku 上运行起来并不容易。`jekyll serve`使用 Ruby 的[内置单线程 web 服务器 WEBrick](https://schneems.com/2017/08/01/is-webrick-webscale/) ，但是公共网站应该使用更适合生产的 web 服务器，比如 nginx。

我们从头开始。你需要安装 Ruby 和 Bundler。

我喜欢把 [ruby-install](https://github.com/postmodern/ruby-install) 和 [chruby](https://github.com/postmodern/chruby) 作为我的 ruby 安装器和切换器。

这是安装最新版本 ruby-install 的平台无关方式。如果你愿意，你可以在 ruby-install 的自述文件中找到关于 OS X 和 Windows 的说明。

> 下面的命令使用了本文发表时最新版本的 ruby-install 和 chruby。如果可能的话，您可能希望使用更新的版本。

```
$ wget -O ruby-install-0.6.1.tar.gz https://github.com/postmodern/ruby-install/archive/v0.6.1.tar.gz
$ tar -xzvf ruby-install-0.6.1.tar.gz
$ cd ruby-install-0.6.1/
$ sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

您还需要 chruby，您可以使用 chruby 的自述文件中的说明以一种更加特定于平台的方式安装它。

```
$ wget -O chruby-0.3.9.tar.gz https://github.com/postmodern/chruby/archive/v0.3.9.tar.gz
$ tar -xzvf chruby-0.3.9.tar.gz
$ cd chruby-0.3.9/
$ sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

安装最新的 Ruby 并使用:

```
$ ruby-install ruby
$ chruby ruby 
```

Enter fullscreen mode Exit fullscreen mode

你需要邦德勒和杰基尔宝石

```
$ gem install bundler jekyll
Fetching: bundler-1.9.9.gem (100%)
Successfully installed bundler-1.9.9
Fetching: jekyll-3.6.2.gem (100%)
Successfully installed jekyll-3.6.2
2 gems installed 
```

Enter fullscreen mode Exit fullscreen mode

生成一个新的 jekyll 站点非常简单，您可以免费获得一个站点模板，就像启动一个新的 Rails 应用程序一样。我们将在 Git 中保存我们的工作:

```
$ jekyll new jekyll-on-heroku
Ignoring nokogiri-1.8.1 because its extensions are not built. Try: gem pristine nokogiri --version 1.8.1
Running bundle install in /Users/caleb.thompson/code/jekyll-on-heroku/_rundoc/tmp/jekyll-on-heroku...
  …
New jekyll site installed in /Users/caleb.thompson/code/jekyll-on-heroku/_rundoc/tmp/jekyll-on-heroku.
$ cd jekyll-on-heroku
$ git init
Initialized empty Git repository in /Users/caleb.thompson/code/jekyll-on-heroku/_rundoc/tmp/jekyll-on-heroku/.git/
$ git add .
$ git commit -m "jekyll new jekyll-on-heroku"
[master (root-commit) ea8cd80] jekyll new jekyll-on-heroku
 8 files changed, 206 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 404.html
 create mode 100644 Gemfile
 create mode 100644 Gemfile.lock
 create mode 100644 _config.yml
 create mode 100644 _posts/2017-12-07-welcome-to-jekyll.markdown
 create mode 100644 about.md
 create mode 100644 index.md 
```

Enter fullscreen mode Exit fullscreen mode

我们有自己的 Jekyll 网站(如果你愿意，继续尝试`jekyll serve`)，但我们需要一个 Heroku 应用程序来部署。在这一步之前，你需要[一个账号](https://signup.heroku.com/)和[安装好的命令行界面](https://devcenter.heroku.com/articles/heroku-cli)。我们将使用官方的 Ruby buildpack 和 Heroku(非官方)的静态 buildpack 来分别完成站点生成和静态页面服务:

```
$ heroku create
Creating app... done, radiant-bastion-50307
https://radiant-bastion-50307.herokuapp.com/ | https://git.heroku.com/radiant-bastion-50307.git
$ heroku buildpacks:add heroku/ruby
Buildpack added. Next release on radiant-bastion-50307 will use heroku/ruby.
Run git push heroku master to create a new release using this buildpack.
$ heroku buildpacks:add https://github.com/heroku/heroku-buildpack-static
Buildpack added. Next release on radiant-bastion-50307 will use:
  1\. heroku/ruby
  2\. https://github.com/heroku/heroku-buildpack-static
Run git push heroku master to create a new release using these buildpacks. 
```

Enter fullscreen mode Exit fullscreen mode

Heroku 的构建包需要特定的文件才能正常工作。我们已经有了用于 Ruby buildpack 的`Gemfile`，但是我们还需要一个用于静态 buildpack 的`static.json`文件。我在`static.json`中添加了一些我最喜欢的选项，我们需要使用`_site/`作为我们的根，因为默认情况下 Jekyll 在那里构建它的静态内容。

在文件`static.json`中写入:

```
{  "clean_urls":  true,  "https_only":  true,  "root":  "_site/"  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们再次保存我们的进度。

```
$ git add static.json
$ git commit -m "Add static.json with reasonable defaults"
[master 6e1dea7] Add static.json with reasonable defaults
 1 file changed, 5 insertions(+)
 create mode 100644 static.json 
```

Enter fullscreen mode Exit fullscreen mode

Ruby buildpack 在可用时运行`rake assets:precompile`。这是受 Rails 模式的启发，但它适用于您需要的任何资产。我们将通过运行`jekyll build`将它用于我们的整个站点。

在文件`Rakefile`中写入:

```
task "assets:precompile" do
  exec("jekyll build")
end 
```

Enter fullscreen mode Exit fullscreen mode

你首先需要 Rake 来运行这个，我们可以通过指定一个版本来省去将来的麻烦。

在`Gemfile`的末尾加上:

```
gem "rake"
ruby "2.4.2" 
```

Enter fullscreen mode Exit fullscreen mode

并运行`bundle`安装耙子。

```
$ bundle
  …
Bundle complete! 5 Gemfile dependencies, 24 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed. 
```

Enter fullscreen mode Exit fullscreen mode

我们需要为 Rake 任务添加更改和新的依赖项:

```
$ git add Rakefile Gemfile Gemfile.lock
$ git commit -m "Add Rake task to build Jekyll site"
[master 94f664b] Add Rake task to build Jekyll site
 3 files changed, 10 insertions(+)
 create mode 100644 Rakefile 
```

Enter fullscreen mode Exit fullscreen mode

终于可以把 app 推送给 Heroku 了！

```
$ git push heroku master
warning: not sending a push certificate since the receiving end does not support --signed push
remote: Compressing source files... done.
remote: Building source:
remote:
remote: -----> Ruby app detected
remote: -----> Compiling Ruby
remote: -----> Using Ruby version: ruby-2.4.2
remote: -----> Installing dependencies using bundler 1.15.2
remote: Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
  …
remote: -----> Detecting rake tasks
remote: -----> Precompiling assets
remote: Running: rake assets:precompile
remote: Configuration file: /tmp/build_c29844d07a39f5ce14fd64c34fc17680/_config.yml
remote: Source: /tmp/build_c29844d07a39f5ce14fd64c34fc17680
remote: Destination: /tmp/build_c29844d07a39f5ce14fd64c34fc17680/_site
remote: Incremental build: disabled. Enable with --incremental
remote: Generating...
remote: done in 0.901 seconds.
remote: Auto-regeneration: disabled. Use --watch to enable.
remote: Asset precompilation completed (1.49s)
remote:
remote: ###### WARNING:
remote: No Procfile detected, using the default web server.
remote: We recommend explicitly declaring how to boot your server process via a Procfile.
remote: https://devcenter.heroku.com/articles/ruby-default-web-server
remote:
remote: -----> Static HTML app detected
remote: % Total % Received % Xferd Average Speed Time Time Time Current
remote: 100 838k 100 838k 0 0 5598k 0 --:--:-- --:--:-- --:--:-- 5628k
remote: -----> Installed directory to /app/bin
remote: -----> Discovering process types
remote: Procfile declares types -> (none)
remote: Default types for buildpack -> web
remote:
remote: -----> Compressing...
remote: Done: 26.4M
remote: -----> Launching...
remote: Released v3
remote: https://radiant-bastion-50307.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To https://git.heroku.com/radiant-bastion-50307.git
 * [new branch] master -> master 
```

Enter fullscreen mode Exit fullscreen mode

打开最后的网址，在 Heroku 上享受你的新 Jekyll 应用！