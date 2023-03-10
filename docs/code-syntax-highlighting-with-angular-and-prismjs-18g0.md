# 使用 Angular 和 Prism.js 突出显示代码语法

> 原文：<https://dev.to/teroauralinna/code-syntax-highlighting-with-angular-and-prismjs-18g0>

我为我的博客实现了基于 [Prism.js](http://prismjs.com) 的代码语法高亮特性。这篇博文展示了如何将 Prism 与 Angular 一起使用。

> Prism 是一个轻量级、可扩展的语法荧光笔，按照现代网络标准构建。

**棱镜现有的角度解很少:**

*   [https://github.com/ngx-prism/core](https://github.com/ngx-prism/core)
*   [https://github.com/vaibhav93/angular-prism](https://github.com/vaibhav93/angular-prism)
*   [https://github.com/tpadjen/ng2-prism](https://github.com/tpadjen/ng2-prism)

但是我决定不使用现有的解决方案，因为所有的解决方案都使用一个需要代码块作为内容或参数的组件。我的博客内容是以 HTML 的形式从 REST API 获取的，所以我不能(轻易地)将代码块与其他内容分开。这就是为什么我决定看看如何在没有现有组件的情况下实现对 Prism 的支持。我使用 Prism 的 [highlightAll](https://github.com/PrismJS/prism/blob/71595beca7ab3c9f3d9ba633898d14e4671691f5/prism.js#L162) 方法一次性处理所有的 pre 和 code 标签。

执行以下步骤需要一个关于角度发展的基本知识。

## 1。从 npm 安装棱镜

```
$ npm install prismjs --save 
```

## 2。创建高亮服务

Prism.js 依赖项在此服务中导入。在这里你可以添加支持的语言和你想使用的额外的 Prism 插件。

```
import { Injectable, Inject } from '@angular/core';

import { PLATFORM_ID } from '@angular/core';
import { isPlatformBrowser } from '@angular/common';

import 'clipboard';

import 'prismjs';
import 'prismjs/plugins/toolbar/prism-toolbar';
import 'prismjs/plugins/copy-to-clipboard/prism-copy-to-clipboard';
import 'prismjs/components/prism-css';
import 'prismjs/components/prism-javascript';
import 'prismjs/components/prism-java';
import 'prismjs/components/prism-markup';
import 'prismjs/components/prism-typescript';
import 'prismjs/components/prism-sass';
import 'prismjs/components/prism-scss';

declare var Prism: any;

@Injectable()
export class HighlightService {

  constructor(@Inject(PLATFORM_ID) private platformId: Object) { }

  highlightAll() {
    if (isPlatformBrowser(this.platformId)) {
      Prism.highlightAll();
    }
  }
} 
```

我的实现目前不支持服务器端渲染。代码语法高亮只在浏览器中启用，因为`Prism.highlightAll`使用了 Node.js 中没有的`document`。可能有解决方法，但我认为代码高亮对服务器端渲染没有那么重要。

Prism 支持 130 种语言，所以我只导入我真正需要的语言。

## 3。在组件中使用高亮显示服务

然后，让我们将服务添加到一个组件中，该组件处理我们想要突出显示的数据。

生命周期钩子 [AfterViewChecked](https://angular.io/guide/lifecycle-hooks#afterview) 在这里用于当视图准备好时调用高亮服务。方法`ngAfterViewChecked`可能被调用多次。我使用`highlighted`布尔值来检查高亮显示是否已经完成，以防止多次`highlightAll`方法调用。

```
import { Component, OnInit, AfterViewChecked } from '@angular/core';

import { HighlightService } from '../shared/utils/highlight.service';

@Component({
  selector: 'app-blog-post',
  templateUrl: './blog-post.component.html',
  styleUrls: ['./blog-post.component.scss']
})
export class BlogPostComponent implements OnInit, AfterViewChecked {

  blogPost: BlogPostInterface;
  highlighted: boolean = false;

  constructor(
    ...
    private highlightService: HighlightService) {
  }

  /**
   * Highlight blog post when it's ready
   */
  ngAfterViewChecked() {
    if (this.blogPost && !this.highlighted) {
      this.highlightService.highlightAll();
      this.highlighted = true;
    }
  }

  /**
   * Fetch blog post from API
   */
  ngOnInit() {
    ...
  }
} 
```

这段代码只是如何在您自己的组件中使用最近创建的服务的部分示例。您需要调整自定义组件的用法。

## 4。添加棱柱样式

接下来将棱镜样式添加到您的`styles.scss`中。至少需要主题 CSS。检查我的[早期博客文章](https://dev.to/teroauralinna/how-to-customize-bootstrap-styles-and-variables-when-using-ng-bootstrap-99l)如何改变 Angular 使用 SCSS，而不是 CSS，如果你不使用 SCSS 已经。或者，您可以将 CSS 文件直接添加到您的 HTML 中。

```
@import "~prismjs/plugins/toolbar/prism-toolbar.css";
@import "~prismjs/themes/prism-okaidia"; 
```

* * *

现在应该可以用了。如果您对如何改进代码库有任何问题或建议，请随时评论这篇文章。

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2017/code-syntax-highlighting-with-angular-and-prismjs)