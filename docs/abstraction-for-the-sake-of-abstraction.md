# 为了抽象而抽象

> 原文：<https://dev.to/foresthoffman/abstraction-for-the-sake-of-abstraction>

我想谈谈任务运行器和自动化。就我个人而言，我已经利用 NPM 和格兰茨在多个项目中自动化重复的任务。这包括文件压缩、JavaScript/PHP 林挺、Sass 编译、脚本/样式缩小、运行 PHP/JS 单元测试。我甚至用它来保持镜像的 SVN 和 Git repos 同步。

刚刚开始一个新项目，我回到以前的项目进行复习。我不想重新发明轮子，所以我要从旧项目中寻找我需要的任何东西。回顾过去，我使用了以下 NPM 软件包:

1.  咕哝
2.  grunt-cli
3.  咕哝-并发
4.  咕噜-贡献-连接
5.  格朗特-伊比-综明
6.  grunt-基准-jshint
7.  咕噜-贡献-萨斯
8.  咕哝-贡献-丑陋
9.  咕噜声-贡献-观察
10.  咕噜-执行
11.  咕噜-摩卡试验
12.  负载-人工-任务
13.  时间咕噜声
14.  不然呢
15.  jsdom
16.  摩卡咖啡
17.  恰依茶

这看起来不算太糟，但后来我看了看 grunt 文件。它有 195 行长！我的另一个项目使用了更少的包，但是 Gruntfile 有 425 行长...是啊。

我突然意识到，我已经为这些项目投入了大量精力来使用 Grunt。原因是我正在学习使用 NPM 和咕噜之类的词。尽管它对学习自动化和如何编写单元测试很有用，但在其他方面它是不必要的。几乎所有我需要完成的任务都可以毫无怨言地完成。我意识到我是为了抽象而添加抽象。

当那个泡泡破裂时，我想起了 NPM 内置了使用`npm run-script`运行命令的功能。是的，我完全忘记了！

所以，我想，“这怎么可能只在 NPM 实现呢？”

下面是我对上述项目的想法，比较了使用 Grunt 和默认的 NPM 运行脚本特性。

### Sass 编译和缩小

咕哝设置:

```
require( 'load-grunt-tasks' )( grunt );

grunt.initConfig({
    paths: {
        sass: {
            dir: 'styles',
            files: '<%= paths.sass.dir %>/**/*.scss'
        },
        css: {
            dir: 'public/styles'
        },
    },
    sass: {
        options: {
            style: 'expanded'
        },
        dist: {
            files: [{
                expand: true,
                cwd: '<%= paths.sass.dir %>',
                src: ['**/*.scss'],
                dest: '<%= paths.css.dir %>',
                ext: '.css'
            }]
        }
    }
});

grunt.registerTask( 'scss', 'sass' ); 
```

Enter fullscreen mode Exit fullscreen mode

咕哝用法:

```
$ grunt scss 
```

Enter fullscreen mode Exit fullscreen mode

NPM 设置:

```
"scripts":  {  "sass":  "sass --scss -t compressed styles/*.scss public/styles/style.min.css"  } 
```

Enter fullscreen mode Exit fullscreen mode

NPM 用法:

```
$ npm run sass 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript 林挺、压缩和缩小

咕哝设置:

```
require( 'load-grunt-tasks' )( grunt );

grunt.initConfig({
    paths: {
        js: {
            source: 'scripts/*.js',
            public_dir: 'public/scripts/',
            public_dest: '<%= paths.js.public_dir %>main.js',
            public_ugly: '<%= paths.js.public_dir %>main.min.js',
            files: [
                '<%= paths.js.source %>',
                'Gruntfile.js',
                'test/**/*.js',
                '!test/utils/**/*.js'
            ]
        },
    },
    concat: {
        js: {
            src: '<%= paths.js.source %>',
            dest: '<%= paths.js.public_dest %>'
        }
    },
    uglify: {
        options: {
            mangle: {
                except: ['jQuery']
            }
        },
        target: {
            files: {
                '<%= paths.js.public_ugly %>': ['<%= paths.js.public_dest %>']
            }
        }
    },
    jshint: {
        options: {
            curly: true,
            eqeqeq: true,
            browser: true,
            devel: true,
            undef: true,
            unused: false,
            mocha: true,
            globals: {
                'jQuery': true,
                'module': true,
                'require': true,
                'window': true,
                'global': true
            }
        },
        dist: '<%= paths.js.files %>'
    }
});

