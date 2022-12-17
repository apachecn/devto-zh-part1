# 调整位图的大小，而不会严重影响质量

> 原文：<https://dev.to/adamkdean/resize-bitmap-without-major-quality-loss-1b18>

我目前正在从事一个计算机视觉项目。在处理它的时候，我不得不调整一些位图的大小，因为我使用的网络摄像头库不能正确设置摄像头的分辨率。

我在 StackOverflow 上发现了这个，效果相当好。有一些质量差异，如平滑导致的锐度损失，但只有当你将两幅图像并排放置时才显而易见。

```
Bitmap resized = new Bitmap(desiredHeight, desiredWidth);
using (Graphics graphics = Graphics.FromImage(resized))
{
    graphics.SmoothingMode = SmoothingMode.HighQuality;
    graphics.InterpolationMode = InterpolationMode.HighQualityBicubic;
    graphics.PixelOffsetMode = PixelOffsetMode.HighQuality;
    graphics.DrawImage(originalImage,
        new Rectangle(0, 0, desiredHeight, desiredWidth));
}
pictureBox1.Image = resized; 
```

Enter fullscreen mode Exit fullscreen mode

就像我说的，工作得很好，绝对是一个片段箱！