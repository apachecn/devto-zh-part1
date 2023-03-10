# 组件驱动的 Web 开发的好处

> 原文：<https://dev.to/fregu/the-benefits-of-component-driven-web-development>

网络由积木组成。当我们决定在 HTML 代码中使用`<a>` anchor 标签时，我们希望它表现得像一个链接，看起来很漂亮并且是蓝色的(或者访问后是紫色的)，当鼠标指针悬停时显示一个小指针，当激活时获得 tab 焦点，最重要的是当点击时将用户带到一个新的 URL。所有这些都不需要我们自己编写任何样式或逻辑。同样的事情也适用于任何其他 HTML 元素，如按钮，输入字段，标题，段落等。这些既有语义又有语法意义，使得 HTML 代码易于编写、阅读和理解。

通过组合 HTML 标签集、一些文本内容、类名和其他属性，我们可以使用 CSS 和/或 JavaScript 来改变默认的外观和感觉，从而定义我们自己的 HTML 外观和行为模式。这有时会导致非常复杂的结构，甚至更复杂的样式表和 JavaScripts，开发人员需要理解这些，以便生成正确的标记，从而在 UI 框架中获得预期的行为。

在开发一个新网站的早期阶段，将所有这些信息保存在开发人员(或者在某些情况下是开发人员)的头脑中可能会很好，那时它似乎可以很好地协同工作，一切都是独角兽和彩虹。但是过一段时间后，当在一些遗留的 web 浏览器中发现错误时，或者仅仅是因为前提条件可能改变，由于设计的改变，框架通常需要修改。没问题，我们的开发人员是一个天才，他/她可以对一些 CSS 类、JS 语句或 HTML 语法进行微小的修改，以使框架适应新的条件。经过框架中这些小小的意外变化的几次迭代，代码清晰的结构和可读性不再像以前那样清晰。其他开发人员开始放弃做出任何改变，因为这可能会(再次)破坏某些东西。你会越来越依赖于你的开发人员，他(每个人都同意)现在是框架的唯一统治者。

当这种情况在开发中发生时，我们通常使用术语技术债务。技术部门是工作代码和可维护(可读)代码之间差距的代价，它随着你做的每一个小的“解决方案”而增长，这可能使一些未来的改变更难做。大多数开发项目都有严格的时间表，并且处于一个敏捷的过程中，在设计或功能上有许多变化，每个人都关注你花了多少时间和圣杯的发布日期。技术债务只是不断蔓延，可能感觉无法避免。

## 逃避债务

可维护代码的秘密并不像你想象的那么大。正如 web 开发中的大多数事情一样，获得更好控制的最好方法是将其分解成更小、定义更清晰的组件。不是让大的 JavaScript 函数做很多事情，而是把它分解成几个更小的纯函数，所有的函数只做一件事情。这就是函数式编程的方法，函数越小，bug 越小，越容易修复。同样的原则也适用于你的前端框架的整个 JavaScript、CSS 和 HTML 栈。

让我们一次一部分地讨论一下如何做到这一点。

### ES6 模块

JavaScript 在 EcmaScript 2015 标准中引入了(ES6)模块的概念，这基本上是一种从其他文件导入特定函数的方法。有了这些模块，我们可以轻松地将 JavaScript 函数分成单独的文件，每个文件都有一个单独的用途，这使得查找、读取和修改任何单个文件的内容变得更加容易。

每个模块可以定义一个单独的默认函数来导出，作为其他文件使用该模块的公共 API。只要这个函数的参数保持不变，并且它的预期输出保持不变，就可以很容易地修改或更新每个模块的内容，而不会导致应用程序的任何其他部分中断。

```
import myCustomEventHandler from '../helpers/customEventHandler';
/** Sets the variable myCustomEventHandler to the default exported function in "../helpers/customEventHandler.js" which later can be called just as a local variable myCustomEventHandler(â€¦);
**/
export default function mySpecialComponent (el, options) {â€¦}
/* Exports the function mySpecialComponent as default function for others to import */ 
```

Enter fullscreen mode Exit fullscreen mode

