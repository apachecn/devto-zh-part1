# Bash 的 Ctrl+R 自动完成功能是一个救命稻草

> 原文：<https://dev.to/ben/ctrlr-autocomplete-with-bash-is-a-life-saver>

我最近才开始将`Ctrl+R`集成到我的命令行工作流中，我很高兴我做到了！这个名为`reverse-i-search`的命令开始自动完成一个人的历史，并消除了我的大多数`up`、`up`、`up`、`up`、`up`行为。

[![ctrl+r bash](img/a9a5035a83b64061aa67722111ebd587.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y6wtf6ux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.imgur.com/IhjImpr.gif)

从 UX 的角度来看，`Ctrl+R`非常适合我。它已经是我经常运行的一些关键命令的肌肉记忆的一部分。它通过搜索你最近的`.bash_history`来工作。一旦您看到您正在寻找的命令，您可以按下 return 键来执行它或使用左箭头和右箭头按钮来修改它。如果你没有看到你想要的命令，或者不太记得你需要的参数，你可以一直点击`Ctrl+R`来循环显示结果。这是一种非常自然的流动。

如果你还沉迷于`up`、`up`、`up`、`up`、`up`，我希望你自己试试这个命令。