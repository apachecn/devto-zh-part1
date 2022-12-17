# 保持文档绿色

> 原文：<https://dev.to/burdettelamar/keeping-the-documentation-green-6gh>

每个人都喜欢文档中的好例子。通常，在软件世界中，例子是*代码*。

很公平。

但是这个示例代码真的有效吗？如果它在过去的某个时候确实有效，那么它现在还有效吗？

唯一确定的方法就是运行它！

在我的 GitHub 项目 RubyTest 中，我正在对项目的一部分进行测试。旅程中的每一站都由一个小测试(代码)和它的输出(日志)组成。

每次我构建这个旅程时，构建过程都会执行每个测试并捕获它的刷新日志。这两者都被插入到一个文本文件中，该文本文件成为游览站点的减价页面。

所以我总是知道测试代码仍然有效！

看看这个:

*   [测试仪巡视](https://github.com/BurdetteLamar/RubyTest/blob/master/examples/github_api/TesterTour.md#tester-tour)
*   [RubyTest 项目](https://github.com/BurdetteLamar/RubyTest)