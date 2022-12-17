# 一个新手向所有 JavaScript 奇迹提出的问题

> 原文：<https://dev.to/mowglite/a-question-from-a-rookie-to-all-the-java-marvels-out-there>

所以这里是基本的。下面的代码引用了一个反应游戏，用户点击一个随机出现的对象，当他们出现在浏览器中时，它会显示他们点击它所花的时间。

```
function appearAfterDelay() {
        setTimeout(makeShapeAppear, Math.random() * 2000);

    }

    appearAfterDelay();

        document.getElementById("shape").onclick = function() {
            document.getElementById("shape").style.display = "none";

            var end = new Date().getTime();

            var timeTaken = (end - start) /1000;

            document.getElementById("timeTaken").innerHTML = timeTaken + "s";

            appearAfterDelay();
    } 
```

Enter fullscreen mode Exit fullscreen mode

(如果你们中的任何人知道更短或更干净的编写代码的方法，你看到我在这里或在未来的帖子中发布，请让我知道，因为我是新的，我想知道我正在做的事情是否干净和可读，如果有更快的方法，那么我完全支持它)

不过还是回到主题吧。我一直在寻找能够让我捕捉他们最快时间(在 2 分钟内，让用户试试运气)的代码，然后将它存储在侧栏中作为记录，允许他们在计时器结束时输入自己的名字。 并且(假设它保存了前十个记录)将其与这十个记录进行比较，如果它击败了其中的一个或多个记录，则将其正确定位，并删除将移动到第十一位的记录。

所以，如果你有这方面的想法，或者知道我可以阅读这方面资料的地方，请告诉我，或者我只是想做一些不可能的事情？因为在我所知道的地方，我还没有发现任何对我有帮助的东西(尽管这可能是因为我不知道到底应该如何称呼这样的代码片段)

任何提示，技巧和想法将不胜感激。谢了。