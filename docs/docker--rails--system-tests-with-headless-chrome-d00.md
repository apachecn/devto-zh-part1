# Docker + Rails +无头 Chrome 系统测试

> 原文：<https://dev.to/dstull/docker--rails--system-tests-with-headless-chrome-d00>

# 目标

展示了一个在带有 Ruby on Rails 的容器中进行的简单的无头系统测试设置。

可能有许多方法可以实现这个目标。然而，我的目标是连接到一个最终的 CI 系统，在这个系统中，可以通过构建 docker 映像和运行测试套件来验证 Rails 应用程序。

我将把它分成 3 个步骤:

*   用 CentOS 和 ruby 2.4 创建 docker 图像
*   为 Rails 和 headless chrome 测试创建 docker 映像
*   为无头 chrome 系统测试配置 Rails

# 用 CentOS 和 ruby 2.4 创建 docker 图像

为了完成这一部分，我创建了一个简单的项目，它为 Rails 应用程序提供了一个基础映像。我决定创建这个基础映像，因为我想坚持使用 CentOS，在创建时，我无法在 ruby 2.4 的 [Docker Hub](https://hub.docker.com/) 上找到 CentOS。

## Dockerfile

```
FROM centos:centos7

# Install the appropriate software
RUN yum -y update && yum clean all
RUN yum -y install \
            epel-release \
        which \
            && \
        yum clean all \
            && \
        :

# Install rvm, default ruby version and bundler.

RUN gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
RUN /bin/bash -l -c "curl -L get.rvm.io | bash -s stable"
RUN /bin/bash -l -c "echo 'source /etc/profile.d/rvm.sh' >> /etc/profile"
RUN /bin/bash -l -c "rvm install 2.4.2"
RUN /bin/bash -l -c "rvm cleanup all"
RUN /bin/bash -l -c "echo 'gem: --no-ri --no-rdoc' > .gemrc"
RUN /bin/bash -l -c "gem install bundler --no-ri --no-rdoc" 
```

然后我在 [Docker Hub](https://hub.docker.com/) 上创建了一个存储库，并针对 [Github repo](https://github.com/dstull/ruby_24) 建立了一个自动化构建。这允许它在更改被推送到 Github 时自动重建映像。

# 为 Rails 和无头 chrome 测试创建 docker 映像

既然 ruby 的基础映像已经在 [Docker Hub](https://hub.docker.com/r/hammer098/ruby_24/) 上构建并可用，我可以在我的示例 [Rails 应用程序](https://github.com/dstull/docker-rails)中使用它作为基础。

棘手的部分开始了:

我想把无头的 chrome 块放在将要投入生产的 Rails 应用程序映像之外。为了做到这一点，有几种方法可以实现:

1.  用我的应用程序并排设置一个容器，并使用预构建的 [Selenium 容器](http://blog.tomazy.com/2017/05/testing-rails-app-in-docker-containers.html)进行集成。我最初在一些 Docker 专业人士的帮助下尝试了这一点，但测试套件速度太慢了(非无头设置/不耐烦的开发人员/没有太努力地尝试诊断)。

2.  设置一个容器，将 chrome 与 Rails 应用程序一起安装。我最终选择了这条路，同时承认应用程序映像和测试应用程序映像并不完全相同。

## Rails 应用程序镜像设置

在一个[构建脚本](https://github.com/dstull/docker-rails/blob/master/sdlc/build)中，我执行了在运行时创建所需 Dockerfile 文件的步骤，并执行映像的构建。这使得特定的应用程序构建步骤对于这个映像和测试映像来说是相同的。请注意，docker 文件不应签入 repo，但在此应用程序中用于演示目的。

### 构建脚本(rails 镜像函数)

```
build_rails() {
  cat Dockerfile.prefix.in \
  sdlc/Dockerfile.in \
  > Dockerfile

  time docker-compose build \
  rails
} 
```

如上所述，我将 Ruby 基础部分( [Dockerfile.prefix.in](https://github.com/dstull/docker-rails/blob/master/Dockerfile.prefix.in) )与公共 Rails 部分( [sdlc/Dockerfile.in](https://github.com/dstull/docker-rails/blob/master/sdlc/Dockerfile.in) )连接起来，然后连接成最终的 Dockerfile，作为 docker-compose 构建的输入。在现实世界的设置中，Dockerfile 的“In”文件片段可能会大一点，并且有一个后缀，其中定义了变量来标记图像。

### [最终 Dockerfile](https://github.com/dstull/docker-rails/blob/master/Dockerfile)

```
FROM hammer098/ruby_24
# common rails build steps
WORKDIR /web

# Install rvm, default ruby version and bundler.
COPY .ruby-version /web/.ruby-version
COPY Gemfile /web/Gemfile
COPY Gemfile.lock /web/Gemfile.lock

RUN /bin/bash -l -c "bundle install"

COPY . /web 
```

## Chrome + Rails 应用镜像设置

在[构建脚本](https://github.com/dstull/docker-rails/blob/master/sdlc/build)中，执行了类似的步骤来在构建时为测试映像创建 docker 文件。

```
build_rails_test() {
  cat sdlc/Dockerfile.rails-test.in \
  sdlc/Dockerfile.in \
  > sdlc/Dockerfile.rails-test

  time docker-compose build \
  rails-test
} 
```

在这个函数中，执行的步骤与 Rails 应用程序构建中的步骤相似。测试映像特有的步骤包含在[SDLC/docker file . rails-test . in](https://github.com/dstull/docker-rails/blob/master/sdlc/Dockerfile.rails-test.in)中。然后添加公共 Rails 部分，并创建 sdlc/Dockerfile.rails-test 供 rails-test 映像使用。不要将 sdlc/Dockerfile.rails-test 添加到 repo 中，因为它是由该过程“动态”创建的。

### 最终 Dockerfile.rails-test

```
FROM hammer098/ruby_24

RUN yum -y install \
            chromedriver \
            chromium \
            gnu-free-sans-fonts \
            xorg-x11-server-Xvfb \
            && \
            yum clean all \
            && \
            :
# common rails build steps
WORKDIR /web

# Install rvm, default ruby version and bundler.
COPY .ruby-version /web/.ruby-version
COPY Gemfile /web/Gemfile
COPY Gemfile.lock /web/Gemfile.lock

RUN /bin/bash -l -c "bundle install"

COPY . /web 
```

# 配置 Rails 进行无头 chrome 系统测试

对于这一部分，我需要配置两部分:

## 轨道测试配置

在这一部分中，我们需要设置无头 chrome 驱动程序并启用特定的 chrome 选项。

### [应用 _ 系统 _ 测试 _ 案例. rb](https://github.com/dstull/docker-rails/blob/master/test/application_system_test_case.rb)

```
require 'test_helper'
require 'selenium-webdriver'
require 'capybara'

class ApplicationSystemTestCase < ActionDispatch::SystemTestCase

  Capybara.register_driver :headless_chrome do |app|

    # options explained https://peter.sh/experiments/chromium-command-line-switches/
    # no-sandbox
    #   because the user namespace is not enabled in the container by default
    # headless
    #   run w/o actually launching gui
    # disable-gpu
    #   Disables graphics processing unit(GPU) hardware acceleration
    # window-size
    #   sets default window size in case the smaller default size is not enough
    #   we do not want max either, so this is a good compromise

    capabilities = Selenium::WebDriver::Remote::Capabilities.chrome(
        chromeOptions: { args: %w[no-sandbox headless disable-gpu window-size=1400,1400] }
    )

    Capybara::Selenium::Driver.new(
        app,
        browser:              :chrome,
        desired_capabilities: capabilities
    )
  end
  driven_by :headless_chrome
end 
```

使用此配置的相应示例测试可在此处找到[。](https://github.com/dstull/docker-rails/tree/master/test/system)关于我为什么选择使用 Rails 系统测试而不是 rspec 的解释可以在[这里找到。](https://dev.to/dstull/my-journey-from-rspec-feature-tests-to-rails-system-tests)

## 用于测试执行的 Xvfb 安装和 Docker 入口点设置

在工作设置中，我们需要安装 [xvfb](https://www.x.org/archive/X11R7.6/doc/man/man1/Xvfb.1.xhtml) 以使测试正常运行。然而，我们也在使用一个旧的 chrome 版本(v59)。在这个示例设置中，我无法证明 xvfb 是必需的。这要么是因为 chrome 的较新版本(v62)，要么是因为测试设置不太复杂。

设置:

1.  安装 xvfb-run rpm，如上图所示。
2.  修改 docker-compose.yml 中的 entrypoint 指令，以包含 xvfb-run 和 rails 测试执行步骤。

### [docker-compose . yml](https://github.com/dstull/docker-rails/blob/master/docker-compose.yml)rails-测试运行定义

```
 rails-test-run:
    image: rails-test
    entrypoint:
      - bash
      - --login
      - xvfb-run
      - rails
      - test:system
      - RAILS_ENV=test 
```

上面我通过命令`docker-compose run --rm rails-test-run`告诉 docker 交互登录(bash - login)，运行 xvfb，执行 rails 系统测试，然后退出。RAILS_ENV 变量被设置为 test，这样 RAILS 在默认情况下不会首先查看开发数据库设置。

下面是一个利用[测试脚本](https://github.com/dstull/docker-rails/blob/master/sdlc/test)的测试运行的输出示例:

```
docker-rails$ sdlc/test
Starting rails-test, running and exiting

Run options: --seed 22514

# Running:

Puma starting in single mode...
* Version 3.10.0 (ruby 2.4.2-p198), codename: Russell's Teapot
* Min threads: 0, max threads: 1
* Environment: test
* Listening on tcp://0.0.0.0:35683
Use Ctrl-C to stop
...

Finished in 8.587443s, 0.3493 runs/s, 0.4658 assertions/s.
3 runs, 4 assertions, 0 failures, 0 errors, 0 skips 
```

# 结论

我发现这个设置是我们最终目标中的一个，即连接到一个 CI 系统。

如此处所示，对于用于生产的 Rails 应用程序来说，设置还远远没有完成。以下是我认为超出范围的一些内容，它们被排除在本文和[示例应用程序](https://github.com/dstull/docker-rails)之外:

1.  Nginx/Unicorn 安装和设置
2.  该容器以 root 用户身份运行，在我们的生产设置中，我们以非 root 用户身份运行。
3.  将容器设置为只读，并在 docker 文件中为 Rails 需要写入的某些区域添加卷指令。在构建时更改这些文件和目录的权限/所有权。
4.  用于测试/开发的 Mysql 容器。
5.  环境变量突破。
6.  处理编译的资产和 js 运行时环境。...更多的