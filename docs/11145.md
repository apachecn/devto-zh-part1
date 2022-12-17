# webpack 的可用开发工具

> 原文：<https://dev.to/adamkdean/available-devtools-for-webpack-1afi>

万一 webpack 文档丢失了这个，这里有一个可用的 devtools 列表。选择开发人员工具来增强调试。

`eval` -每个模块用`eval`和`//@ sourceURL`执行。

`source-map` -发出 SourceMap。参见`output.sourceMapFilename`。

`hidden-source-map`——与`source-map`相同，但是没有给 bundle 添加引用注释。

`inline-source-map`-source map 作为 DataUrl 添加到 JavaScript 文件中。

`eval-source-map` -每个模块用`eval`执行，一个 SourceMap 作为 DataUrl 添加到`eval`。

`cheap-source-map` -一个没有列映射的 SourceMap。不使用来自加载器的源地图。

`cheap-module-source-map` -没有列映射的 SourceMap。来自加载器的源映射被简化为每行一个映射。

前缀`@`、`#`或`#@`将强制执行一种实用程序风格。(默认为`#`，推荐)

组合是可能的。`hidden`、`inline`、`eval`和 pragma 风格具有排他性。

即`cheap-module-inline-source-map`、`cheap-eval-source-map`、`#@source-map`