# 国家预防机制提示/技巧

> 原文：<https://dev.to/nickytonline/npm-tipstricks-10ea>

我几乎从不全局安装包，除非是像 yarn 或 yeoman 这样的东西。当您在 Node/JS 项目的根目录中时，如果您想在没有 npm 脚本的情况下运行类似 mocha 的东西，您应该执行`./node_modules/.bin/mocha`。

我所做的就是将`./node_modules/.bin`添加到我的 shell 的 profile/config 文件中的路径中，只要我在任何 Node/JS 项目中，我就可以运行例如`mocha`。

你有什么 npm 技巧/诀窍想分享吗？