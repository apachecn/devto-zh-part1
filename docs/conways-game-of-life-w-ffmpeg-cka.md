# 康威的生活游戏

> 原文：<https://dev.to/yohhoy/conways-game-of-life-w-ffmpeg-cka>

可以用 [FFmpeg](https://www.ffmpeg.org/) 命令行工具玩[《康威的人生游戏》](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)。我没开玩笑。

让我们用 worldsize=60x60 generation=500 来生成 GIF 动画。

```
$ ffmpeg -f lavfi -i life=s=60x60 -vframes 500 lifegame.gif 
```

Enter fullscreen mode Exit fullscreen mode

[![world60x60-500gen-simple](img/3b138d047d1ab964316c625a04a2e8c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pVWJoF-U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://camo.qiitausercontent.com/eb6eb367d6c0366643178e211d4e26d6cc9c271a/68747470733a2f2f71696974612d696d6167652d73746f72652e73332e616d617a6f6e6177732e636f6d2f302f363635372f64336131666432372d643865662d663463342d656339352d3930316662353437653661342e676966)

好的，这里是装饰版:

```
$ ffmpeg -f lavfi -i life=s=60x60:life_color=00ff00:ratio=.5 \
  -sws_flags neighbor -vf scale=360:360,drawgrid=w=6:h=6 \
  -vframes 500 lifegame.gif 
```

Enter fullscreen mode Exit fullscreen mode

[![world60x60-500gen](img/f0793720726b8882b2379e08ba1581e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_z_xPOyJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://camo.qiitausercontent.com/6460dfb406278f818805744fde2ecefe9203e1e4/68747470733a2f2f71696974612d696d6167652d73746f72652e73332e616d617a6f6e6177732e636f6d2f302f363635372f64373634383466652d346161612d313763662d353837332d3962313139313763663961612e676966)

尽情享受吧！