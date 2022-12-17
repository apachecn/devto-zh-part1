# 如何编写自己的 Python 文档生成器

> 原文：<https://dev.to/tryexceptpass/how-to-write-your-own-python-documentation-generator-4f97>

### 用 inspect 模块进行自检

在我使用 Python 的早期，我最喜欢的事情之一是坐在解释器前使用内置的`help`函数来检查类和方法，试图确定下一步该键入什么。这个函数导入一个对象并遍历它的成员，提取文档字符串并生成类似联机帮助页的输出，以帮助您了解如何使用它所检查的对象。

将它内置到标准库中的好处在于，由于输出是直接从代码中生成的，它间接强调了像我这样的懒人的编码风格，这些人希望做尽可能少的额外工作来维护文档。尤其是如果您已经为变量和函数选择了简单明了的名称。这种风格包括向函数和类中添加文档字符串，以及通过在私有成员和受保护成员前面加上下划线来正确地标识它们。

[读下去...](https://tryexceptpass.org/article/how-to-write-your-own-python-documentation-generator/)