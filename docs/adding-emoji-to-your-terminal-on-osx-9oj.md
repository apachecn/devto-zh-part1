# 将表情符号ðÿ添加到您的终端

> 原文：<https://dev.to/anthonydelgado/adding-emoji-to-your-terminal-on-osx-9oj>

如果你整天盯着一个终端编码，尽可能定制和个性化你的工作环境是很重要的。除了[改变你的终端 mac 的颜色](https://apple.stackexchange.com/questions/92756/how-do-you-change-mac-terminal-theme-so-that-it-doesnt-go-back-to-basic-every)之外，给你的终端添加一个(或两个)表情符号也是一种有趣、快速和简单的定制你的设置的方式。在最近我为我的开发团队的一些成员创建的[要点](https://gist.github.com/anthonydelgado/0d8997ea95c885b61c86d8dcfb60a11f)中，我向你展示了如何做到这一点！

### 第一步。打开你的。bash_profile 在您最喜欢的文本编辑器中。

```
sublime ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

### 步骤 1.1 如果文件尚不存在您可以创建它)

```
cd ~
touch .bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步将这一行添加到文件的底部

W 是你当前所在的工作目录，之后你可以在你的终端上添加无限数量的表情符号。

```
PS1="\W ðŸ”¥ðŸ¦„ " 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步-保存并退出/重启终端&享受！

[![Alt text of image](img/1bb1d97c25aecba925044bafa88fa760.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hGFUbrgP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2kuvnvxzhp2pa0uf4m5n.png)

## 结果是这样的。

您可以使用[这个生成器](http://bashrcgenerator.com/)来完全定制您的命令提示符。

#### 你还能如何定制你的开发设置？

### ðÿ'‡在下面的评论区留下你的设置技巧。ðŸ'‡

黑客快乐！