grunt.registerTask( 'js', [ 'jshint', 'uglify', 'concat' ] ); 
```

Enter fullscreen mode Exit fullscreen mode

咕哝用法:

```
$ grunt js 
```

Enter fullscreen mode Exit fullscreen mode

NPM 设置:

```
"scripts":  {  "js":  "jshint scripts/*.js test/*.test.js && uglifyjs scripts/*.js -cmo public/scripts/word_search.min.js"  } 
```

Enter fullscreen mode Exit fullscreen mode

NPM 用法:

```
$ npm run js 
```

Enter fullscreen mode Exit fullscreen mode

### 摩卡单元测试

咕哝设置:

```
require( 'load-grunt-tasks' )( grunt );

grunt.initConfig({
    paths: {
        test: {
            files: 'test/**/*.test.js'
        },
    },
    mochaTest: {
        test: {
            options: {
                reporter: 'spec',
                require: 'test/utils/jsdom-config.js'
            },
            src: '<%= paths.test.files %>'
        }
    }
});

grunt.registerTask( 'mocha', 'mochaTest' ); 
```

Enter fullscreen mode Exit fullscreen mode

咕哝用法:

```
$ grunt mocha 
```

Enter fullscreen mode Exit fullscreen mode

NPM 设置:

```
"scripts":  {  "test":  "mocha -R spec -r test/utils/jsdom-config.js test/*.test.js"  } 
```

Enter fullscreen mode Exit fullscreen mode

NPM 用途:

由于`npm run-script`接受`test`作为预定义任务，所以命令更短！

```
$ npm test 
```

Enter fullscreen mode Exit fullscreen mode

### 部署

我的源目录就在我的分发目录旁边。部署的繁重任务需要`grunt-exec`依赖项，它允许运行命令行表达式。在将`public/`目录复制到分发目录之前，部署任务运行所有的林挺、丑化和 sass 编译。为了简洁起见，我不打算再次列出所有的任务，只列出关键的任务。

咕哝设置:

```
require( 'load-grunt-tasks' )( grunt );

grunt.initConfig({
    pkg: grunt.file.readJSON( 'package.json' ),
    paths: {
        host: {
            dir: '../foresthoffman.github.io/<%= pkg.name %>/'
        },
        source: {
            dir: 'public/'
        }
    },
    /* Other task definitions */
    exec: {
        copy: {
            cmd: function () {
                var host_dir_path = grunt.template.process( '<%= paths.host.dir %>' );
                var source_dir_path = grunt.template.process( '<%= paths.source.dir %>' );

                var copy_command = 'cp -r ' + source_dir_path + '  ' + host_dir_path;

                return copy_command;
            }
        }
    },
});

grunt.registerTask( 'build', [
    'jshint', 
    'sass', 
    'cssmin', 
    'mochaTest', 
    'concat', 
    'uglify', 
    'exec:zip'
]);
grunt.registerTask( 'deploy', [ 'build', 'exec:copy' ] ); 
```

Enter fullscreen mode Exit fullscreen mode

咕哝用法:

```
$ grunt deploy 
```

Enter fullscreen mode Exit fullscreen mode

NPM 设置:

我制作了一个简单的 bash 脚本，这样我就可以完全放弃这个任务了。

```
##
# cp_public
##

