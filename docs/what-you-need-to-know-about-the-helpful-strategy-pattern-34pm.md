# 关于有用的策略模式，你需要知道什么

> 原文：<https://dev.to/kylegalbraith/what-you-need-to-know-about-the-helpful-strategy-pattern-34pm>

我最近在学习新语言的同时，重新审视了各种编码模式。我个人最喜欢的一个模式是策略模式。策略模式的前提是封装一系列行为。封装后，我们可以选择在运行时使用哪种行为。

对于我的例子，我将展示 C#中的模式，但这适用于各种语言。假设我们正在创建一段从文本、pdf 和图像文件中提取内容的代码。因此，我们的行为是文本提取。但是我们希望根据文件的类型以不同的方式提取内容。

首先，我们定义一个名为`ITextExtractor`的接口。

```
public interface ITextExtractor
{
    bool UseExtractor(string fileExtension);
    string[] ExtractContent(string filePath);
} 
```

Enter fullscreen mode Exit fullscreen mode

对于我们想要从中提取内容的每种类型的文档，我们创建一个实现`ITextExtractor`的新类。请注意`UseExtractor`方法，因为我们将在运行时使用它来选择我们的提取器。让我们继续创建三个文本提取器。

### 平原文本摘录器

```
public class PlainTextExtractor : ITextExtractor
{
    public bool UseExtractor(string fileExtension)
    {
        return fileExtension.Equals("txt", StringComparison.OrdinalIgnoreCase);
    }

    public string[] ExtractContent(string filePath)
    {
        // extract the content here...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

只有当文件扩展名以 txt 结尾时，`PlainTextExtractor`才会从`UseExtractor`返回 true。

### pdftextractor

```
public class PdfTextExtractor : ITextExtractor
{
    public bool UseExtractor(string fileExtension)
    {
        return fileExtension.Equals("pdf", StringComparison.OrdinalIgnoreCase);
    }

