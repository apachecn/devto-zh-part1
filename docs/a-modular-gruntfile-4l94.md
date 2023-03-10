# 模块化的 Gruntfile

> 原文：<https://dev.to/kjir/a-modular-gruntfile-4l94>

Grunt 是一个非常强大的任务运行器，如果你正在用 Javascript 开发，你可能正在使用它。它的配置文件定义了所有可用的任务以及它们应该做什么，但是您有没有感觉到即使对于中等规模的项目，它也很容易变得太大而难以管理？

我也有这种感觉，所以我决定在 [load-grunt-config](http://firstandthird.github.io/load-grunt-config/) 的帮助下把它分成许多更小的配置文件。按照[保罗·巴卡乌斯](https://paulbakaus.com/)在[关于 html5rocks](http://www.html5rocks.com/en/tutorials/tooling/supercharging-your-gruntfile/) 的文章中给出的优秀建议，我想出了一个更干净、更易于管理的设置。

在这里，我将尝试在他的文章的基础上，添加一些关于我在设置中使用的有用技巧的更多细节。

## YAML 的短暂

作为一名 Python 开发者，我喜欢简洁的语法。为什么要为不必要的括号、逗号或引号而烦恼呢？它们会使你的文件变得杂乱无章，让人更难读懂。因此，对于配置文件来说，从 Javascript 语法转移到 YAML 语法对我来说是一大解脱！

支持许多不同种类的语法文件，它们可以混合使用，这让我可以大大减少配置中的干扰。如果我最初有一个这样的`Gruntfile`:

```
module.exports = function (grunt) {
  var pkg = grunt.file.readJSON('package.json');

  grunt.initConfig({
    ngconstant: {
      options: {
        name: 'config',
        dest: 'app/config/config.js',
        constants: {
          ENV: {
            apiLocation: '',
            appName: 'MyApp',
            appVersion: pkg.version
          }
        }
      },
      development: {
        constants: {
          ENV: {
            apiLocation: 'http://localhost:3000/api'
          }
        }
      },
      staging: {
        constants: {
          ENV: {
            apiLocation: 'https://staging.example.com/api'
          }
        }
      },
      production: {
        constants: {
          ENV: {
            apiLocation: 'https://api.example.com'
          }
        }
      }
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

我现在将它作为`Gruntfile` :

```
module.exports = function (grunt) {
  require('load-grunt-config')(grunt);
}; 
```

Enter fullscreen mode Exit fullscreen mode

我将在`grunt/ngconstant.yaml` :
中创建一个 YAML 配置文件

```
options:
  name: 'config'
  dest: 'app/config/config.js'
  constants:
    ENV:
      apiLocation: ''
      appID: 'MyApp'
      appVersion: '<%=  package.version  %>'
  development:
    constants:
      ENV:
        apiLocation: 'http://localhost:3000/api'
  staging:
    constants:
      ENV:
        apiLocation: 'https://staging.example.com/api'
  production:
    constants:
      ENV:
        apiLocation: 'https://api.example.com' 
```

Enter fullscreen mode Exit fullscreen mode

这不是更干净吗？而且它也更短！

还要注意我是如何不必手动解析我的`package.json`来访问它的值的？这是`load-grunt-config`的另一个好用的功能。你所需要做的就是使用类似`'<%=package.version%>'`的简单模板来读取你的值。

## 任务间共享值

在我的项目中，我曾经拥有一些变量，这些变量包含一些由许多不同任务共享的值。让我们以文件列表为例，它可以被`concat`使用，也可以被`uglify`或`copy`或其他什么使用。于是就有了这样的东西:

```
module.exports = function (grunt) {

  var files = [

    // Application files
    'app/**/*.module.js',
    'app/module/**/*.js',
    'app/component/**/*.js',
    'app/service/**/*.js',
    'app/app.js',

    // Do not include tests in application build
    '!app/**/*.test.js',
    '!app/**/*_test.js'
  ];

  grunt.initConfig({
    jshint: {
      options: {
        jshintrc: true
      },
      all: {
        src: [files]
      }
    },
    uglify: {
      production: {
        src: files,
        dest: 'dist/app/js/<%= package.name %>.min.js',
        sourceMap: true
      }
    }
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

既然它们都存在于各自的文件中，我该如何在任务间共享这些值呢？首先，我创建了一个保存这些值的`grunt/project.yaml`文件:

```
files:
  # Application files
  - 'app/**/*.module.js'
  - 'app/module/**/*.js'
  - 'app/component/**/*.js'
  - 'app/service/**/*.js'
  - 'app/app.js'
    # Do not include tests in application build
  - '!app/**/*.test.js'
  - '!app/**/*_test.js' 
```

Enter fullscreen mode Exit fullscreen mode

下面是`grunt/jshint.yaml`文件的神奇之处:

```
options:
  jshintrc: true
all:
  src:
    - '<%=  project.files  %>' 
```

Enter fullscreen mode Exit fullscreen mode

`grunt/uglify.yaml` :
也是如此

```
production:
  src: '<%=  project.files  %>'
  dest: 'dist/app/js/<%=  package.name  %>.min.js'
  sourceMap: true 
```

Enter fullscreen mode Exit fullscreen mode

那很容易！

## 按环境分离配置文件

如你所见，在我的项目中，我使用 [grunt-ng-constant](https://github.com/werk85/grunt-ng-constant) 根据环境创建一个合适的配置文件。我还结合使用 [grunt-env](https://github.com/jsoverson/grunt-env) 和[grunt-预处理](https://github.com/jsoverson/grunt-preprocess)来根据环境不同地配置我的`index.html`文件。我希望有一个包含单个环境的所有配置的文件，这样就可以更容易地验证和更改它们，而`load-grunt-config`又一次拯救了我！

我使用了[配置分组](http://firstandthird.github.io/load-grunt-config/#toc7)来做到这一点:

```
# File: development-tasks.yaml
ngconstant:
  constants:
    ENV:
      appVersion: '<%=  package.version  %>-dev'
    apiLocation: 'https://devel.example.com/api'
env:
  ENV: 'DEVELOPMENT'
  THEME_CSS: 'css/mytheme.devel.css' 
```

Enter fullscreen mode Exit fullscreen mode

使用这种配置，我现在可以为每个环境创建一个单独的文件。`grunt/ngconstant.yaml`和`grunt/env.yaml`文件变成这样:

```
# File: grunt/ngconstant.yaml
options:
  name: 'config'
  dest: 'app/config/config.js'
  constants:
    # These are all default values
    ENV:
      appName: 'MyApp'
      appVersion: '<%=  package.version  %>'
    apiLocation: 'https://api.example.com/api/v1'

# File: grunt/env.yaml
options:
  add:
    THEME_CSS: 'css/mytheme.css' 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这里只有默认选项，没有特定于环境的配置。

我还想将特定于环境的配置文件从标准任务配置中分离出来，这样上下文就清晰了。所以我把它们都放在我创建的`config`目录中，并修改了我们的`Gruntfile.js` :

```
module.exports = function (grunt) {

  var path = require('path');

  require('load-grunt-config')(grunt, {
    configPath: [
      path.join(process.cwd(), 'grunt'), // Task settings here
      path.join(process.cwd(), 'config') // Environment specific settings here
    ],

    /*
     * If you want to change your setting locally without changing what is on
     * the repo, you can define your configuration overrides in config/override
     */
    overridePath: [
      path.join(process.cwd(), 'config', 'override')
    ],

  });

}; 
```

Enter fullscreen mode Exit fullscreen mode

`load-grunt-config`还支持覆盖，这是另一个很好的特性:当我设置它时，你可以在`config/override`中创建一个配置文件，它将覆盖任何先前设置的配置选项。这对于适应每个开发人员的需求是非常有用的，他们的需求可能会有一点不同，而不需要他们修改版本控制下的文件。相当整洁！

## 结论

是一个非常好的 grunt 插件，通过将 grunt 文件分割成许多更小的文件，它真正提高了 grunt 文件的质量。不仅如此:它增加了许多有用的特性，这些特性会让你想知道为什么还要编写简单的`Gruntfile`呢！

你在你的项目中使用`load-grunt-config`吗？我还错过了其他什么伟大的功能吗？请在下方留言评论！