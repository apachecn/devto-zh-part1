# 如何在 CI 中使用 Google PageSpeed 测试

> 原文：<https://dev.to/tomoyukikashiro/how-to-use-google-pagespeed-test-in-ci-4fkp>

本帖最初发表于[如何在 CI](https://blog.tomoyukikashiro.me/post/how-to-use-google-pagespeed-test-in-ci) 中使用 Google PageSpeed 测试。

## 动机

你知道网站性能对搜索引擎优化，转换很重要，但很难找到如何不断测试它。(对我来说很难……)。因此，我将解释如何使用 [Google PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/) 在 CI 中设置性能测试。

## 概述

我将在 Travis CI 和 heroku 中使用 Google PageSpeed Insights 解释连续性能测试设置。我想测试的应用程序是由[鹈鹕](https://blog.getpelican.com/)生成的[这个博客](https://blog.tomoyukikashiro.me)，所以该应用程序是完全静态的网站(不需要数据库和服务器端程序)。

*   Heroku 应用程序
*   Travis CI 与 github 存储库连接

基本流程如下。

## - 1 在 github 库中制作 PR

2 在 Travis CI 中启动测试流程

*   1 安装设备
*   2 构建应用程序
*   3 将构建的应用程序推送到 heroku
*   4 运行 Google Page Insight 测试

## 准备英雄

第一步是设置 heroku 来测试应用程序。

```
$ mkdir ${app_name}
$ cd ${app_name}
$ git init
$ heroku apps:create ${app_name} 
```

这篇文章可以作为在 heroku 上制作静态服务器的很好参考！

## 编辑 CI 设置

第二步是设置 travis CI。您可以看到存储库的 travis CI 设置，如下所示。

> [https://Travis-ci . org/$ {帐户名称}/$ {存储库名称}/设置](https://travis-ci.org/%24%7Baccount_name%7D/%24%7Brepository_name%7D/settings)

您需要在`Environment Variables`部分进行这些设置。

*   HEROKU_API_KEY
*   测试站点远程
*   TEST _ SITE _ URL![travis settings screen](img/0236f37d89f05bd8e94c2aad77b60285.png)# # # HEROKU _ API _ KEY

Heroku 应用程序是 git 库，它是私有的，所以要在 travis 服务器中克隆它，你需要这个令牌。你可以通过这个命令看到它。

`bash
$ heroku auth:token`

### 测试 _ 站点 _ GIT _ 远程

heroku 应用程序 git url。git 协议比 https 协议更好，不需要每次都询问 id/密码。

### 测试 _ 网站 _ 网址

要测试的 heroku 应用程序 url。

## 编辑 CI 任务

基本`travis.yml`到了。您需要为您的应用程序
替换`${your install process here}`和`${your build process here}`

```
sudo: required
language: node_js
node_js:
  - "7"
install:
  - ${your install process here}
  - npm install
  - scripts/setup_heroku.sh
  - git clone $TEST_SITE_GIT_REMOTE output
before_script:
  - ${your build process here}
  - cd output && git add --ignore-removal . && git commit -m 'deploy for test' && git push origin master -f && cd -
script:
  - npm test $TEST_SITE_URL
after_script:
  - heroku keys:clear 
```

### setup_heroku.sh

[setup_heroku.sh](https://github.com/tomoyukikashiro/blog.tomoyukikashiro.me/blob/master/scripts/setup_heroku.sh)

该脚本将`ssh private key`添加到 heroku 以克隆存储库 thu heroku CLI。

我使用 nodejs heroku 命令，heroku 不推荐使用这个命令，因为我不能安装其他的安装方法…如果你有什么想法，请告诉我！

### npm 测试$TEST_SITE_URL

```
"devDependencies": {
    "psi": "^3.0.0"
  }, 
  "scripts": {
    "test": "npm run test_psi",
    "test_psi": "psi --strategy mobile"
  }, 
```

[psi](https://github.com/addyosmani/psi) 是做 Google pageSpeed Insights 的 npm 模块。

## 引用

你可以在这里查看例子 [![https://github.com/tomoyukikashiro/blog.tomoyukikashiro.me](img/682d2cc33ec71b818247b816be149dd8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NwgGmBLZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/tomoyukikashiro/blog.tomoyukikashiro.me)

如果你有问题，请告诉我！！