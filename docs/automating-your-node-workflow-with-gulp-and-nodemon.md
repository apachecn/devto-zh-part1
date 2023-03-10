# 使用 Gulp 和 Nodemon 自动化您的节点工作流

> 原文：<https://dev.to/zak/automating-your-node-workflow-with-gulp-and-nodemon>

# 免责声明

请允许我在这篇文章的开头说，我是 web 开发的新手，这里的大多数老手可能已经知道这些技能。然而，当我开始为自己制作一个专业网站时，我发现这些东西非常有用，希望其他任何寻求帮助的新手也会发现这些信息也很有用。

## 你说的这个*【一饮而尽】*是什么？

根据他们的[网站](http://gulpjs.com/)，“gulp 是一个工具包，用于自动化开发工作流程中痛苦或耗时的任务，因此你可以停止浪费时间并构建一些东西。”简而言之，gulp 是一个任务运行器，可以帮你节省大量的时间和压力。

## 好吧，我为什么需要？

开发一个现代网站不是一件简单的任务。大多数项目使用 SASS 或更少，Bower，Browserify，如果你是用 CoffeeScript 或 Typescript 写的话，可能还要用到编译器，还有很多。在每一个微小的变化之后，都必须不断地编译样式表或 javascript，这是一种巨大的痛苦。当然，这些技术中的一些提供了某种“监视”功能，但是你仍然会有几个同时运行。Gulp 允许你将所有这些事情整合到一个任务中，在你每次做出改变时，重新编译你可能有的任何样式或脚本。

## 好吧，我被说服了。现在我该怎么做？

#### 编辑:[尼克·摩尔顿](https://dev.to/55sketch)在评论中指出， [Bulp](https://chrisdemars.github.io/bulp/) 是一个软件包，它提供了一个易于配置的样板 gulpfile 来帮助你开始。如果你不想完全从头开始写你的任务，去看看吧。

首先，你需要安装 gulp。这可以通过 npm 来实现，就像现在的大多数事情一样。

```
npm install gulp-cli -g 
```

Enter fullscreen mode Exit fullscreen mode

所有 gulp 任务都存储在 gulpfile 中，通常名为 gulpfile.js。以下是编译 scss 样式的 gulp 任务的示例。(示例源在此找到[。我添加的评论。)](https://www.npmjs.com/package/gulp-sass) 

```
'use strict';

var gulp = require('gulp');

//This is another package to install via npm
var sass = require('gulp-sass'); 

gulp.task('sass', function(){
    // '**/*.scss' recursively searches the specified directory for any files
    // with the .scss file extension.
    return gulp.src('./sass/**/*.scss')
        .pipe(sass().on('error', sass.logError)) //define error behavior
        .pipe(gulp.dest('./css'));               //specify compile destination
});

//This task watches for any changes to .scss files in the directory and
// runs the 'sass' task defined above whenever a change is detected.
gulp.task('sass:watch', function(){
    gulp.watch('./sass/**/*.scss', ['sass']);
}); 
```

Enter fullscreen mode Exit fullscreen mode

有太多的“囫囵吞枣”软件包可以让你自动完成几乎任何任务。下面是一个用 coffeescript 自动开发网页的任务集的例子。

```
var gulp = require('gulp');
var sass = require('gulp-sass');
var coffee = require('gulp-coffee');

gulp.task('sass', function(){
    return gulp.src('./styles/**/*.scss')
        .pipe(sass().on('error', sass.logError))
        .pipe(gulp.dest('./css'));
});

gulp.task('sass-dev', function(){
    gulp.watch('./styles/**/*.scss', ['sass']);
});

gulp.task('coffee', function(){
    return gulp.src('./app/**/*.coffee')
        .pipe(coffee({bare: true}))
        .pipe(gulp.dest('./js'));
});

gulp.task('coffee-dev', function(){
    gulp.watch('./app/**/*.coffee', ['coffee']);
});

// This means that the when 'gulp dev' is run, it runs the
// sass-dev and coffee-dev tasks
gulp.task('dev', ['sass-dev', 'coffee-dev']); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个 gulpfile，只需运行`gulp dev`就可以开始观察我们所有的风格和 coffeescript 文件。任何改变都会触发自动重新编译，我们不需要做任何事情。

## 但是等等，难道我们不需要在改变后端代码的时候重启我们的节点应用吗？

是的，但是这就是 nodemon 发挥作用的地方。要使用它，首先通过 npm 安装它。

```
npm install -g nodemon 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，我们只需用 nodemon 而不是 node 启动我们的应用程序，就像这样:

```
nodemon index.js 
```

Enter fullscreen mode Exit fullscreen mode

Nodemon 现在将监控我们的源代码，任何时候它看到一个变化，它会自动重启我们的应用程序。很可爱，对吧？现在我们所要做的就是刷新网页，看看我们的前端或后端的变化。

## 进一步阅读

这远不是对 gulp 的详尽解释，所以如果你想知道更多关于它如何工作和它提供什么，可以查看他们的网页和文档。我提供了我下面提到的所有包的链接，如果我引起了你的兴趣，请随意查看。

#### 引用的包

*   [查找](https://chrisdemars.github.io/bulp/)
*   [一饮而尽](http://gulpjs.com/)
*   [大口喘气](https://www.npmjs.com/package/gulp-sass)
*   [大口喝咖啡](https://github.com/contra/gulp-coffee)
*   [节点门](https://nodemon.io/)