要深入了解 ES6 模块，请看:[http://exploringjs.com/es6/ch_modules.html](http://exploringjs.com/es6/ch_modules.html)

由于 ES6 模块是 EcmaScript 2015 标准的一部分，它还没有在几个浏览器中实现，可能需要一些帮助才能让它在任何地方工作。

### 通天塔

Babel(原名 6to5)是一个 transpiler，将 EcmaScript 2015 等未来主义风格的代码翻译成 EcmaScript 5(跨浏览器兼容的 JavaScript)。除了 ES6 模块，Babel 还可以使用 EcmaScript 2015 的其他功能，如:const，let，arrow 函数，destructuring，spread 操作符，map()，forEach()，filter()，reduce()等等。要了解 EcmaScript 2015 的所有荣耀，请访问 http://exploringjs.com/es6/和 https://babeljs.io/[的](https://babeljs.io/)。

既然我们已经将 JavaScript 分解成了极小的明确命名和定义的文件，那么让我们看看在没有样式表的情况下可以做些什么。

## 面向对象的 CSS

BEM 已经成为实现面向对象 CSS 的一种广泛流行的语法，它通过建议一种 HTML 中的类名命名标准来定义和样式；块(组件)、元素(组件子元素)和修改器(组件变体)。

面向对象的 CSS 的思想是创建描述性的、单一用途的类名，这使得每个类名是什么以及在哪里定义它变得非常清楚。BEM 类应该只用于样式设计，而不应该用于不相关的样式设计或者用于将 JavaScript 绑定到 DOM，因为这会导致不明确的依赖关系。与 ES6 modules for JavaScript 一样，这个想法是因为每个类名只用于一个目的，所以更改或更新它的定义只会影响具有其预期目的的预期元素。

**块**类名是指组件名，通常用于任何给定组件块的包装元素。

```
.MyComponent {} /* Block class name */ 
```

Enter fullscreen mode Exit fullscreen mode

**元素**类名用于定义属于块组件的相关子元素。

```
.MyComponent-header {} /* Sub element class name */ 
```

Enter fullscreen mode Exit fullscreen mode

**修饰符**类名是块类名的版本，用于默认块样式的变体。

```
.MyComponent--wide {} /* Modifier class name */ 
```

Enter fullscreen mode Exit fullscreen mode

通过在块上定义所有修改器，可以在每个修改器下设置不同的元素样式。但是通过阅读 CSS 可以清楚地知道哪些修饰符是可用的。

```
.Component--wide .Component-header {} /* Element class name with block modifier */ 
```

Enter fullscreen mode Exit fullscreen mode

我更喜欢使用 SUIT 语法([https://suitcss.github.io/](https://suitcss.github.io/))，这是一个使用 PascalCase 命名的 BEM 的流行实现。

通过使特定组件的所有样式成为同一个块类的一部分，组件可以完全独立于其他框架样式，从而使调试和开发更加容易。

#### 变量和工具类

有些样式在每个组件中重复是没有意义的，比如颜色、排版、间距、布局、断点、动画等等。在这种情况下，我们可以使用组件可以使用的全局变量，例如:

```
:root {
  --color-deep-blue: #000080;
}
/* And then later used like this */
color: var(--color-deep-blue); 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，特定的实用程序类可能更适合您，这些实用程序类是具有单一、非常具体的用途的单个元素类。比如设置特定于视窗的样式或某种排版。

```
<p class="text-small s-max-hidden color-dark-red">Example of utility class names defining a certain small typography, with red color which is hidden on small viewports.</p> 
```

Enter fullscreen mode Exit fullscreen mode

具有良好名称的实用程序类和变量使新开发人员清楚地知道哪些样式适用于任何给定的元素，并使更新组件或更改实用程序类变得更容易，而不会在其他地方引起新的错误。

### PostCSS

CSS 变量是一个相对较新的标准，尚未在所有浏览器中实现(并且在文件之间不可用)，但是通过使用 CSS 的 transpiler(就像我们的 JavaScript 的 Babel 一样)，我们今天可以使用这些功能。

它还使得编写干净的标准化 CSS 和信任 PostCSS 来管理变量、供应商前缀、计算和自定义断点以及将所有 CSS 文件捆绑在一起变得容易。

开始使用 PostCSS 结帐[http://postcss.org/](http://postcss.org/)和[http://cssnext.io/](http://cssnext.io/)。

## HTML 模板

维护一个大框架的最大问题之一是管理网站中所有实现的 HTML 代码。在许多情况下，网站某个部分的修复、错误或设计更新可能会导致标记的变化。更改 HTML 通常是一个大禁忌，这使得重新设计和修复错误几乎不可能，因为 HTML 需要在整个应用程序中(有时)数百个地方进行更新。

大多数服务器端实现使用某种模板语言来为客户端生成 HTML，通常带有过多的逻辑和后端功能，以表示类似组件设计的东西。但是想象一下，如果您可以将应用程序的逻辑层从表示层中分离出来，让后端专注于为纯设计模板生成数据。

有很多模板语言，都有自己的一套特性和限制，但大多数都关注同一件事；从一组数据变量生成 HTML。许多甚至支持多种平台和后端语言，使得构建 HTML 模板成为可能，这些模板可以在具有静态 JSON 文件的客户端上运行，就像在具有动态数据库内容的后端上运行一样。

关于组件驱动的开发，模板语言最重要的特性之一是模板片段的使用。

模板部分是将其他模板文件作为页面的一部分包含进来的一种方式。通过实现对服务器使用模板部分的支持，而不是编写 HTML，我们可以修改任何模板部分，而无需对后端进行任何更改。并且对模板的所有更改都将在使用分部的任何地方实现。

几乎任何平台都支持的最简单的模板语言之一是 Mustache([https://Mustache . github . io](https://mustache.github.io))。但我更喜欢，如果可能的话，这是密切相关的，但更复杂一点的表弟车把(【http://handlebarsjs.com】T2)。

### 车把

Handlebars 是 Mustache 的一个实现，增加了一些额外的功能，使其易于使用，并可根据每个项目的特定需求进行定制。Handlebars 对服务器端语言有很强的(有时是非官方的)支持，通常比 JavaScript 版本更受限制，但在大多数情况下绝对足够好。

就像我们将 JavaScript 和 CSS 分解成更小的模块一样，我们可以轻松地为每个组件编写 Handlebars 模板，并使用不同的数据变量集对它们进行配置。

就像小胡子一样，Handlebars 使用花括号来定义变量`{{myVar}}`、块助手`{{#each myArr}}â€¦{{/each}}`和模板部分`{{> MyComponent}}`。

```
<section class="Component {{#each modifier}} Component--{{this}}{{/each}}">
  <header class="Component-header">
    {{#with title}}
      {{> Title class="Component-title"}}
    {{/with}}
  </header>
  <div class="Component-content">
    {{#each content}}
      <p class="text-plain">{{{this}}}</p>
    {{/each}}
  </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

为每个模板编写一个简单的 json 数据结构，我们可以呈现我们的模板，以确保它们与 CSS 和 JavaScript 一起很好地工作，而且还定义了后端需要实现以正确使用组件的预期数据。

```
{  "modifier":  ["wide"],  "title":  "My awesome component",  "content":  [  "Hello component",  "i dig your style"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 文件结构

现在我们有了 JavaScript、CSS、模板和 JSON 的独立文件，我们只需要一个地方来放置它们。既然它们都与特定的组件相关，为什么不把它们放在一起呢？在同一个文件夹中使用 html、css 和 js 的好处是，如果你将文件命名为 index.css、index.js 或 index.html，当从其他文件导入该文件夹时，它们将被假定为默认文件。这样，通过使用 CSS 或 JS 中的 import 语句，导入组件文件夹将获取正确的索引文件。

在 JavaScript 中，我们可以包含这样一个组件。

```
import MyComponent from '../components/MyComponent'; 
```

Enter fullscreen mode Exit fullscreen mode

并且在 CSS 中

```
@import url('../components/MyComponent'); 
```

Enter fullscreen mode Exit fullscreen mode

这很容易在 handlebars 中实现，也使得只包含组件文件夹名称成为可能，以使用实际的 index.hbs 文件。

```
components/MyComponent
  - index.css
  - index.js
  - index.hbs
  - default.json
  - readme.md 
```

Enter fullscreen mode Exit fullscreen mode

现在我猜你正在想象你的代码编辑器中到处都是索引文件的混乱。我不会说谎，很难相信你的编辑器选项卡，但大多数现代编辑器都有简单的快速查找功能，可以根据目录名轻松地在文件之间跳转。

获得组件的标准化结构的另一个令人兴奋的部分是，现在很容易构建一个解析器来读取所有文件，并以组件库这样的样式指南来呈现它们，我稍后将回到这一点。

有不同种类的组件。有些很小，可重复使用，有些很麻烦，只使用一次，有些甚至没有任何模板文件，只有 CSS 或 JS，就像我们在 CSS 部分讨论的实用程序类。

在我的项目中，我为不同种类的组件建立了一个文件夹结构，如下所示。

**/base** 包含设计中的基本要素，如颜色、字体、动画等。这些组件大多是实用程序组件，定义 CSS 变量和样式实用程序类。

**/components** 包含所有可重用的公共组件，这些组件(理论上)可以在应用程序中的任何地方使用。

**/modules** 包含更复杂且通常使用一次或两次的组件，如 PageHeader 和 PageFooter。

**/helpers** 包含可以被其他组件使用的助手组件，这可以是手柄循环部分，或者主要是不依赖于任何特定组件的 JavaScript 函数。

**/views** 包含用于创建页面模板的组件或组件组合。

**/layouts** 包含所有或大多数视图组件使用的一个或多个根文档模板。

我们现在拥有的是一个独立于任何后端实现的 UI 组件架构。但是几乎任何服务器端语言都很容易使用，只需要一些小的路径配置。我希望你能意识到与后端完全分离的 UI 管理的优势。这意味着，只要后端为组件正确地准备了数据，组件就可以与服务器实现并行地修改和更新，而将那些瀑布式流程留在它们所属的过去。

为了处理这些组件，与后端分开，我们需要一个简单的原型服务器，用我们的 json 数据呈现组件。

## 启动并运行原型

有了如上所述的组件结构，我们所需要的就是一个文件解析器、模板渲染器和 HTTP 服务器，以使我们的文件夹结构成为一个工作原型。

你可以用任何服务器端语言来做这件事，但是作为前端开发人员，我更喜欢用 NodeJS。

使用 nodeJS，我可以设置一个 KoaJS([http://koajs.com/](http://koajs.com/))服务器渲染手柄，并配置组件文件夹的路径。然后我可以设置 URL 规则，使它们与我的视图组件的名称一致。但我没有一步一步地教你如何设置，而是像任何一个优秀的电视厨师一样，为你准备了这个。

## 生活设计系统

我们称之为活的设计系统，因为它的核心目标是创建一个能在网站发布后存活下来的设计系统。这是一个运行解析器、服务器和样式向导的工具，它将呈现与服务器实现完全相同的 UI 代码，它还为您提供了一些不错的其他工具作为额外的奖励。

它是我和我在 Daytona Sthlm (daytona.se)的同事开发的。它是 100%开源的，可以免费下载和使用，因为它仍处于早期测试阶段，不同版本之间可能会有变化。

使用 npm 安装活设计系统(简称 LDS)。

```
$ npm install -g @daytona/living-design-system 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的计算机上全局安装 lds，使名称空间“LDS”全局可用，并允许您在任何地方启动项目，而无需在项目中安装任何其他包。安装完软件包后，创建一个新文件夹并运行:

```
$ lds init 
```

Enter fullscreen mode Exit fullscreen mode

这将使用我们刚刚完成的设置建立一个新项目。

还有跑步:

```
$ lds watch 
```

Enter fullscreen mode Exit fullscreen mode

将启动一个服务器，在 [http://localhost:4000](http://localhost:4000) 上运行一个简单的起始页，在[http://localhost:4000/styleguide](http://localhost:4000/styleguide)上运行一个 style guide。watch 命令将启动服务器，传输和捆绑您的资产，观察您的文件的任何更改，并在任何文件更改时自动重新解析和重新加载您的浏览器或样式表链接。

请尝试一下，创建你自己的 som 组件和视图，看看组件驱动开发如何适合你。

这是一个我觉得很舒服的设置，但它完全可以自由配置你自己的首选项来处理 CSS、JS、图像和模板语言。要了解 LDS 做什么以及如何配置它，请看[https://www.npmjs.com/package/@daytona/living-design-system](https://www.npmjs.com/package/@daytona/living-design-system)。

如果您想进一步了解如何在您的组织中使用 LDS 来控制数字品牌，并以实用的风格指南展示，请随时联系我。

## 总结

将 UI 分解成组件很有意义，因为这使得阅读、开发和更新代码更加容易。如果您想要更改或替换一个组件，只有组件目录中的文件会影响组件的外观和行为。

我们甚至可以独立开发和测试组件，以确保它们在不同数据集下的所有设备中都能正常工作。

每个组件都可以由自己的 CSS 和 BEM 语法组成，使其易于理解，ES6 JavaScript 模块有明确的目的，只导出初始化组件所需的内容。我们还将 HTML 封装在模板 partial 中，使后端实现成为 UI 用户，而不是 UI 所有者。通过添加一些额外的信息，如示例数据、文档、配置模式和屏幕截图，我们可以提供一个漂亮的风格指南，其中包含理解如何使用、开发和扩展组件所需的所有信息。

通过控制 UI 开发和维护，你可能会成功地拥有一个 UI 库，并在下一次网站发布时继续存在。

如果你只是想自己尝试一下，那就请便，前往[https://www.npmjs.com/package/@daytona/living-design-system](https://www.npmjs.com/package/@daytona/living-design-system)，你将有望意识到为什么这是一个好主意。