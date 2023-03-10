# 使用 ng-bootstrap 时如何定制 Bootstrap 样式和变量

> 原文：<https://dev.to/teroauralinna/how-to-customize-bootstrap-styles-and-variables-when-using-ng-bootstrap-99l>

[Ng-bootstrap](https://ng-bootstrap.github.io) 是一个将 Bootstrap 4 组件集成到 Angular 的伟大项目。主要的好处是它不依赖于第三方 Javascript 库，如 jQuery 或 Bootstrap JS。

安装很容易，但是[安装说明](https://ng-bootstrap.github.io/#/getting-started)缺少重要的一点，如何使引导样式可编辑。

## 下面是如何做的步骤

这些步骤适用于 Angular 版本 5，并期望您使用 Angular CLI。尽管即将到来的和更早的版本应该也能工作。

通过运行以下命令或直接编辑`.angular-cli.json`(styleExt)，改变角度样式以使用 Sass。

```
ng set defaults.styleExt scss 
```

在`src`下创建一个名为`sass`的文件夹(或者你想叫它什么样式文件夹都行)。创建两个文件进入`src/sass`；`styles.scss`和`_variables.scss`。

之后，修改`.angular-cli.json`并将样式值改为如下。

```
"styles": [
  "sass/styles.scss"
] 
```

将所需的引导 SCSS 文件导入到您的`styles.scss`文件中。

```
// Custom variables
@import "variables";

// Bootstrap
@import "~bootstrap/scss/functions";
@import "~bootstrap/scss/variables";
@import "~bootstrap/scss/mixins";
@import "~bootstrap/scss/print";
@import "~bootstrap/scss/reboot";
@import "~bootstrap/scss/type";
@import "~bootstrap/scss/images";
//@import "~bootstrap/scss/code";
@import "~bootstrap/scss/grid";
//@import "~bootstrap/scss/tables";
//@import "~bootstrap/scss/forms";
//@import "~bootstrap/scss/buttons";
//@import "~bootstrap/scss/transitions";
//@import "~bootstrap/scss/dropdown";
//@import "~bootstrap/scss/button-group";
//@import "~bootstrap/scss/input-group";
//@import "~bootstrap/scss/custom-forms";
//@import "~bootstrap/scss/nav";
//@import "~bootstrap/scss/navbar";
//@import "~bootstrap/scss/card";
//@import "~bootstrap/scss/breadcrumb";
//@import "~bootstrap/scss/pagination";
//@import "~bootstrap/scss/badge";
//@import "~bootstrap/scss/jumbotron";
//@import "~bootstrap/scss/alert";
//@import "~bootstrap/scss/progress";
//@import "~bootstrap/scss/media";
//@import "~bootstrap/scss/list-group";
//@import "~bootstrap/scss/close";
//@import "~bootstrap/scss/modal";
//@import "~bootstrap/scss/tooltip";
//@import "~bootstrap/scss/popover";
//@import "~bootstrap/scss/carousel";
@import "~bootstrap/scss/utilities";

// Customization
@import "my-component"; 
```

就是这样！现在可以修改引导变量并选择要使用的组件。您还可以轻松地将自己的全局自定义组件添加到项目中。

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2017/how-to-customize-bootstrap-styles-and-variables-when-using-ng-bootstrap)