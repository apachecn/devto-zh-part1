# CSS 中的图像预加载

> 原文：<https://dev.to/cwardzala/image-preloading-in-css-49ad>

有一段时间我没有想到的是 css 中的图像预加载。前几天，我给 Matt 发了一份代码审查，因为他预加载图像的方式导致了一些奇怪的行为。我们讨论过这个问题，但是想不出一个好的解决方案，或者更好的预加载选项，所以我移除了他的预加载 CSS(如下)。

```
html {
    background: url(/path/to/image.jpg) no-repeat -1000px;
} 
```

Enter fullscreen mode Exit fullscreen mode

在大多数情况下，我认为这种方法是可行的，但在我们的例子中，它会导致内容简短的页面在页面底部有一个不规则的白色块。我不确定如何告诉他解决这个问题，我们真的不想走 JS 的老路，但是在 CSS 中找不到这样做的方法。

然后，毫无理由地，我四处寻找 http://foundation.kr 的来源，发现了这个对我来说完全有意义的小东西。

```
body:before {
    content: url() url() ...;
    display:none;
} 
```

Enter fullscreen mode Exit fullscreen mode

我的第一个想法是“哦，天哪，这太棒了！”。然后我开始怀疑图片会不会下载，因为`display:none`。我更新了我们的代码来使用这种预加载的方法，并且成功了！我抓住 Matt 给他看，他问了我同样的关于展示品的问题。因为图像 url 是伪元素的内容，所以不管显示属性如何，它都会被下载。不像背景图片只有在图片显示时才会被下载。

这种方法最好的部分是你可以在内容中有尽可能多的`url()`,它们都会被下载！