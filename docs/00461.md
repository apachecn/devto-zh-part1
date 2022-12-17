# 当编写包含 JS 的原因库时

> 原文：<https://dev.to/mrmurphy/when-writing-a-reason-library-with-js-included-33i3>

今天，我发现自己在 Reason 中编写了一些包代码(具体来说，是一个 local feed 包装器)。出于各种原因，我不想在这里深究，我想出售一些 JS 代码，包括项目中的 Javascript 文件，并从我的原因代码用一个相对路径`./foo.js`链接到它。

问题来了。bucklescript(Javascript 后端编译器)尽可能去掉不必要的代码，所以我的库包装代码(只有几个`external`声明)被完全去掉，而`import`语句被直接移到调用包装库的文件中。

这是一个很好的优化，当被包装的 Javascript 存在于`node_modules`中，并且可以从项目中的任何文件访问而不改变导入路径时，这个优化效果很好。但是当使用相对进口时，效果就不那么好了。

Reason Discord 通道通知我，如果库包含一个`.rei`文件，或者一个[原因接口文件](https://reasonml.github.io/docs/en/module.html#every-rei-file-is-a-signature)，包装代码不会被编译掉，相对导入将留在定义它的文件中，而不是移动到调用站点。

这个*应该*允许 Reason 库作者在他们的 Reason 项目中包含使用相对路径链接的原始 Javascript。

“不和谐频道”也提出了另一种方法:

只需复制您想要包含的所有 Javascript，并将其粘贴到某个原因文件顶层的`[%%bs.raw ]`块中。这将把所有的 JS 直接包含到该文件的编译输出中，并且作者完全不需要担心之后的相对导入。