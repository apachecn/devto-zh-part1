# 围棋中的远程登录蛇

> 原文：<https://dev.to/plutov/snake-over-telnet-in-go-42n>

前段时间非常流行 Telnet 游戏，尤其是这部星球大战电影:`telnet towel.blinkenlights.nl`。

我想在 Go 中创造一些东西，于是我通过 Telnet 编写了这个[贪吃蛇游戏。](https://github.com/plutov/go-snake-telnet)

在这种情况下，Go 非常棒，不需要任何依赖来构建这个有趣的东西。

试试看:

```
telnet pliutau.com 8080 
```

Enter fullscreen mode Exit fullscreen mode

[![go-snake-telnet](img/63c5dc0a8128ae8237bc426ea58ddcb6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uE2V45TJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://pliutau.com/go-snake-telnet.gif)

### 发展

```
go get github.com/plutov/go-snake-telnet
go-snake-telnet --host localhost --port 8080 
```

Enter fullscreen mode Exit fullscreen mode

### 投稿

这是一个开源项目，所以请随意贡献- [go-snake-telnet](https://github.com/plutov/go-snake-telnet) 。