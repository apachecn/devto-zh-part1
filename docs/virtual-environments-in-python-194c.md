# Python 中的虚拟环境

> 原文：<https://dev.to/rishibaldawa/virtual-environments-in-python-194c>

我一直在努力理解如何打包 python 脚本和库。我使用的一些工具严重依赖于该语言的旧版本。有些甚至依赖于特定版本的旧库。每天在 Java 商店工作，这并不奇怪。真正让我措手不及的是，当有不同的库可以使用时，如何让所有的库都工作起来。例如，一些库需要 [Python 2.7](https://www.python.org/download/releases/2.7/) ，而我的另一个项目需要 [Python 3.4 或更高版本](https://www.python.org/download/releases/3.4.0/)。看来解决的办法是用 [VirtualEnv](https://virtualenv.pypa.io/en/latest/#) 。

VirtualEnv 允许你隔离不同的包&甚至是 python 版本。您需要手动激活/停用一个 virtualenv，但是一旦您进入其中一个，每个 virtualenv 都可以在 site-packages 目录中拥有自己的 Python 副本和依赖项。这允许您在同一个开发盒/笔记本电脑中创建多个环境。通常，您为每个工作区/项目创建一个虚拟 env，但是您也可以为同一个项目创建多个 env，以查看您的库在不同情况下是如何工作的。这是一笔很好的交易。

## 安装

要进行所有设置，请遵循[安装指南](https://virtualenv.pypa.io/en/latest/installation/)

```
sudo pip install virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

要创建一个新的 virtualenv，你只需进入你的项目并激活

```
cd my_project_root_folder
virtualenv -p /usr/bin/python2.7 my_project
# now you activate
source my_project/bin/activate
# now do your dirty work like...
pip install -r requirements.txt
# when yo done, deactivate
deactivate 
```

Enter fullscreen mode Exit fullscreen mode

## 更多资源

一些真正帮助我理解 python 中打包是如何工作的链接

*   [应用依赖关系](https://www.fullstackpython.com/application-dependencies.html)-python 依赖关系概述
*   Python projectland 中的 Alice 带你从一个简单的脚本到一个完整的应用程序/库，在一个非常简化的层次上了解打包是如何工作的
*   [setup.py for Humans](https://github.com/kennethreitz/setup.py) -示例项目解释 setup.py 中的不同值&moar 相关资源
*   setup.py 与 requirements . txt——区分 setup.py & requirements.txt 并解释为什么两者都是必需的。
*   [希区柯克的虚拟人指南](http://docs.python-guide.org/en/latest/dev/virtualenvs/)

我在路上找到的各种链接

*   [一个模板做好 README.md](https://gist.github.com/PurpleBooth/109311bb0361f32d87a2)

## 注

您必须始终手动激活/停用您的虚拟设备。如果你不注意的话，这使你容易犯人为错误。也有工具可以自动完成这一步，但是我发现它们不太好用(到目前为止…)

到目前为止，我遇到的另一个问题是无法将依赖项隔离到它们自己的外壳中。类似于[加·加](https://github.com/shevek/jarjar)的东西。我不认为这是一件坏事，但这仍然是我需要解决的一个小麻烦(通过创建一个项目/微服务器)。