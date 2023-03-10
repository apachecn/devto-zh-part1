# 从 RSpec 测试中自动生成 API 文档

> 原文：<https://dev.to/melcha/automatically-generate-api-documentation-from-rspec-tests>

手动文档维护既耗时又容易出错，没有人喜欢这样做。由于人为错误，东西经常被忽略、遗忘或意外替换。大多数开发人员都参与了不必要的调试会话，因为 API 被更改了，但是文档没有正确更新。

我们已经构建了一个 gem，用于从 Rails - [Dox](https://github.com/infinum/dox) 的 RSpec 测试中生成文档。它使用来自测试示例的请求/响应信息，您只需要使用 Dox DSL 编写一些元数据。它生成 [API 蓝图](https://apiblueprint.org/)格式的降价。要查看它的运行情况，请查看[演示应用](https://github.com/infinum/dox-demo)。

将它插入到 Rails/RSpec API 应用程序中应该不会花费您太多时间，您就可以开始享受额外的时间了。

*阅读全文[翻船八博客](https://infinum.co/the-capsized-eight/generate-api-documentation-from-rspec-examples-with-dox)T3】。*