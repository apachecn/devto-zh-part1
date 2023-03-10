# 如何保持代码库之间的功能同步？

> 原文：<https://dev.to/goodidea/how-to-keep-functionality-in-sync-between-codebases>

我正在建立一个新项目，它将由一个中央 API 组成，许多不同的子项目将链接到其中。

为了简单起见，假设整个平台的部分功能是获取图像的元数据。这可能发生在核心 API 中，或者某个子项目的服务器或前端。

所有这些都将用 Javascript 编写，像这样的函数将是纯粹的，包含在它们自己的模块中。我想有一个单一的地方，我可以创建和编辑这些模块，以便在任何情况下以上，我可以这样做:

```
import getImageMetaData from 'getImageMetadata'; // it's within node_modules

const resource = 'http://example.com/image.jpg'
const metaData = getImageMetaData(resource);

... 
```

Enter fullscreen mode Exit fullscreen mode

最简单的方法是给这个模块一个自己的 github repo，然后在服务的所有不同端将它包含在`package.json`中，并持续运行(使用钩子或其他东西)`npm install upgrade my-package`？

有什么聪明的方法让这一切保持同步吗？最好是尽可能自动化~前端子项目可能多达十几个，我想确保我能一次修复所有这些项目。