if [ ! $# == 2 ] || [ ! -d $1 ] || [ ! -d $2 ]; then echo "cp_public /path/to/source /path/to/dist"
    exit fi

# the name property line from the package.json file in the current directory
name_line=$(grep -Ei "^\s*\"name\":\s\".*\",$" package.json | grep -oEi "[^\",]+")

# the package name by itself
name=$(echo $name_line | cut -d " " -f 3)

path_reg="\/"

source_path="$(echo ${1%$path_reg})/"
dist_path="$(echo ${2%$path_reg})/$name"

cp -r $source_path $dist_path 
```

Enter fullscreen mode Exit fullscreen mode

```
"scripts":  {  "deploy":  "./cp_public public/ ../foresthoffman.github.io/"  } 
```

Enter fullscreen mode Exit fullscreen mode

NPM 用法:

```
$ npm run deploy 
```

Enter fullscreen mode Exit fullscreen mode

### 观察者

我选择使用 [`npm-watch`](https://www.npmjs.com/package/npm-watch) 包，而不是使用 Grunt 来运行并发文件观察器。这允许我指示 X 个脚本(来自我的`package.json`文件的脚本属性)和当观察器激活时应该触发脚本的文件。我只需要让观察者处理 js 文件的变化，因为 sass 命令有它自己的`--watch`参数。

那么 watch 语句的配置如下所示:

```
"watch":  {  "js":  "scripts/*.js"  },  "script":  {  "js":  "uglifyjs scripts/*.js -cmo public/scripts/main.min.js",  "watch":  "npm-watch"  } 
```

Enter fullscreen mode Exit fullscreen mode

然后可以通过`npm run watch`运行。

在写这篇文章的时候，我实际上实现了我在这里提到的改变。我已经删除了我的`Gruntfile`，剩下的是我的`package.json`中的配置和我在`cp_public`中的新 bash 脚本。一切都在按计划进行。呜！

`package.json`...

```
"devDependencies":  {  "chai":  "^3.4.1",  "jsdom":  "^7.2.2",  "mocha":  "^2.3.4",  "npm-watch":  "^0.1.7",  "sinon":  "^1.17.2"  }  ...  "watch":  {  "js":  "scripts/*.js"  },  "scripts":  {  "deploy":  "./cp_public public/ ../foresthoffman.github.io/ || true",  "sass":  "sass --scss -t compressed styles/*.scss public/styles/style.min.css",  "sassWatch":  "sass --watch --scss -t compressed styles/style.scss:public/styles/style.min.css",  "js":  "uglifyjs scripts/*.js -cmo public/scripts/main.min.js",  "test":  "mocha -R spec -r test/utils/jsdom-config.js test/*.test.js || true",  "testWatch":  "mocha -w -R spec -r test/utils/jsdom-config.js test/*.test.js || true",  "watch":  "npm-watch"  } 
```

Enter fullscreen mode Exit fullscreen mode

和`cp_public`...

```
#!/bin/bash

##
# cp_public
#
# Copies the source directory to the target directory.
#
# Usage: cp_public /path/to/source /path/to/dist
# 
# The package.json file, from which the package name is collected is relative to where this script
# is executed. It uses the current directory. That is why this script should be placed next to the
# package.json file in the project's hierarchy.
##

if [ ! $# == 2 ] || [ ! -d $1 ] || [ ! -d $2 ]; then echo "cp_public /path/to/source /path/to/dist"
    exit fi

# the name property line from the package.json file in the current directory
name_line=$(grep -Ei "^\s*\"name\":\s\".*\",$" package.json | grep -oEi "[^\",]+")

# the package name by itself
name=$(echo $name_line | cut -d " " -f 3)

path_reg="\/"

source_path="$(echo ${1%$path_reg})/"
dist_path="$(echo ${2%$path_reg})/$name"

cp -r $source_path $dist_path 
```

Enter fullscreen mode Exit fullscreen mode

### 那都是乡亲们！

我承认，如果没有更多的依赖性，没有多少好的并发包。这样，使用 Grunt(或其他跑步者)是有益的。就我个人而言，我不能再为看起来相对微小的好处而增加复杂性。

我喜欢工具和自动化。我认为编写单元测试是一件非常奇妙并且非常值得的事情。然而，我觉得我们用来自动化基本任务的工具增加了太多的复杂性。由于 web 开发生态系统的发展速度，我每天都看到越来越多的这种情况。

还有人有同样的感觉吗？有没有人陷入了无法证明移除这些依赖项是正确的境地？有人仍然是跑步者的超级粉丝，并且会在未来的项目中使用他们吗？

必备 xkcd 漫画插件:

[![](img/56a51e7f355ebe62f917e35f417ab731.png)T2】](http://xkcd.com/927/)