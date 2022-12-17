# 使用 SASS 部分

> 原文：<https://dev.to/sarah_chima/using-sass-partials-7mh>

样式表随着时间的推移变得越来越大。它们越长越难维护。将大的样式表分成小块才有意义。Sass 中的 Partials 帮助我们在不影响性能的情况下将文件分成小文件。

partial 只是一个带下划线的 Sass 文件。比如`_name-of-file.scss`。下划线告诉 Sass 该文件是部分文件，不应该被编译成 CSS。然后，可以将这部分内容导入到另一个将被编译成 CSS 的文件中。根据您想要构建 Sass 项目的方式，partial 可以包含项目中使用的所有变量、函数或 mixins，也可以是特定的页面或页面组件。

使用`@import`指令导入部分。CSS 中也有`@import`指令。然而，它们是不同的。每当在 CSS 中使用`import`语句时，都会向服务器发出 http 请求。这增加了请求的资源数量，并对网页的性能产生负面影响。萨斯不会这么做。相反，它获取您想要导入的文件，并将其与您要导入的文件合并，这样您就可以将单个 CSS 文件提供给 web 浏览器。所以这不影响性能。将片段导入到其中的文件称为清单文件。

让我们用一个假想的目录来解释这一点。

```
 |-- application.scss    // Sass manifest file
    |
    |-- _reset.sass         // Partials
    |-- _variables.scss             |
    |-- _functions.scss             |
    |-- _mixins.scss                |
    |-- _base.scss                  |
    |-- _buttons.scss               |
    |-- _forms.sass                 |
    |-- _modules.sass               |
    |-- _theme.sass 
```

Enter fullscreen mode Exit fullscreen mode

在目录中，您可以看到片段和清单文件，所有片段都将被导入其中。在清单文件中，您可以导入如下所示的片段。

```
 //application.scss

    @import "reset";
    @import "variables";
    @import "functions";
    @import "mixins";
    @import "base";
    @import "buttons"; 
```

Enter fullscreen mode Exit fullscreen mode

注意没有添加`.scss`扩展名。这是因为萨斯足够聪明，能够明白这一点。
所有的文件不必在同一个文件夹中。您可以将文件排列在文件夹中，然后适当地导入。使用文件夹可以让您的文件更有条理。这里有一个例子。

```
 |– base/ 
    |   |– _reset.scss       # Reset/normalize 
    |   |– _typography.scss  # Typography rules 
    |   ...                  # etc
    | 
    |– components/ 
    |   |– _buttons.scss     # Buttons 
    |   |– _navigation.scss  # Navigation
    |   |– _dropdown.scss    # Dropdown  
    |   ...                  # etc 
    |...
    |
    main.scss                #manifest file 
```

Enter fullscreen mode Exit fullscreen mode

然后，可以将这些文件导入 main.scss 文件中。

```
 //main.scss

    /* base */
    @import "base/reset";
    @import "base/typography";

    /* components */
    @import "components/buttons";
    @import "components/nav";
    @import "components/dropdown"; 
```

Enter fullscreen mode Exit fullscreen mode

分部极大地帮助我们组织我们的 CSS 文件。有了项目的架构，将 CSS 文件分解成部分文件就容易多了。这可以更好地维护和管理您的 CSS 文件。

在使用偏旁音时，你发现了哪些有用的技巧？

有什么补充或问题吗？留下评论。

感谢您的阅读。:)