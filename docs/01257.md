# 您应该将/vendor 文件夹提交给 git 吗？

> 原文：<https://dev.to/tommym9/should-you-commit-your-vendor-folder-to-git-2kae>

Composer 的建议是*而不是*提交您的/vendor 文件夹，并让您的部署流程负责运行`composer install`来获取您的所有依赖项。

阅读完本文后，我开始接受这样一种观点，即在 git 中拥有/vendor 文件夹有一些非常大的优势:

[https://www . code enigma . com/build/blog/do-you-really-need-composer-production](https://www.codeenigma.com/build/blog/do-you-really-need-composer-production)

我很好奇是否还有人认为这是一个好主意！