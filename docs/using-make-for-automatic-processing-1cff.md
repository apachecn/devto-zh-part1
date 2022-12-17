# 使用 make 进行自动处理

> 原文：<https://dev.to/wincentbalin/using-make-for-automatic-processing-1cff>

最近，我需要从一个 PDF 文件中提取文本。此 PDF 文件是由扫描的页面生成的。为了提取页面，我使用了命令

```
pdfimages file.pdf IMG 
```

Enter fullscreen mode Exit fullscreen mode

它将原始扫描结果存储在 PBM 格式的图像文件中。这些文件以`IMG-XXX.pbm`模式命名，从`XXX`开始，从`000`开始递增。

下一步，我将原始的`.pbm`文件转换成 TIFF 格式，因为[宇宙魔方光学字符识别软件](https://github.com/tesseract-ocr)更喜欢这种格式。通过使用 [ImageMagick](http://imagemagick.org) 命令`convert`，图像被额外处理以去除斑点和其他瑕疵。格式转换和图像处理命令整体为

```
convert -verbose -colorspace gray -median 3x3 -resize 300% -blur 10 input.pbm output.tiff 
```

Enter fullscreen mode Exit fullscreen mode

由于我一次对多个图像重复尝试不同的图像处理链，执行时间超过了 1 分钟。看着 CPU 负载，我突然想到，一次只使用了一个内核。多核 CPU 上的多进程执行肯定会加快每次 OCR 运行的速度。但是一开始我不确定是否有一个简单的进程管理器，它限制了并发运行进程的数量。然后我想起了一个老熟人， [`make`](http://en.wikipedia.org/wiki/Make_%28software%29) ，或者，准确的说， [GNU `make`](https://www.gnu.org/software/make/) 。此外，该工具还可以自动解析依赖关系，这有助于进程的调度。

唯一需要解决的问题是缺乏从源图像到 OCRed 文本的翻译规则，或者用`make`的说法，*模式规则*。幸运的是，几年前我使用`make`从描述中自动产生可打印的和可听的乐谱，这些乐谱是用 [ABC 符号](http://en.wikipedia.org/wiki/ABC_notation)写的；因此，我只需要再次阅读 [`make`文档](https://www.gnu.org/software/make/manual/make.html)。这是结果:

```
CONVERT = convert
IMFLAGS = -verbose -colorspace gray -median 3x3 -resize 300% -blur 10

TESSERACT = tesseract

RM = rm

SRCS = $(wildcard *.pbm)
INTERMEDIATES = ${SRCS:.pbm=.tiff}
RESULTS = ${SRCS:.pbm=.txt}

.PHONY: all clean
all: $(RESULTS)
.PRECIOUS: $(INTERMEDIATES)

clean:
$(RM) -f $(INTERMEDIATES) $(RESULTS)

%.tiff: %.pbm
$(CONVERT) $(IMFLAGS) $< $@

%.txt: %.tiff
$(TESSERACT) $< $(basename $@) -l deu 
```

Enter fullscreen mode Exit fullscreen mode

命令`convert`、`tesseract`和`rm`存储在变量`CONVERT`、`TESSERACT`和`RM`中，以便能够指定它们的异常位置。变量`SRCS`、`INTERMEDIATES`和`RESULTS`包含文件名。声明`.PHONY`指示`make`执行目标`all`和`clean`，即使存在实际文件名`all`或`clean`。`.PRECIOUS`声明指示`make`保存中间文件。以`%to: %from`形式开始的线组是模式规则(翻译规则)。`$<`和`$@`分别表示源文件和目标文件。

用`make -j number`运行，其中`number`等于 CPU 中的核心数或线程数。在 4 核的 AMD CPU 上，我用的是`make -j 4`。

使用适用于任何现成操作系统的`make`，您可以在多核系统上加快冗长的处理速度。另外，`make`确保处理链不同阶段之间的无缝翻译。

这就引出了一个问题:除了日常的源代码编译，你在哪里使用`make`？