# 使用一夫一妻制导入内容& XNA

> 原文：<https://dev.to/adamkdean/import-content-using-monogame-xna-49pi>

所以我知道这是迟早的事。我知道内容管道会有问题，长期失去的根源会回到 XNA 的深渊，以及无人维护的微软项目。游戏开发是充满挑战的。这是我喜欢它的原因。一切都把你推向极限；精神上，身体上，情感上。

我的第一个挑战是用 XNA 管道建立并运行一个一夫一妻制项目。幸运的是，一夫一妻制的人已经在这方面付出了很多努力，所以这很简单。话说回来，一切都是一旦你想通了。与我作对的是一个由过时的教程和不足的文档组成的支离破碎的网络。

我使用的是 Visual Studio 2012。我安装了 Windows Phone SDK，这允许我安装 XNA Game Studio 4.0。我还安装了 Visual Studio 2012 XNA 刷新扩展，允许我在 VS2012 中做任何事情。最后，我安装了一夫一妻制。

我已经创建了一个名为阿尔法的项目，它的第一种。在那里，我添加了两个新项目:一个一夫一妻制内容项目和一个一夫一妻制 Windows 项目。内容项目将创建两个项目。一个将被称为`%name%`并且是 XNA 内容项目，另一个将被称为`%name%Content`并且是一夫一妻制内容项目。我用了 Alpha 这个名字，它给了我 Alpha 和 AlphaContent。一夫一妻制 Windows 项目将简单地创建一个项目。我称之为 AlphaGame。

所以现在我有三个项目:Alpha (XNA 内容)、AlphaContent(一夫一妻制内容)、AlphaGame(一夫一妻制 windows)。

我们如此需要的原因是因为我们依赖 XNA 将我们的内容转换成 XNA/一夫一妻制就绪的内容。一旦你设置好了，你甚至不需要担心细节。一切都非常非常整洁。

我已经创建了两个文件；blue.png 和 green.png。我有一个名为 assets 的独立目录，它不是 Visual Studio 解决方案的一部分。我会把我所有的内容都保存在这里，并在解决方案准备好的时候复制它。要将它们导入到解决方案中，我只需将它们拖到解决方案资源管理器中。我们希望它们属于`AlphaContent`项目(一夫一妻制内容项目)。

现在，当项目构建时，它们将被转换成 XNA 内容。但是我们希望能够在我们的 AlphaGame 中访问它们。

在文本编辑器中打开 AlphaGame.csproj。我用崇高。在底部，我们想要添加一个 AfterBuild 脚本。

```
<Target Name="AfterBuild">
    <ItemGroup>
        <ContentSource Include="..\Alpha\Alpha\bin\PSM\Content\**\*.*" />
    </ItemGroup>
    <Copy SourceFiles="@(ContentSource)" DestinationFolder="$(TargetDir)\Content\%(RecursiveDir)" SkipUnchangedFiles="true" />
</Target> 
```

Enter fullscreen mode Exit fullscreen mode

您可能需要更改一些路径以适应您的项目。

当我们现在重新构建我们的整个解决方案时，我们将得到以下结果:

```
1>AfterBuild:
1>  Copying file from "..\Alpha\Alpha\bin\PSM\Content\Artwork\blue.xnb" to "C:\Users\Adam\Desktop\AlphaGame\src\AlphaGame\bin\Windows\Debug\\Content\Artwork\blue.xnb".
1>  Copying file from "..\Alpha\Alpha\bin\PSM\Content\Artwork\green.xnb" to "C:\Users\Adam\Desktop\AlphaGame\src\AlphaGame\bin\Windows\Debug\\Content\Artwork\green.xnb". 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样轻松地加载内容:

```
protected override void LoadContent()
{
    this.Content.Load<Texture2D>("Artwork/blue");
} 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章可能不像我的其他文章那样容易阅读。大部分时间我都在思考我在写什么，确保产生易于阅读的内容。但是我的头在游戏开发，所以我只是在做一个 braindump。我将开始把这些帖子标记为`braindump` s。

页（page 的缩写）s，确保您设置游戏项目依赖于内容项目，方法是右键单击解决方案并转到构建顺序，然后将游戏项目设置为依赖于内容项目。