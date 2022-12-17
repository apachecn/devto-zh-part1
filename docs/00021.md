# 一个简单的刺激表过滤器演示

> 原文：<https://dev.to/dstull/a-simple-stimulus-table-filter-demo-3gmb>

# 目标

展示了使用名为 [stimulus](https://stimulusjs.org/) 的新适度 js 框架实现表过滤的前后情况

我们将关注一个具有表格和过滤功能的简单页面。

我将把它分成两步:

*   无刺激实施
*   刺激实施

# 实现无刺激

为了完成这一部分，我创建了一个简单的项目。涉及到两个文件:

## [HTML 文件](https://github.com/dstull/stimulus-demo/blob/without-stimulus/app/views/hello/index.html)

## [CoffeeScript 文件](https://github.com/dstull/stimulus-demo/blob/without-stimulus/app/assets/javascripts/hello.coffee)

在这里，我演示了当用户按 enter 键时对表行的过滤。

# 用刺激实现

为了完成这一部分，我使用了主分支。[这个](https://github.com/dstull/stimulus-demo/commit/7681b4d0ab01c39ce09aaa8d562590a0b9539fb1)提交显示了转换的差异。最终的演示应用可以在[这里](https://stimulus-demo.herokuapp.com/)找到。

涉及 3 个文件:

## [HTML 文件](https://github.com/dstull/stimulus-demo/blob/master/app/views/hello/index.html)

这里值得注意的变化是对目标、控制器和动作的数据属性的利用。该动作特别定义了 javascript 事件 keyup 以及它将调用的控制器和函数。

## [应用 JS 文件](https://github.com/dstull/stimulus-demo/blob/master/app/javascript/packs/application.js)

关于这个文件没有太多要说的，因为它是刺激使用的入口点所需的基本布线。

## [你好控制器 JS 文件](https://github.com/dstull/stimulus-demo/blob/master/app/javascript/controllers/hello_controller.js)

在这里，我演示了从原始 CoffeeScript 到刺激的转换。

# 结论

当您可以用它来描述数据属性中的事件处理程序，而不是用 JavaScript 来处理它们时，Stimulus 就大放异彩了。如果您正在使用 html 的服务器端呈现，这个框架可以真正帮助您组织 javascript 和管理事件。