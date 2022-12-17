# Ruby 中的博客引擎

> 原文：<https://dev.to/defman/pisatelrb---a-blogging-engine-in-ruby>

我用 Ruby、Sinatra 和 GraphQL 构建了一个非常简单的博客引擎。我正在尝试创建一个轻量级的 Ghost 替代品，这样默认主题的外观就是 Ghost'y。

特点/优点:

*   你可以...写帖子。降价中。在一个漂亮的实时预览编辑器中(感谢那些开源他们酷项目的人。我使用的 markdown 编辑器称为 simple-markdown-editor)。
*   它使用 Vue.JS。
*   Sqlite/mysql/postgres 支持(Sequel 可以处理更多，但我不想为所有现有的数据库提供支持，所以我选择了最受欢迎的)。
*   Dockerized(使用 docker-compose)。
*   它有一个首次设置页面，您可以在其中配置数据库内容(主机、端口、数据库名称等)。

缺点:

*   是阿尔法狼。我还没有测试 postgres 支持。可能有 bug。
*   它不像 Ruby on Rails 那样使用很酷的框架(实际上我并不觉得这有什么不好，但是请记住，代码/项目结构可能看起来有点难看)。
*   因为看到上面的内容，所以对它做出贡献并不容易。

我在找:

*   想法！
*   Bug 报告！

[Github 页面](https://github.com/Defman21/pisatel.rb)。

演示即将开始。

由我用痛苦、泪水、鲜血和爱建造。