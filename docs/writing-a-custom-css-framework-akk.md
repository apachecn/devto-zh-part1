# 编写自定义 CSS 框架

> 原文：<https://dev.to/kbariotis/writing-a-custom-css-framework-akk>

写 CSS 很难！尤其是跨浏览器测试，这就像是有史以来最不好玩的游戏。但是，这仍然是我们日常生活中的一件大事，你总是对结果感觉良好。

就我个人而言，我正在重用我能从互联网和开源社区获得的任何东西。 [Bootstrap](http://getbootstrap.com) 就是这样一个例子。Bootstrap 是一套很好的可重用组件，你可以直接插入到你的网站中。问题是它很大，通常你不需要它提供的所有[组件](http://getbootstrap.com/components/)。

此外，最新版本的 Bootstrap 仍然需要 jQuery，这是我试图避免的。特别是对于只有几个点击事件和一个联系表单的小项目，这真的没有必要。

所以，像一个真正的黑客一样，我使用 SASS 来构建我的 CSS 文件，我的主文件最初看起来是这样的:

```
$bootstrap-sass-asset-helper: true;

@import "~bootstrap-sa../static/stylesheets/bootstrap/variables";
@import "~bootstrap-sa../static/stylesheets/bootstrap/mixins";
@import "~bootstrap-sa../static/stylesheets/bootstrap/normalize";
@import "~bootstrap-sa../static/stylesheets/bootstrap/print";
@import "~bootstrap-sa../static/stylesheets/bootstrap/glyphicons";
@import "~bootstrap-sa../static/stylesheets/bootstrap/scaffolding";
@import "~bootstrap-sa../static/stylesheets/bootstrap/type";
@import "~bootstrap-sa../static/stylesheets/bootstrap/grid";
@import "~bootstrap-sa../static/stylesheets/bootstrap/forms";
@import "~bootstrap-sa../static/stylesheets/bootstrap/buttons";
@import "~bootstrap-sa../static/stylesheets/bootstrap/utilities";
@import "~bootstrap-sa../static/stylesheets/bootstrap/responsive-utilities"; 
```

Enter fullscreen mode Exit fullscreen mode

我使用 [webpack](https://webpack.js.org) 来解析文件。这里有一个配置 [webpack 和引导程序](https://github.com/kbariotis/webpack-patterns)的例子。

最终文件将只包含我需要的基本组件。你可能已经注意到我没有包括任何需要 Javascript 的组件，因此 jQuery 也没有。我也不打算包含 Bootstrap 的 Javascript 文件。

太好了，现在让我们写一些 HTML:

```
<div class="container posts-container">
  <div class="row posts-row">
    <div class="col-sm-6 col-md-3 posts-row__post">
      <h1 class="post__title">Post title</h1>
    </div>
    <div class="col-sm-6 col-md-3 posts-row__post">
      <h1 class="post__title">Another blog post</h1>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

好的，它起作用了。但是，Bootstrap 的网格系统和我们的 HTML 代码有很强的耦合性。因为 Bootstrap 是一个 CSS 框架，只有我们的 CSS 代码应该处理它。这样我就可以在将来删除 Bootstrap，只需要修改我的 CSS 代码，而不是 HTML。

我们来改写:

```
<div class="posts-container">
  <div class="posts-row">
    <div class="posts-row__post">
      <h1 class="post__title">Post title</h1>
    </div>
    <div class="posts-row__post">
      <h1 class="post__title">Another blog post</h1>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

是的，更加清晰，易读，面向领域。现在让我们用 Bootstrap 把它绑在一起:

```
.posts-container {
  @extend .container;
}

.posts-row {
  @include make-row();
}

.posts-row__post {
  @include make-sm-column(6);
  @include make-md-column(3);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们要么扩展 Bootstrap 的类，要么使用它的 [mixins](https://github.com/twbs/bootstrap-sass/blob/mast../static/stylesheets/bootstrap/mixins/_grid.scss) 。

现在只有我们 CSS 知道 Bootstrap。HTML 不在乎。Bootstrap 可以是任何你喜欢的 CSS 框架。

例如，如果我要切换到 Flexbox，我会这样做:

```
.posts-row {
  display: flex;
  flex-wrap: wrap;
}

.posts-row__post {
  flex: 1 0 200px;
  box-sizing: border-box;
  padding: 10px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我总是这样为一个项目创建 CSS 代码库。它帮助我快速创建原型，一旦项目达到更成熟的阶段，我就可以开始重写并抛弃依赖关系。

总之，尽量保持你的代码库干净，不要混淆。在你的头脑中有一个未来将如何发展的愿景。一旦你有了这些，你就可以开始思考你能在未来简化这个过程。

如果你喜欢我开始一个新的定制 CSS 框架的方法，请给我留言。