# 使用 Turbolinks、select2 和 formValidation 的激励

> 原文：<https://dev.to/dstull/stimulus-with-turbolinks-select2-and-formvalidation-1i7l>

# 目标

展示了使用名为 [stimulus](https://stimulusjs.org/) 的新适度 js 框架实现[select 2](https://select2.org/)/[form validation](http://formvalidation.io/)/[turbo links](https://github.com/turbolinks/turbolinks)前后的情况

我在这里使用 Rails 作为我的框架，但是设置实际上与框架无关。然而，这展示了一个没有前端框架分离的服务器端呈现 html 的良好用例。

我将把它分成两步:

*   无刺激实施
*   刺激实施

# 实现无刺激

为了完成这一部分，我创建了一个简单的项目。涉及到两个文件:

## [Erb 文件](https://github.com/dstull/stimulus-formvalidation/blob/without-stimulus/app/views/cars/_form.html.erb)

在这个文件中，我遵循了使用 FormValidation 的[声明性/html5 数据属性](http://formvalidation.io/examples/attribute/)方式，因为我更喜欢尽可能保持 javascript 的通用性。

## [CoffeeScript 文件](https://github.com/dstull/stimulus-formvalidation/blob/without-stimulus/app/assets/javascripts/cars.coffee)

我在这里演示:

*   Select2 with normal、multiple 和 tags with createTag 作为约束允许输入的方式
*   Select2 上的表单验证
*   对名称字段进行自定义回调的表单验证
*   触发 select2 更改事件时使用重新验证和隐藏输入更改的表单验证
*   在 Turbolinks 缓存页面之前从 DOM 中删除 Select2，这样可以防止页面中出现两个 Select2

# 用刺激实现

为了完成这一部分，我使用了主分支。[这个](https://github.com/dstull/stimulus-formvalidation/commit/1739fc0ae05a739a6b4a3cb77c547085209c819f)提交显示了转换的差异。最终的演示应用可以在[这里](https://stimulus-formvalidation.herokuapp.com/cars)找到。

涉及 3 个文件:

## [Erb 文件](https://github.com/dstull/stimulus-formvalidation/blob/master/app/views/cars/_form.html.erb)

这里值得注意的变化是对目标、控制器和占位符的数据属性的利用。

## [应用 JS 文件](https://github.com/dstull/stimulus-formvalidation/blob/master/app/javascript/packs/application.js)

关于这个文件没有太多要说的，因为它是刺激使用的入口点所需的基本布线。

## [汽车控制器 JS 文件](https://github.com/dstull/stimulus-formvalidation/blob/master/app/javascript/controllers/cars_controller.js)

我在这里演示:

*   从原始 CoffeeScript 转换为刺激
*   额外存储选定的项目，以便它们保留在前进和后退按钮上

# 结论

我真的很喜欢它提供了一种简单的方法来利用数据属性并轻松地与 Javascript 挂钩。虽然我没有在这里展示它，但在另一篇博客中会展示，当您可以用它来描述数据属性中的事件处理程序，而不是用 JavaScript 来处理它们时，Stimulus 确实大放异彩。

我鼓励人们去看看。正如这里可以看到的，您可以使用它轻松地挂钩到现有的 jquery 库。

感谢[这个回购](https://github.com/pascallaliberte/stimulus-turbolinks-select2)让我度过了最初的一些低谷。