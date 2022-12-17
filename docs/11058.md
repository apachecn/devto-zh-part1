# 在 Jekyll 的页面加载中加载一个随机的背景图像

> 原文：<https://dev.to/mariordev/load-a-random-background-image-on-page-load-in-jekyll-i16>

在我的一个 Jekyll 项目中，我想在每次页面加载时加载一个随机的背景图片。是一个很好的 jQuery 插件，可以显示“超大屏幕”风格的大背景图像。所以我把 Backstretch 和一点 JavaScript 和 Jekyll Liquid 结合起来使用。我会告诉你怎么做。

### 后退

在 Jekyll 布局上，加载 Backstretch 插件。

```
...
    <script src="//cdnjs.cloudflare.com/ajax/libs/jquery-backstretch/2.0.4/jquery.backstretch.min.js"></script>
</body> 
```

### Javascript

将它添加到 Jekyll 布局中加载 Backstretch 插件的 script 标记之后。这将选择一个随机图像并显示它。像这样:

```
<script src="//cdnjs.cloudflare.com/ajax/libs/jquery-backstretch/2.0.4/jquery.backstretch.min.js"></script>
    <script>
        // Image pool to select from
        var images = [
            'image1.jpg', 'image2.jpg', 'image3.jpg'
        ];

        // Pick a random image
        var maxImages = images.length;
        var rand = Math.floor(Math.random() * maxImages);
        var image = images[rand];

         // Set image as background
        $(".jumbotron").backstretch(
            '/assets/img/' + image,
            {
                centeredY: false
            }
        );
    </script>
</body> 
```

注意:确保用您自己的文件名和路径替换图像文件名和路径。

### HTML

使用 Bootstrap jumbotron 类在你的页面或帖子上显示背景图像，就大功告成了！

```
<div class="jumbotron">
  <div class="container text-center">
          <h1>Hello World!</h1>
  </div>
</div> 
```

## 更灵活

如果您想灵活地用逐页确定的特定图像覆盖随机图像，您只需添加一些修改即可:

### 身前要紧

在你页面的首页，添加一个带有图片名称的变量，比如:

```
---
layout: your_layout
...
image: your_image_file_name
--- 
```

### 头

在您的布局上，添加一个 meta 标记，这样我们就可以将图像文件名从 Jekyll 传递给 JavaScript。

```
<head>
    ...
    {% if page.image %}
        <meta name="backg-image" content="cliff.jpg">
    {% endif %}
</head> 
```

### Javascript

添加一个液态条件来运行适当的 JavaScript 代码:

```
{%if page.image %}
    <script>
        // Use image file provided by the page
        var image = $('meta[name="backg-image"]').attr('content');
    </script>
{% else %}
    <script>
        // Image pool to select from
        var images = [
            'image1.jpg', 'image2.jpg', 'image3.jpg'
        ];

        // Pick a random image
        var maxImages = images.length;
        var rand = Math.floor(Math.random() * maxImages);
        var image = images[rand];
    </script>
{% endif %}

<script>
     // Set image as background
    $(".jumbotron").backstretch(
        '/assets/img/' + image,
        {
            centeredY: false
        }
    );
</script> 
```

就是这样。加载和重新加载你的页面，你应该看到图像随机变化。

编码快乐！