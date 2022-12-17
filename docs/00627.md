# 用 Circle CI 2.0 集成测试 Phoenix 应用程序

> 原文：<https://dev.to/vinhnglx/integration-testing-phoenix-application-with-circle-ci-20-50md>

## 简介

编写测试用例是软件开发人员应该做的事情。编写测试以确保项目源代码的质量。

用 Ruby on Rails 框架开发应用时，经常会用到 Capybara。

那么，如果我们用 Elixir/Phoenix 开发应用程序，我们应该使用哪个库呢？Hound 是编写集成测试和浏览器自动化的灵丹妙药库。Hound 不是一个新的库，我相信你们可以在网上找到很多教程，展示如何在 Elixir/Phoenix 应用程序中使用 Hound。

在这篇文章中，我将展示如何在 Circle CI 2.0 for Phoenix 应用程序上运行由 Hound 编写的集成测试。

## 圆 CI 2.0

我们所知道的是，当在任何 CI 环境中运行集成测试时，我们需要配置测试可以在无头浏览器模式下运行，这将在 CI 执行测试之前设置。

有各种各样的工具可以帮助进行无头测试，比如 PhantomJS，Headless Chrome，Selenium。在 CI 环境中设置这些工具也有很多方法。例如:在 Travis CI 中，我们可以使用 before_script 块运行 shell 脚本来安装 headless 测试工具。在 Circle CI 2.0 中，他们支持开箱即用的浏览器测试。听起来很酷，对吧？

## 入门

### 指定正确的图像

为了在 Circle CI 2.0 上启用 headless 测试，主映像预装了当前稳定版本的 Chrome(这由-browsers 后缀指定)。这意味着，你的。circleci/config.yml 文件需要使用带有-browsers 后缀的图像。

```
version: 2
jobs:
  build:
    working_directory: ~/my_application
    docker:
      - image: circleci/elixir:1.5-browsers 
```

### 安装硒驱动程序

猎犬默认使用 Selenium 驱动，所以这一步，我准备在 Circle CI 2.0 上安装 Selenium:

```
steps:
  - checkout
  - run:
      name: Download Selenium
      command: |
        curl -O http://selenium-release.storage.googleapis.com/3.5/selenium-server-standalone-3.5.3.jar
  - run:
      name: Start Selenium
      command: |
        java -jar selenium-server-standalone-3.5.3.jar -log test-reports/selenium.log
      background: true 
```

### 运行测试

最后一步是将所有配置放在一起:

```
version: 2
jobs:
  build:
    working_directory: ~/my_application
    docker:
      - image: circleci/elixir:1.5-browsers
        environment:
          TEST_DATABASE_URL: postgresql://developer@localhost/my_application_test?sslmode=disable
      - image: circleci/postgres:9.5-alpine
        environment:
          POSTGRES_USER: developer
          POSTGRES_DB: my_application_test
          POSTGRES_PASSWORD: ""

    steps:
      - checkout
      - run:
          name: Download Selenium
          command: |
            curl -O http://selenium-release.storage.googleapis.com/3.5/selenium-server-standalone-3.5.3.jar
      - run:
          name: Start Selenium
          command: |
            java -jar selenium-server-standalone-3.5.3.jar -log test-reports/selenium.log
          background: true
      - run:
          name: Install System Dependencies
          command: sudo apt-get update -qq && sudo apt-get install -y build-essential postgresql postgresql-client libpq-dev rake nodejs unzip
      - run: mix local.hex --force
      - run: mix local.rebar
      - run: mix deps.get
      - run: MIX_ENV=test mix ecto.create
      - run: MIX_ENV=test mix ecto.load -d apps/my_application/priv/repo/structure.sql
      - run: mix test 
```

## 结论

让凤凰集成测试和 Circle CI 和谐工作并不难。希望你喜欢，并随时让我知道你的评论。谢谢！

来源:[https://the first April . com/2018/01/18/Integration-testing-Phoenix-application-with-Circle-CI-2-0/](https://thefirstapril.com/2018/01/18/Integration-testing-Phoenix-application-with-Circle-CI-2-0/)