# Heroku 中的自定义构建包

> 原文：<https://dev.to/farisj/custom-buildpacks-in-heroku-5176>

想象一下这个——你正在构建一个网络应用程序，并把它托管在 Heroku 上。然而，你正在使用的框架和技术栈有一些配置问题，并没有以你希望的方式推向 Heroku 也许你的回购并不完全具有你现场需要的结构，一些被某个东西在本地抽象掉的东西。你该怎么办？

### 进入:自定义 Heroku Buildpacks！

Heroku 允许应用程序获取管理员指定的任何构建包，并在部署之前运行该构建包。要给你的应用添加新的构建包，只需运行

```
heroku buildpacks:set https://github.com/some/buildpack.git -a myapp 
```

在候机厅，你应该准备好了。

##### 那么这些构建包到底是什么样子的呢？

根据 [Heroku 文档](https://devcenter.heroku.com/articles/buildpack-api)，构建包包含三个在运行时执行的主要文件:

*   `bin/detect`:决定是否将这个 buildpack 应用到一个 app。
*   `bin/compile`:用于执行 app 上的转换步骤。
*   `bin/release`:向运行时返回元数据。

#### `bin/detect`

`detect`文件将决定安装这个构建包的应用程序是否适合构建包本身。从本质上来说，这是一种保护措施，确保您在正确的框架内工作，不管您的构建包将要做什么。

它接收一个参数`BUILD_DIR`，这是应用程序本身的根。从那里`detect`应该分析文件结构，看看它是否兼容(例如，如果你需要它是一个 Rails 应用程序，确保有一个 Gemfile 和 config.ru)。

#### `bin/compile`

这个文件实际上执行了您在这个构建包中设定的任务。这可以是你想要的任何东西！

除了`BUILD_DIR`之外，它还接收一个`CACHE_DIR`参数，该参数表示 buildpack 可以用来在两次构建之间缓存文件的目录。

此外，它还接收第三个参数`ENV_DIR`，这是一个包含 Heroku 设置提供的所有环境变量的文件目录。在这个目录中，文件名是变量名，文件内容是变量值。

#### `bin/release`

这个程序将运行一个可选文件，以 YAML 格式返回一个应用程序使用的`addons`和`default_process_types`列表。`addons`仅在第一次部署时执行。如果你试图在基于 buildpack 的应用中添加额外的技术，这是一个很好的地方。

* * *

有了这些信息，在部署到 Heroku 之前很容易做很多事情。Heroku 的网站[这里](https://elements.heroku.com/buildpacks)列出了各种定制的构建包，但是如果没有适合你的用例，就自己动手制作吧！