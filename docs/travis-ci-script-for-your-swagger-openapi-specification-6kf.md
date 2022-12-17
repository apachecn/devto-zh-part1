# 用于您的 Swagger/OpenAPI 规范的 Travis CI 脚本

> 原文：<https://dev.to/hossain/travis-ci-script-for-your-swagger-openapi-specification-6kf>

最近，我和斯瓦格合作了一个项目。 [Swagger 2.0](http://swagger.io/) 已经成为开放标准，现在称为 [OpenAPI 规范](https://github.com/OAI/OpenAPI-Specification) *(OAS)* 。

我也喜欢**medium.com**，所以当我看到他们的 API 文档是纯文本手写的时候，我想把它转换成 OpenAPI 规范格式会是一个很好的练习。结果是 https://github.com/amardeshbd/medium-api-specification 的

现在，最近我开始为我最近的大部分项目添加 [**Travis**](https://travis-ci.org/) 持续集成(CI)脚本。所以，我想为这个 OAS 项目添加 CI 支持。

### 增加对 OpenAPI 规范的 Travis CI 构建支持

谷歌搜索这个特定的主题并没有带来太多的内容。我想保持脚本简单，使用由[swagger-validator-badge](https://github.com/swagger-api/validator-badge)web 服务*提供的现有验证器(注意:社区中有几十种不同语言的验证器——参见*[*http://swagger.io/open-source-integrations/*](http://swagger.io/open-source-integrations/)*)*。

以下是 CI 的关键要素:

* shUnit2 —用于在 shell 脚本中编写单元测试。这是在构建开始之前通过 Travis 构建配置*下载的。*