# 菜鸟的错误在。射流研究…

> 原文：<https://dev.to/mowglite/the-rookies-errors-in-js>

当谈到编码场景时，我仍然非常新鲜，但今晚对我来说是最有趣但非常大的学习曲线之一，我可以对所有像我一样的初学者说，总是在每个转折点寻求帮助，让你想知道“我做错了什么？为什么这样不行？”因为今晚就是一个很好的例子。这是我为一个反应测试小游戏编写的一些代码，使用了一点 HTML 和 CSS，但主要是在 JavaScript 方面，让我的大脑处理数组(这将给出一个随机大小的圆形或带有随机颜色的正方形)，然后在最后给出正确的“答案”。(答案是对象出现在浏览器中后你点击它所花的时间)

```
 function appearAfterdelay() {
        setTimeout(makeShapeAppear, Math.random() * 2000);

    }

    appearAfterDelay();

        document.getElementById("shape").onclick = function() {
            document.getElementById("shape").style.display = "none";

            var end = new Date().getTime();

            var timeTaken = (end - start) /1000;

            document.getElementById(timeTaken).innerHTML = timeTaken + "s";

            appearAfterDelay();
    } 
```

Enter fullscreen mode Exit fullscreen mode

所以你们中的一些人已经看到那里的错误了，至于我，我仔细思考了给出第一个错误的控制台日志，然后回头看代码，我一定花了大约一两个小时来来回回试图找出我做错了什么。

第一个错误是这样的:

```
Uncaught ReferenceError: appearAfterDelay is not defined
at javascript.js:59 
```

Enter fullscreen mode Exit fullscreen mode

指的是这一行:

```
 appearAfterDelay(); 
```

Enter fullscreen mode Exit fullscreen mode

所以我又看了一遍所有的剧本，最终找了一个朋友帮忙，不用说他差点笑死了，我也是，当他给我看了上面几行的这个简单的拼写错误

```
 function appearAfterdelay() {
        setTimeout(makeShapeAppear, Math.random() * 2000); 
```

Enter fullscreen mode Exit fullscreen mode

d(出现在 delay 中)应该是大写的。
修复后，我想“耶，终于可以工作了”,但时间很短，因为现在我得到了一个错误消息:

```
Uncaught TypeError: Cannot set property 'innerHTML' of null
at HTMLDivElement.document.getElementById.onclick (javascript.js:68) 
```

Enter fullscreen mode Exit fullscreen mode

参考此行:

```
 document.getElementById(timeTaken).innerHTML = timeTaken + "s"; 
```

Enter fullscreen mode Exit fullscreen mode

我又通读了一遍，责备自己没有看到问题所在，喝了一杯咖啡后，我叫我的朋友回来帮忙，但他又一次笑得死去活来，我只留下了时间表两边的两个小小的" "。

因此，我学到了一个教训，因为我真的不习惯寻求帮助，我希望现在能更多地这样做，对于那些刚刚开始的人，我建议你也这样做，而不是模仿我，花几个小时想知道什么是错的，当你能够放松下来，看着手中的一杯咖啡以应有的方式工作时。