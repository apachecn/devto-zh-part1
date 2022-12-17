# 我的天啊

> 原文：<https://dev.to/kritner/xml2rfc-oh-my-jeez-230n>

呃。在安装 xml2rfc 的过程中，开始使用 Python for windows 遇到了很大的麻烦。背景是我正试图在一个项目的适当 XML 文件中重新生成一些基于 JSON 编辑的 RFC 文档。

所以，下面是我(认为)最终采取的让它工作的步骤——以防我需要再做一次。

1.  安装 Python 3.5

    1.  统一资源定位器
    2.  [https://www.python.org/downloads/release/python-352/](https://www.python.org/downloads/release/python-352/)
    3.  我使用的确切链接:
    4.  [https://www . python . org/FTP/python/3 . 5 . 2/python-3 . 5 . 2-amd64-web install . exe](https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64-webinstall.exe)
2.  (失败)pip 安装 xml2rfc

    1.  与 lxml 相关的错误，由于某种原因从 VS14 目录抛出异常？
3.  (失败)pip 安装 lxml

    1.  好的...所以我想这是不要做的事情
4.  pip 安装轮

    1.  在浏览线程之后，发现“wheel”可以用来安装 lxml
5.  python -m pip 安装 lxml-3 . 6 . 4-cp35-cp35m-win _ amd64 . whl

    1.  从[http://stackoverflow.com/a/30494745/2312877](http://stackoverflow.com/a/30494745/2312877)
    2.  抓住。lxml 的 whl(不确定 pip 安装 lxml 为什么不起作用)
    3.  放入目录
    4.  从基地运行命令
6.  pip 安装 xml2rfc

万岁！那花了很长时间...显然，事情只是需要以一种奇怪的顺序来完成，而不是一种(至少我会)期望的顺序。哦，与这个包管理器相比，我多么想念 npm...