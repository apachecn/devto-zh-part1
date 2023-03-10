# Clarifai 驱动的 Adobe Lightroom 插件可根据图像质量自动对照片进行分类

> 原文：<https://dev.to/clarifai/clarifai-powered-adobe-lightroom-plugin-automatically-sorts-your-photos-by-image-quality-26p>

利用我们最近发布的风景质量和肖像质量模型，我们为摄影师构建了一个 Adobe Lightroom 插件，用于自动将高质量的照片与低质量的照片进行分类。这有助于通过过滤掉模糊、过度曝光和其他低质量的图像来提高后期处理工作流程的效率！

数码摄影的进步使得拍摄越来越多的照片变得越来越容易。一方面，这有助于摄影师捕捉到完美的镜头。另一方面，这意味着还有更多糟糕的照片需要整理。我们从最近发布的摄影质量视觉识别模型中构建了一个 Adobe Lightroom 插件，该插件将自动将高质量的照片与低质量的照片进行分类，并为您选择最佳照片进行后期处理节省大量时间和精力！

[下载插件](https://drive.google.com/drive/folders/1-hPYNh6eJihAyAAhyix8bDaEZX_gWsS_?usp=sharing)

### Adobe Lightroom Clarifai 照片质量插件说明

请确保您安装了 Adobe Lightroom Classic。

[![](img/52a50a8c661b3ce280151bd475884497.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MNBOBGcv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image11.png)

从:[https://drive . Google . com/drive/folders/1-hpynh 6 ejihayaahix 8 bdaezx _ gWsS _ 下载插件？usp=sharing](https://drive.google.com/drive/folders/1-hPYNh6eJihAyAAhyix8bDaEZX_gWsS_?usp=sharing) 并解压文件。

安装 Clarifai 插件。转到文件并打开插件管理器

[![](img/e38c6f4aed47d955241bb85be7d71649.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8qiSQBnn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image5.png)

在插件管理器窗口中，点击添加按钮，如下所示。

[![](img/e4e1b65457f854f4c0d6922424a289c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cVP0yoFK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image3.png)

点击解压后的文件，应该是一个目录，标签为，
，
“clarifailandscapequality . lrdevplugin”。这将安装插件。

您应该会看到下面的屏幕。

[![](img/e4e1b65457f854f4c0d6922424a289c6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cVP0yoFK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image3.png)

你只需要输入一个 Clarifai API 密钥就可以开始了。如果你还没有，在这里注册一个账户:[https://clarifai.com/developer/account/signup](https://clarifai.com/developer/account/signup)。

注册后，点击您的名字进入您的帐户仪表板。

[![](img/d74226e3160462002bc4752995ada996.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--e0svNCNv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image4.png)

我们已经为您创建了第一个应用程序。点击名称获取您的 API 密钥。

[![](img/9a9e3f64b38bc727d588f311807aae8d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Grw8oKgl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image8.png)

复制您的 API 密钥并将其输入到 lightroom Clarifai 插件中。(注意:这张图中的 API 键是假的。请创建您自己的以使其工作)

[![](img/3f75d1da1b7d81ffe73fd3b866d6ae32.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_5oOn1dS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image1.png)

在 Lightroom 插件中输入 API 密钥，然后单击“完成”。

在 Lightroom 中选择要运行插件的照片。

[![](img/c599cbafcd46b9996a17c3e9e1bbcd5a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--utKnnRan--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image10.png)

点击“图库>附加插件>用 Clarifai 检查风景照片质量”

[![](img/fa1f9a67b3ec6e1f8415e4618891995d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4osu6lHt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image7.png)

你会看到一个对话框，显示所有选定的照片，如果他们是高质量或低质量的照片。点按“存储”,这样每张照片都可以用该关键词进行标记，然后可以按高质量或低质量对照片进行排序。

[![](img/a211991c8808527db8a8cac2be82e8e7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dTtwHUuc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image2.png)

您可以通过在 Lightroom 的文本过滤器部分键入“高”或“低”来对照片进行排序。

[![](img/b7853abb657829360e22f840b8604dbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_0f0EMiZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.clarifai.com/wp-content/uploads/2017/11/image6.png)

文章 [Clarifai 驱动的 Adobe Lightroom 插件根据图像质量自动分类你的照片](https://blog.clarifai.com/clarifai-powered-adobe-lightroom-plugin-automatically-sorts-photos-image-quality/)最先出现在 [Clarifai 博客|人工智能在行动](https://blog.clarifai.com)上。