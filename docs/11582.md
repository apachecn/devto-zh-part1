# 提高 YouTube 上的播放速度

> 原文：<https://dev.to/adamkdean/increase-playback-speed-on-youtube-1ak6>

不知道你是不是和我一样没有耐心，如果是的话，你大概看腻了 1.0x 极速播放的教程/教育视频。我以 1.5-2.0 倍的速度浏览了 Jon Skeet 的 C#视频，但 YouTube 却以 1.0 倍的速度运行，而且耗时太长。说重点吧。

但我发现了一种方法来控制这一点，通过强制视频在新的 HTML5 视频播放器中播放，这应该可以让你在选择质量的同一弹出菜单上访问播放速度控制。

从您当前的 URL 中取出您的视频 ID，在本例中为`1CQ0zDfX4QE`,应该如下所示:

> [http://www.youtube.com/watch?v=1CQ0zDfX4QE](http://www.youtube.com/watch?v=1CQ0zDfX4QE)

将`watch`替换为`watch_popup`，并将`&vq=large`添加到 URL 的末尾，就像这样:

> [http://www.youtube.com/**watch_popup**?v=1CQ0zDfX4QE**&VQ =大型**](http://www.youtube.com/**watch_popup**?v=1CQ0zDfX4QE**&vq=large**)

现在你可以从质量弹出菜单中看到速度控制。哈利路亚！

[![Bingo!](img/b3f2a1bb745b84309dc7af211c494086.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0SQsZRVw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/mJnXYGb.png)