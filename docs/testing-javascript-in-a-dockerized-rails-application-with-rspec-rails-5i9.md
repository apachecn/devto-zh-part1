# 用 rspec-rails 在 dockerized rails 应用程序中测试 javascript

> 原文：<https://dev.to/aptituz/testing-javascript-in-a-dockerized-rails-application-with-rspec-rails-5i9>

前几天，我想使用 rspec-rails 在(dockerized) rails 应用程序中添加对 javascript 功能测试的支持。

由于 rails 5.1 包含了系统测试的细节，比如对失败的测试自动截屏，我希望有一种方法可以从这个
特性中获益，而不需要改变到不同的测试框架。幸运的是，rspec-rails 的作者最近才增加了对所谓系统规范的支持。到目前为止没有太多的文档(但是在相应的错误报告 [#1838](https://github.com/rspec/rspec-rails/pull/1813) 中有很多有用的信息，一个名叫托马斯·沃波尔([@特沃波尔](https://github.com/twalpole))的友好的家伙正在帮助回答那个问题)。

让事情变得稍微复杂一点:所讨论的应用程序通常在 docker 容器中运行，因此应用程序的测试也在 docker 容器中运行。我不想改变这一点，所以这里是我花了这个运行。

## 概述

让我们来看看我们到底想要实现什么:从技术的角度来看，我们将在一个容器(姑且称之为:web)中拥有被测应用程序(AUT)和测试，并且我们将需要另一个运行支持 javascript 的浏览器的容器(姑且称之为:chrome)。

因此，我们需要测试来驱动一个远程运行的浏览器(至少在 docker 环境中运行时),该浏览器需要在一个不同于通常的地址下访问应用程序。即一个可由`chrome`容器到达的地址，因为默认情况下(正当地)假定它不能通过`127.0.0.1`到达。

如果我们希望管理员身份验证存根起作用(正如我们所做的那样，因为我们的应用程序使用了 Devise)以及事务性固定装置(例如，rails 在没有 database_cleaner gem 的情况下处理测试之间的数据库清理)，我们还需要确保应用程序服务器是由测试启动的，并且测试实际上是在该服务器上运行的。否则我们可能会遇到问题。

## 准备好集装箱

假设您已经有了一个容器设置(并且像我们一样使用 docker-compose ),那么 docker 前端没有太多变化。基本上，您需要添加一个名为`chrome`的新服务，将它指向一个适当的图像，并在您现有的`web`容器中添加一个指向它的链接。

我决定对浏览器部分使用 standalone-chrome，为此有 selenium 项目提供的 docker 图像(他们也有其他浏览器的图像)。值得称赞。

```
...
services:
  chrome:
    image: selenium/standalone-chrome
  …
  web:
  …
    links:
      - chrome 
```

Enter fullscreen mode Exit fullscreen mode

该链接确保 chrome 实例在我们运行测试之前可用，并且`web`-容器能够解析该容器的名称。不幸的是，反之则不然，所以我们需要在测试代码中使用一些魔法来找出`web`容器的 ip 地址。稍后会详细介绍。

除此之外，您可能需要配置一个卷来访问截图，这些截图保存在应用程序目录中的`tmp/screenshots`
中。

## 为运行系统测试准备应用程序

在应用程序方面还有更多的事情要做。步骤大致是:

1.  添加必要的依赖/版本约束
2.  注册远程 chrome 的驱动程序
3.  将 capybara 配置为使用合适的主机进行测试(以及您配置的驱动程序)
4.  用`type: :system`和`js: true`添加实际测试

让我们带他们走一遍。

### 添加必要的依赖

我们需要的是以下内容:

*   rspec-rails 版本> = 3.7.1
*   rails 本身大于 5.1.4(在撰写本文时尚未发布)
*   水豚和水豚硒

所需的功能已经是 3.7.0 的一部分，但这个版本是我使用的版本，它包含一个 bugfix，可能相关，也可能不相关。

关于 rails 版本的一个评论:为了让测试正常工作，让 puma 使用某些设置是可行的。在 rails 5.1.4(在撰写本文时发布的版本)中，使用了来自`config/puma.rb`的设置，这很可能与必需的[设置](https://github.com/rspec/rspec-rails/pull/1813#issuecomment-337843391)相冲突。您可以自己确保这些设置，或者使用分支`5-1-stable`中的 rails，该分支包含[和](https://github.com/rails/rails/commit/87e085dacc3cffe53591b76550dd263fb89aff3f)变更。我决定选择后者，并将我的`Gemfile`绑定到当前提交。

### 为远程 chrome 注册驱动程序

要注册所需的驱动程序，您必须在您的`rails_helper.rb` :
中添加一些行

```
if ENV['DOCKER']
  selenium_url =
      "http://chrome:4444/wd/hub"

  Capybara.register_driver :selenium_remote do |app|
    Capybara::Selenium::Driver.new(app,
        {:url =&gt; selenium_url, :browser =&gt; :remote, desired_capabilities: :chrome})
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

注意，如果设置了环境变量`DOCKER`，我有条件地添加了这些行(因为我仍然希望能够通过 chromedriver 使用本地 chrome)。*我们*在我们的`Dockerfile`中定义了那个环境变量，因此你可能需要根据你的情况修改它。

还要注意 selenium_url 是硬编码的。你完全可以采取不同的方法，例如使用外部指定的 SELENIUM_URL，但最终的要求是驱动程序需要知道 chrome 实例正在主机`chrome`，端口 4444(容器默认)上运行。

### 配置水豚使用合适的主机和驱动程序

下一步是确保需要 javascript 的系统测试实际上在给定的驱动程序下运行，并使用正确的主机。为了实现这一点，我们需要在相应的测试中添加一个 before-hook...或者我们可以通过修改`rails_helper.rb`中的 rspec-configuration 来相应地配置 rspec，使其总是包含这样一个钩子，如下所示:

```
RSpec.configure do |config|
  ...
  config.before(:each, type: :system, js: true) do
    if ENV['DOCKER']
      driven_by :selenium_remote
      ip = Socket.ip_address_list.detect{|addr| addr.ipv4_private? }.ip_address
      host! "http://#{ip}:#{Capybara.server_port}"
    else
      driven_by :headless_chrome
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

注意带有 ip 地址的部分:它试图为`web`-容器(运行测试的容器)找到一个 IPv4 私有地址，以确保`chrome`-容器使用这个地址来访问应用程序。这里的`Capybara.server_port`很重要，因为它将*对应于测试*发起的 puma 实例。

这种启发式方法(第一个 ipv4 私有地址)目前对我们有效，但可能对你无效。这基本上是我无法在 chrome 容器中解决`web`问题的一个变通办法——这在 docker 端可能是可以解决的，但是我懒得进一步研究这个问题。

如果你改变了它:只要确保`host!`方法使用了一个指向`web`容器的地址的 URI，该地址可以到达`chrome`容器。

### [T3 用`type: :system`和`js: true`定义测试](#define-tests-with-raw-type-system-endraw-and-raw-js-true-endraw-)

最后但同样重要的是，您需要所需类型的实际测试，无论有没有`js: true`。这可以通过创建如下测试文件来实现:

```
RSpec.feature "Foobar", type: :system, js: true do 
```

Enter fullscreen mode Exit fullscreen mode

因为新的 rspec 风格的系统测试是基于以前的特性规范，所以剩下的测试就像特性规范的[描述的一样。](https://github.com/rspec/rspec-rails#feature-specs)

## 运行测试

要运行测试，应该使用如下命令行:

```
docker-compose run web rspec 
```

Enter fullscreen mode Exit fullscreen mode

对 chrome 进行测试不会引起太大的反响，除非有什么东西失败了。在这种情况下，你会看到一条消息，告诉你截图放在哪里。

## 故障排除

下面我补充一些我在配置过程中发现的问题的提示:

### 测试失败，截图显示登录画面

在这种情况下，puma 可能配置错误，或者您没有使用事务性夹具。请参阅上面关于要使用的 rails 版本的提示，其中还包括一些有用解释的链接。

注意，rspec-rails 默认情况下不输出 puma 启动输出，因为它扰乱了测试。出于调试的目的，在您的测试中添加下面一行代码可能会有所帮助:

```
ActionDispatch::SystemTesting::Server.silence_puma = false 
```

Enter fullscreen mode Exit fullscreen mode

### 错误信息:“无法找到 chromedriver”..."

这表明您的驱动程序没有正确配置，因为系统测试的默认设置是`driven_by` selenium，它试图生成一个自己的 chrome 实例，适合非 dockerized 测试。

检查您的测试是否被标记为 js: true(如果您遵循了上面的说明),以及您是否正确地将 before-hook 添加到了 rspec-configuration 中。

### 与录像机相撞

如果你碰巧有使用 [vcr gem](https://github.com/vcr/vcr) 的测试，你可能会看到它抱怨不知道如何处理驱动程序和 chrome 实例之间的请求。您可以通过告诉 VCR 忽略这些请求来解决这个问题，在您配置 VCR 的地方添加一行代码:

```
VCR.configure do |config|
  # required so we don't collide with capybara tests
  config.ignore_hosts 'chrome'
... 
```

Enter fullscreen mode Exit fullscreen mode