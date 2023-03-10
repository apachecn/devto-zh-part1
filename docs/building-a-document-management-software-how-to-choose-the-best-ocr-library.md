# 构建文档管理软件:如何选择最好的 OCR 库

> 原文：<https://dev.to/dianamaltseva8/building-a-document-management-software-how-to-choose-the-best-ocr-library>

在这篇文章中，我将分享我们公司在选择最适合解决我们的任务和目标的 OCR 库方面的经验。

由于我们需要改进公司内部的文档管理，特别是自动化企业纸面记录分析过程，我们决定创建一个基于 OCR 库的软件解决方案。

OCR，或称 [光学字符识别](https://gist.github.com/githubutilities/b6a9062c27f43cc52d60) ，是将打字文本的图像机械或电子转换成机器编码的文本。

OCR 还代表了一种将印刷文本数字化的方法，以便可以对其进行电子存储、编辑、显示，并在认知计算、机器翻译和数据挖掘等机器过程中使用。

此外，OCR 是作为纸质文档(包括财务记录、名片、发票等等)的一种信息输入形式而应用的。

在开始开发过程之前，我们对三个最流行的 OCR 库进行了研究，以确定最适合我们目标的库。

### 我们调查了以下库:

1.  谷歌文本识别 API
2.  宇宙魔方
3.  任何线路

### 谷歌文本识别 API

Google 文本识别 API 是检测图像和视频流中的文本并识别其中包含的文本的过程。一旦检测到，识别器就确定每个块中的实际文本，并将其分割成行和词。

文本 API 检测多种语言(法语、德语、英语等)的文本。)实时。

应该注意的是，总的来说，Google 文本识别 API 对于解决我们的任务是有效的。我们获得了识别实时文本和文本文档的现成图像的能力。

在我们的研究中，我们定义了使用 Google 文本识别 OCR 库的一些优点和缺点。

**优点:**

*   实时识别文本的能力
*   从图像中识别文本的能力
*   小型图书馆
*   高识别速度

**缺点:**

*   包含训练数据的大文件(~30Mb)

了解更多关于 **[OCR 库，他们的调查，检查出他们的比较。](https://smartym.pro/blog/developing-a-document-management-solution-how-were-choosing-between-google-text-recognition-api-tesseract-and-anyline-ocr-libraries/)**