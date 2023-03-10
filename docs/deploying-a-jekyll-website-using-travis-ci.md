# 使用 Travis CI 部署 Jekyll 网站

> 原文：<https://dev.to/ajaykarwal/deploying-a-jekyll-website-using-travis-ci>

继我关于[从 CMS 迁移到 Jekyll](https://dev.to/ajaykarwal/switching-from-a-cms-to-jekyll) 的帖子之后，下一个需要解决的棘手问题是站点的构建和部署。

Jekyll 在将你的网站编译成一个整洁的`_site`文件夹方面做得很好，然后你可以将它 FTP 到你的服务器上，然而这确实意味着你需要手边有一个方便的 FTP 客户端。更好的解决方案是自动化这个过程，看到我们的源代码已经存储在 GitHub 上，我们已经完成了一半。

## 认识特拉维斯

Travis CI 是一个免费的持续集成服务，用于测试和部署你的开源 Github 项目(私有 GitHub 项目可以选择付费)。

向您的项目添加一个配置文件，将 Travis CI 指向您的 GitHub repo，当您推送您的代码或合并一个 pull 请求时，Travis CI 会在 VM 中构建您的 Jekyll 站点，并根据配置中的设置部署您的代码。

那么，我们开始吧。

## 创建 Travis CI 配置文件

在 Jekyll 项目的根目录下创建一个新文件，并将其命名为`.travis.yml`。由于这是一个“点文件”,它可能隐藏在 Finder 中，但应该出现在您的文本编辑器中。该文件的内容将告诉 Travis CI 如何构建和部署您的站点。这是我的文件内容。

```
language: ruby
rvm:
  - 2.3.1

install:
  - bundle install
  - gem install jekyll
  - gem install jekyll-sitemap
  - gem install emoji_for_jekyll

branches:
  only:
    - master

env:
  global:
    - JEKYLL_ENV=production

notifications:
  email:
    recipients:
      - ajaykarwal@gmail.com
    on_success: always
    on_failure: always

script:
  - chmod +x _scripts/build.sh
  - _scripts/build.sh

after_success:
  - chmod +x _scripts/deploy.sh
  - _scripts/deploy.sh

sudo: false
addons:
  apt:
    packages:
      - ncftp 
```

Enter fullscreen mode Exit fullscreen mode

让我们一步一步地分解它

### 定义构建环境和依赖关系

```
language: ruby
rvm:
  - 2.3.1

install:
  - bundle install
  - gem install jekyll
  - gem install jekyll-sitemap
  - gem install emoji_for_jekyll

branches:
  only:
    - master

env:
  global:
    - JEKYLL_ENV=production 
```

Enter fullscreen mode Exit fullscreen mode

这一部分告诉 Travis CI 构建需要 Ruby，并将版本设置为 2.3.1。它还列出了所有 Gem 依赖项。“jekyll-sitemap”和“emoji_for_jekyll”是我的项目特有的。

“分支”部分允许您控制想要在存储库中构建哪个分支。在我的例子中，我只是构建主分支，但是这个部分也可以用来设置一个登台环境。

将`JEKYLL_ENV`设置为生产意味着我们可以测试这个环境变量，同时进行本地测试来[忽略像谷歌分析](https://github.com/ajaykarwal/portfolio/blob/master/_includes/head.html#L26-L28)这样的事情。

### 建设和部署站点

```
script:
  - chmod +x _scripts/build.sh
  - _scripts/build.sh

after_success:
  - chmod +x _scripts/deploy.sh
  - _scripts/deploy.sh

sudo: false
addons:
  apt:
    packages:
      - ncftp 
```

Enter fullscreen mode Exit fullscreen mode

这个部分告诉 Travis CI 找到并执行位于`_scripts/build.sh`的文件，并在成功时执行位于`_scripts/deploy.sh`的文件。

addons 部分告诉 Travis CI 还要安装一个名为 ncftp 的 FTP 客户端。这将用于部署您的网站。

在根目录下创建一个名为`_scripts`的文件夹，并在其中创建一个构建和部署 shell 脚本。

#### build.sh

```
#!/bin/bash

bundle exec jekyll build --config _config.yml 
```

Enter fullscreen mode Exit fullscreen mode

构建脚本本质上与您在本地构建站点时在终端中运行的命令相同，只是将`_config.yml`文件定义为站点的配置文件。

#### deploy.sh

```
#!/bin/bash

if  [[ $TRAVIS_PULL_REQUEST = "false" ]]
then ncftp -u "$USERNAME" -p "$PASSWORD" "$HOST"<<EOF rm -rf site/wwwroot
    mkdir site/wwwroot
    quit EOF

    cd _site || exit ncftpput -R -v -u "$USERNAME" -p "$PASSWORD" "$HOST" /site/wwwroot .
fi 
```

Enter fullscreen mode Exit fullscreen mode

部署脚本执行 3 个主要任务

1.  使用您在 [Travis CI 设置](#setting-up-travis-ci)中设置的`$USERNAME`、`$PASSWORD`和`$HOST`变量登录您的 FTP 账户。
2.  删除`site/wwwroot`目录并重新创建一个空目录
3.  将`_site`文件夹的内容复制到`/site/wwwroot`

这个剧本是由杰米·麦咭写的，他在整个过程中提供了一些非常有用的指导。

## 设置特拉维斯 CI

为了让部署脚本工作，您需要在 Travis CI 中为 GitHub 存储库配置环境变量。

*   去你的[特拉维斯个人资料](https://travis-ci.org/profile/)
*   找到您的 Jekyll 存储库，打开 Travis CI 并单击齿轮图标
*   为您的 FTP 主机设置环境变量。

[![Travis CI Environment Settings](img/a0aea1add3f968d490055ef43ed66fee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YgpExHws--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6lbk8gsv8qakckzisg8w.png)

**注意:开源项目的构建日志是公开可见的，所以记得关闭“在构建日志中显示值”选项。**

## 将所有的事情自动化

现在一切都已经设置和配置好了，接下来就是将代码推送到 GitHub 主分支了。Travis CI 将监视您的存储库的变化，并自动触发构建。只有当构建成功时，Travis CI 才会将您的站点部署到您的 FTP 主机上。

通过拉请求工作流，Travis CI 将在 PR 上运行构建，只有当它成功时，它才会允许分支合并到主服务器中。

文件`.travis.yml`中的通知部分可用于管理谁接收构建状态电子邮件通知。

```
notifications:
  email:
    recipients:
      - ajaykarwal@gmail.com
    on_success: always
    on_failure: always 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

使用 Travis CI 部署您的 Jekyll 网站简单、快速且安全。Pull Request 工作流非常适合在开源项目上进行协作，或者在您准备好的时候通过合并分支来简单地安排您自己的内容。

所有构建过程都由 Travis CI 处理，这意味着您可以从任何地方提交对存储库的更改，测试和验证您的代码，然后合并以实时推送您的内容。我用这种方法通过 GitHub 网站用我的手机更新网站。

要了解更多使用 Travis CI 来满足您需求的方法，请查看[文档](https://docs.travis-ci.com/)。