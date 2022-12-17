# Sparrowdo 作为吞咽和朋友的选择。

> 原文：<https://dev.to/melezhik/sparrowdo-as-gulp-and-friends-alternative-2nk>

如今，开发人员使用广泛流行的工具，如 [Gulp](https://gulpjs.com/) 或 [Gunt](https://gruntjs.com/) 进行日常任务自动化。然而，这些工具都很棒:

*   您应该使用 Java 脚本来编写构建场景和扩展。
*   扩展(插件)作为 npm 软件包安装。

嗯，[Sparrow do](https://github.com/melezhik/sparrowdo/)/[Sparrow](https://github.com/melezhik/sparrow)为那些想坚持其他语言的人提供了合理的选择:

*   构建场景是用漂亮的高级语言 Rakudo [Perl6](https://perl6.org/) 编写的。
*   扩展是在其中一种语言上创建的——Bash/perl 5/Ruby/Python
*   插件以 Sparrow 插件的形式安装(不需要显式安装，详见)。

下面是任务自动化的一个简单例子或 Sparrowdo 用法。

# 安装刀具链

你只需要两部分:

Sparrowdo 是 Sparrow 插件系统的轻量级 Perl6 API。和麻雀本身，因为它充当任务亚军。安装过程非常简单:

```
$ sudo cpanm Sparrow --notest -q
$ zef install Sparrowdo 
```

Enter fullscreen mode Exit fullscreen mode

# 创建场景

应该是名为`sparrowfile`的文件，放在当前工作目录:

```
#!perl6
use IO::Glob;

directory "tmp/";

for glob("js/*.js") -> $file { 

  task-run "minify $file" , 'js-minify', %(
      in  => $file.absolute,      
      out => "tmp/" ~ ( $file.basename ),      
  );

}

for glob("images/*.png") -> $file { 

  task-run "compress $file", "png-compress", %(
      in  => $file.absolute,      
      out => "tmp/" ~ ( $file.basename ),
      compression => 6       
  );

} 
```

Enter fullscreen mode Exit fullscreen mode

让我解释一下这里发生了什么。

我们运行了两个名为`"minify $file"`和`"compress $file"`的任务，它们执行以下操作:

*   缩小输入的 JS 文件，并将更新后的版本存储在`tmp/`目录下
*   压缩 PNG 文件，并将更新后的版本存储在同一个`tmp/`目录下

所有的任务都在循环中运行，每个文件都位于相应的目录:
`js/`和`images/`。

`Task_run`函数获得 3 个参数:

*   `task_description` -人类可读的任务描述。
*   `plugin_name` -底层 sparrow 插件的名称。
*   `plugin_parameters` -这是一个可选参数，用来设置插件所消耗的参数，它们以 Perl6 哈希结构的形式传递。

麻雀插件是工作马，在这里做所有的工作。正如我已经说过的，它们可以用 4 种语言中的一种编写——perl 5、Bash、Ruby 或 Python。

我在这里使用了几个插件:

*   使用 GD 库压缩 png 图像的插件。
*   js-minify -使用 JavaScript::Minifier CPAN 模块来缩小 JavaScript 文件的插件。

使用 Sparrow 插件很酷的一点是，我们不需要事先安装就可以使用它们。这是自动提供的，*在运行时*。此外，许多插件，因为它们是在 Perl5 或 Ruby 或 Python 上编写的，可以有自己的依赖项(CPAN/RubyGems/Pip 模块),所有这样的依赖项都由 Sparrow 为您解决(再次在运行时)并以安全的方式安装在本地，不会污染您的系统路径，也不需要运行`sudo`。

# 让我们试一试

现在我们都准备好了。轮到 Sparrowdo 来执行我们的场景:

```
$ sparrowdo  --local_mode --no_sudo --sparrow_root=$PWD/.sparrowdo-cache --cwd=$PWD  --format=production 
  --format=production
running sparrow tasks on 127.0.0.1 ... 
target OS is - ubuntu
push [task] create directory tmp/ OK
push [task] minify js/in2.js [plg] js-minify OK
push [task] minify js/in1.js [plg] js-minify OK
push [task] minify js/in3.js [plg] js-minify OK
push [task] compress images/image1.png [plg] png-compress OK
push [task] compress images/image3.png [plg] png-compress OK
push [task] compress images/image2.png [plg] png-compress OK
SPL file /home/melezhik/projects/sparrowdo-test/task-runner/.sparrowdo-cache/sparrow.list is empty
get index updates from SparrowHub ... OK
set up task box file - /home/melezhik/.sparrowdo//home/melezhik/projects/sparrowdo-test/task-runner/.sparrowdo-cache/task-box.json - OK
installing public@directory version 0.001005 ...
Download https://sparrowhub.org/plugins/directory-v0.001005.tar.gz --- 200
installing public@js-minify version 0.000002 ...
Download https://sparrowhub.org/plugins/js-minify-v0.000002.tar.gz --- 200
Installing modules using /home/melezhik/projects/sparrowdo-test/task-runner/.sparrowdo-cache/plugins/public/js-minify/cpanfile
Successfully installed JavaScript-Minifier-1.14
1 distribution installed
Complete! Modules were installed into /home/melezhik/projects/sparrowdo-test/task-runner/.sparrowdo-cache/plugins/public/js-minify/local
installing public@png-compress version 0.000001 ...
Download https://sparrowhub.org/plugins/png-compress-v0.000001.tar.gz --- 200
Installing modules using /home/melezhik/projects/sparrowdo-test/task-runner/.sparrowdo-cache/plugins/public/png-compress/cpanfile
Successfully installed ExtUtils-PkgConfig-1.16
Successfully installed GD-2.67
2 distributions installed
Complete! Modules were installed into /home/melezhik/projects/sparrowdo-test/task-runner/.sparrowdo-cache/plugins/public/png-compress/local
unknown project taskbox at /home/melezhik/perl5/perlbrew/perls/perl-5.23.6/lib/site_perl/5.23.6/Sparrow/Commands/Project.pm line 94.
running task box from /home/melezhik/projects/sparrowdo-test/task-runner/.sparrowdo-cache/sparrow-cache/task-box.json ... 
2017-11-30 12:08:23 : [task] create directory tmp/ [path] modules/create/
2017-11-30 12:08:23 : [task] minify js/in2.js [plg] js-minify [path] /
2017-11-30 12:08:23 : [task] minify js/in1.js [plg] js-minify [path] /
2017-11-30 12:08:23 : [task] minify js/in3.js [plg] js-minify [path] /
2017-11-30 12:08:24 : [task] compress images/image1.png [plg] png-compress [path] /
2017-11-30 12:08:24 : [task] compress images/image3.png [plg] png-compress [path] /
2017-11-30 12:08:24 : [task] compress images/image2.png [plg] png-compress [path] / 
```

Enter fullscreen mode Exit fullscreen mode

# 结束

当然 Sparrowdo/Sparrow 面临着 Grunt 或 Gunt 这样的主要工具的激烈竞争。尽管我在这里看到了一块绿色的领域，特别是对于那些喜欢用 Java 脚本之外的语言来创建自动化场景的人。

PS 示例项目的源代码可以在这里找到-[https://github . com/mele zhik/sparrow do-test/tree/master/task-runner](https://github.com/melezhik/sparrowdo-test/tree/master/task-runner)