    public string[] ExtractContent(string filePath)
    {
        // extract the content here...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

只有当文件扩展名以 pdf 结尾时，`PdfTextExtractor`才会从`UseExtractor`返回 true。

### 图像文字摘录器

```
public class ImageTextExtractor : ITextExtractor
{
    private string[] _imageTypes = new[] { "png", "jpg", "jpeg", "tiff" };
    public bool UseExtractor(string fileExtension)
    {
        return _imageTypes.Any(it => it.Equals(fileExtension, StringComparison.OrdinalIgnoreCase);
    }

    public string[] ExtractContent(string filePath)
    {
        // extract the content here...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

只有当文件扩展名以 png、jpg、jpeg 或 tiff 结尾时，`ImageTextExtractor`才会从`UseExtractor`返回 true。有比这多得多的图像类型，但是这给你一个我们在追求什么的想法。

### 运行时选择策略的 _(ツ)_/方法

现在我们有了不同的文本提取器。当在运行时选择合适的提取器时，你经常会看到这样的代码。

```
public class RunExtraction
{
    private PlainTextExtractor _plainTextExtractor;
    private PdfTextExtractor _pdfTextExtractor;
    private ImageTextExtractor _imageTextExtractor;

    public RunExtraction(PlainTextExtractor plainTextExtractor,
        PdfTextExtractor  pdfTextExtractor, ImageTextExtractor imageTextExtractor)
    {
        _plainTextExtractor = plainTextExtractor;
        _pdfTextExtractor = pdfTextExtractor;
        _imageTextExtractor = imageTextExtractor;
    }

    public string[] Extract(string filePath, string fileExtension)
    {
        if(_plainTextExtractor.UseExtractor(fileExtension))
        {
            return _plainTextExtractor.ExtractContent(filePath);
        }
        else if(_pdfTextExtractor.UseExtractor(fileExtension))
        {
            return _pdfTextExtractor.ExtractContent(filePath);
        }
        else if(_imageTextExtractor.UseExtractor(fileExtension))
        {
            return _imageTextExtractor.ExtractContent(filePath);
        }
        else
        {
            throw new Exception("unable to extract content");
        }        
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码在技术上没有任何问题。但它是未来最具扩展性的吗？如果我们不得不开始从 Word 文档中提取内容会怎么样？

首先，我们将创建一个新的实现`ITextExtractor`的`WordDocumentTextExtractor`类。

```
public class WordDocumentTextExtractor : ITextExtractor
{
    private string[] _docTypes = new[] { "doc", "docx" };
    public bool UseExtractor(string fileExtension)
    {
        return _docTypes.Any(it => it.Equals(fileExtension, StringComparison.OrdinalIgnoreCase);
    }

    public string[] ExtractContent(string filePath)
    {
        // extract the content here...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们必须更新`RunExtraction`类，以在构造函数中获取`WordDocumentTextExtractor`。

```
private PlainTextExtractor _plainTextExtractor;
private PdfTextExtractor _pdfTextExtractor;
private ImageTextExtractor _imageTextExtractor;
private WordDocumentTextExtractor _wordDocumentTextExtractor;

public RunExtraction(PlainTextExtractor plainTextExtractor,
    PdfTextExtractor  pdfTextExtractor, ImageTextExtractor imageTextExtractor,
    WordDocumentTextExtractor wordDocumentTextExtractor)
{
    _plainTextExtractor = plainTextExtractor;
    _pdfTextExtractor = pdfTextExtractor;
    _imageTextExtractor = imageTextExtractor;
    _wordDocumentTextExtractor = wordDocumentTextExtractor;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要添加另一个`else if`语句来检查 Word 文档。

```
else if(_wordDocumentTextExtractor.UseExtractor(fileExtension))
{
    return _wordDocumentTextExtractor.ExtractContent(filePath);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不断地从不同类型的文档中提取内容，这将变得难以控制。每次我们都必须:

1.  添加新的文本提取类。
2.  将新类传入`RunExtraction`。
3.  更新`else if`条件以检测新的文件类型。

我的焦虑程度已经在上升了。一定有不同的方法，对吗？

### 运行时选择策略的 q(❂‿❂)p 方法

幸运的是，我们用我们的战略模式为自己的成功做好了准备。每个文本提取器实现相同的接口`ITextExtractor`。在该接口中，我们添加了方法`UseExtractor`。它根据每个提取器支持的扩展返回`true`或`false`。我们可以利用这两个优势。

首先，我们改变传递给`RunExtraction`的构造函数的内容。

```
private ITextExtractor[] _extractors;

public RunExtraction(ITextExtractor[] extractors)
{
    _extractors = extractors;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们不再为每种类型的提取器传递具体的类。相反，我们传入一个数组`ITextExtractor`。这样，当我们想要添加一个新类型的提取器时，我们只需将它添加到传入的数组中。

接下来，我们可以将`RunExtraction`的`Extract`方法改为不再使用`if`T3....`else if`。

```
public string[] Extract(string filePath, string fileExtension)
{
    var extractor = _extractors.FirstOrDefault(e => e.UseExtractor(fileExtension));
    if(extractor != null)
    {
        return extractor.ExtractContent(filePath);
    }
    else
    {
        throw new Exception("unable to extract content");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

再见`else if`和你好扩展性。我们的`RunExtract`类现在可以轻松地支持新的文档文本提取器。现在，当我们想要添加我们的`WordDocumentTextExtractor`时，我们需要完成以下步骤:

1.  添加新的文本提取类。
2.  将新类添加到`RunExtraction`的提取器数组中。

### 结论

这里我们已经介绍了策略模式的基础。

*   为类似的功能定义一个接口。(如文本提取)
*   创建实现该功能的具体类。
*   使用依赖注入和接口来密封您的逻辑类，防止更改。
*   在运行时动态选择必要的功能。

然后观察随着不同类型的具体类的添加，您的代码将来会变得更加可扩展。

### 饿着肚子学亚马逊 Web 服务？

有很多人渴望学习亚马逊网络服务。受到这个事实的启发，我创建了一个课程，专注于通过使用它来学习 Amazon Web Services。关注静态网站的托管、保护和交付问题。通过构建问题的解决方案，您可以学习 S3、API Gateway、CloudFront、Lambda 和 WAF 等服务。

AWS 周围有大量的信息。很容易迷失方向，在学习上没有任何进步。通过解决这个问题，我们可以简化信息，加快你的学习。我用这本书和视频课程的目的是与你分享我所学到的。

听起来有趣吗？查看登录页面了解更多信息，并选择适合您的套餐，[此处](https://www.kylegalbraith.com/learn-aws/)。