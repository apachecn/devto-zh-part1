# 你喜欢的框架还不够

> 原文：<https://dev.to/samsonasik/your-favorite-framework-is-not-enough>

是啊！你可能是一个非常棒的 wow 框架 **X** 的粉丝。但是:

> 没有人是完美的。靠互相扶持活着，就是游戏，叫生活。
> 
> *假面骑士头骨。*

当然，你可以非常努力地调整，使你可爱的框架按照你的需要工作，但是时间会限制它。

每当有另一个工具正好适合这一点，你应该只是使用他们-结合他们-利润。

## 一个用例

我用 PHP 得到了一个项目，我用 Zend Framework。在开始的时候，一切似乎都很完美，我所需要的都在那里，直到一个报道功能的到来。我需要一个 html 到 pdf 的转换器。在我尝试了许多 PHP 函数库之后，我找到了一个适合我需要的，它被命名为 [Snappy](https://github.com/KnpLabs/snappy) ，它可以通过 composer 命令:
很容易地得到

```
$ composer require knplabs/knp-snappy 
```

Enter fullscreen mode Exit fullscreen mode

这个库需要非 php 可执行命令行工具，名为 [wkhtmltopdf](https://wkhtmltopdf.org/)

如果我没记错的话，我的客户也想在他的 windows 中编码，服务器使用 linux，我的开发工具使用 OS X。由于我的资源有限，虚拟机当时不是一个解决方案，所以，我在我的应用程序中下载了 3 个版本(windows、linux 和 mac)的可执行文件。

```
.
├── bin
│   ├── linux
│   │   └── wkhtmltopdf
│   ├── mac
│   │   └── wkhtmltopdf
│   └── windows
│       ├── bin
│       │   ├── wkhtmltoimage.exe
│       │   ├── wkhtmltopdf.exe
│       │   └── wkhtmltox.dll
│       ├── include
│       │   └── wkhtmltox
│       └── lib
│           └── wkhtmltox.lib 
```

Enter fullscreen mode Exit fullscreen mode

我创建了一个简单的代码来检测操作系统，它返回如下的`wkhtmltopdf`可执行文件:

```
<?php
private function getWkhtmlToPdfLocation()
{
    $os = PHP_OS;

    if ($os === 'Darwin' || $os === 'Linux') {
        $os = ($os === 'Darwin') ? 'mac' : strtolower($os);
        return realpath(getcwd() . '/bin/' . $os . '/wkhtmltopdf');
    }

    return realpath(getcwd() . '/bin/windows/bin/wkhtmltopdf.exe');
} 
```

Enter fullscreen mode Exit fullscreen mode

我需要从 html 报告生成一个 pdf，并下载它，然后我做以下动作:

```
<?php

use Knp\Snappy\Pdf;
use Zend\View\Model\ViewModel;

// ... demo: not actual code
public function generatereportAction()
{
    $filename = '/path/to/report.pdf';    

    $snappy = new Pdf($this->getWkhtmlToPdfLocation());
    $snappy->generateFromHtml($this->getOutput(), $filename);

    return $this->sendResponse($filename);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我创建了单独的方法来缓冲 html 输出和发送响应，以方便您查看上述操作的逻辑:

```
<?php
// ... demo: not actual code
private function getOutput()
{
    $viewModel = new ViewModel();
    $viewModel->setTemplate('/application/report')->setTerminal(true);

    // renderer is a Zend\View\Renderer\PhpRenderer instance
    $output = $this->renderer->render($viewModel);   

    return $output;
}

private function sendResponse($filename)
{
    $response = $this->getResponse();

    $response->getHeaders()->addHeaderLine('Content-Type', 'application/pdf');
    $response->getHeaders()->addHeaderLine('Content-Disposition', 'attachment; filename="'.basename($filename).'"');
    $response->getHeaders()->addHeaderLine('Content-Length', filesize($filename));

    $response->setContent(file_get_contents($filename));

    //remove file after not needed
    @unlink($filename);

    return $response;
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，您可以将它们包装在服务和模型中，使用捆绑的模块，使其服务可以在许多应用程序中使用。

## 结论

思想开放。