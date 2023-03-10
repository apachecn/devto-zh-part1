# 在 Docker 中运行 Python 的最快方法

> 原文：<https://dev.to/grahamlyons/the-quickest-way-to-run-python-in-docker-165>

我喜欢 Python。我认为这是一种设计精美的语言，其哲学让我作为一名努力完成工作的开发人员非常欣赏。只需在命令提示符下运行此命令:`python -m this`

```
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those! 
```

Enter fullscreen mode Exit fullscreen mode

不那么好看的是 Python 包装。这很尴尬，也很令人困惑——它正在变得越来越好，但仍然没有其他语言(Ruby、Java、Javascript)好。

Docker 是各种 Linux 特性的集合——名称空间、cgroups、union 文件系统——以这样的方式组合在一起，使得您可以在一个与语言无关的容器中打包和分发软件。Docker 是规避 Python 打包痛苦的好方法。

要安装它，去[https://www.docker.com/](https://www.docker.com/)并在“获取 Docker”链接下选择你的操作系统版本。

## 刚好够 Docker

所以。我们至少能逃脱什么惩罚？或者，我至少能写些什么来说明这一点呢？好吧，如果我们使用`onbuild` Python Docker 图像，那么就不多了。

想象一下，我们有一个超级简单的 Flask 应用程序，它只有一个路由，返回一个固定的字符串(Hello，World？).我们需要很少，但我们确实依赖烧瓶。即使在我的 Mac 上，在最新的操作系统下(好吧，还不是很高)，默认的 Python 安装也不包括`pip`包管理器。搞什么鬼？它确实包括`easy_install`，所以我可以`easy_install pip`，或者更确切地说是`sudo easy_install pip`，因为它将在全球范围内发布。然后我也可以全球安装`flask`软件包。呜呜呜。我的系统现在全局安装了哪个版本？谁知道呢！(?)

我们不要那样做。让我们创建我们的`requirements.txt`文件:

```
echo Flask > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

还有我们的烧瓶 app:

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0') 
```

Enter fullscreen mode Exit fullscreen mode

然后*然后*让我们也来个`Dockerfile`:

```
echo FROM python:onbuild >> Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

## 建造它

好了，那么我们来建造它:

```
$ docker build -t myapp.local .
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM python:onbuild
# Executing 3 build triggers...
Step 1/1 : COPY requirements.txt /usr/src/app/
 ---> Using cache
Step 1/1 : RUN pip install --no-cache-dir -r requirements.txt
 ---> Using cache
Step 1/1 : COPY . /usr/src/app
 ---> Using cache
 ---> 79fdf87107de
Successfully built 79fdf87107de
Successfully tagged myapp.local:latest 
```

Enter fullscreen mode Exit fullscreen mode

是这样吗？建成了吗？是啊:

```
$ docker image ls myapp.local
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
myapp.local         latest              c58ad169cb28        4 seconds ago       700MB 
```

Enter fullscreen mode Exit fullscreen mode

## 运行它

太好了，我们的应用程序在一个容器里！接下来呢？像这样运行容器:

```
$ docker run --rm myapp.local python server.py
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit) 
```

Enter fullscreen mode Exit fullscreen mode

这告诉`docker`命令`run`到`myapp.local`容器(我们构建的)，当它停止(`--rm`)时移除它，并在其中运行命令`python server.py`。太神奇了！我们现在可以看我们的应用了吗？

## 最后一块

我们目前看不到我们的应用程序，因为尽管它在容器中运行得很好，但在其他任何地方都无法访问。当我们运行它时，我们得到的消息说它正在侦听端口`5000`，但是如果我们试图在同一个主机上访问它，我们会得到一个错误:

```
$ curl localhost:5000
curl: (7) Failed connect to localhost:5000; Connection refused 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在运行它的容器之外公开端口:

```
docker run --rm -p 5001:5000 myapp.local python server.py 
```

Enter fullscreen mode Exit fullscreen mode

`-p`参数将您的本地`5001`端口映射到容器内的`5000`(它们可以是相同的，但我将它们区分开来只是为了说明主机端口和容器端口在哪里)。

## 还好，还没完全结束...

所以当我写这个的时候，我发现:

> ONBUILD 映像变体已被否决，并且不鼓励使用它们。

没关系——你不会真的在任何严肃的事情上使用`ONBUILD`图像，定义它的`Dockerfile`非常容易理解。自己去看看吧，看看效果如何:[https://github . com/docker-library/python/blob/f12c 2d/3.6/Jessie/onbuild/docker file](https://github.com/docker-library/python/blob/f12c2d/3.6/jessie/onbuild/Dockerfile)

我们可以用下面的内容替换我们的`Dockerfile`的内容，得到同样的结果:

```
FROM python

RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r requirements.txt

COPY . /usr/src/app 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚删除了`ONBUILD`指令(加上`3.6-jessie` Python 版本——我们可以用最新的)。

## 真的结束了

这就是我们如何在 Docker 容器中运行 Python 应用程序及其依赖项的最小示例。Docker 是一项令人惊叹的技术，难怪[公司](https://www.bloomberg.com/news/articles/2017-08-09/docker-is-said-to-be-raising-funding-at-1-3-billion-valuation)被[看重](https://www.sdxcentral.com/articles/news/sources-microsoft-tried-to-buy-docker-for-4b/2016/06/)所以[高度评价](https://www.forbes.com/sites/mikekavis/2015/07/16/5-reasons-why-docker-is-a-billion-dollar-company/#47e077c1f04f)。在这种情况下，它为我们提供了一种干净的方式来避免 Python 打包的痛苦，但我们现在也有了一个容器映像，其中包含我们的应用程序，可以在任何其他可以运行 Docker 的系统上分发和运行。