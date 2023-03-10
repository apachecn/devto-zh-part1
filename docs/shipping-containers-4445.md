# 海运集装箱

> 原文：<https://dev.to/s1hofmann/shipping-containers-4445>

# 集装箱，到处都是集装箱！

现代软件开发对容器非常着迷。

它们无疑改变了我们发布软件的方式，作为一名最新的开发人员，不可避免地会听到、读到或使用容器技术。

当我第一次摆弄容器时，我脑海中立即出现了一个想法:

容器将是在我的服务器上使用的完美选择！

在自己的隔离环境中运行我的服务器上的每个组件，包括配置和数据，将保持实际服务器的整洁及其组件的可移植性。构建您配置的 Docker 映像一次，它就可以在任何服务器上运行(运行 Docker)。

在我的兄弟和我的一个朋友说服我完全需要运行我自己的 XMPP 服务器之后，我决定这将是放在容器中的最完美的东西。

包含我的 XMPP 服务器教会了我很多关于 Docker 的实际使用，所以如果你感兴趣，请继续阅读！

## 首发出场

遵循 [KISS 原则](https://en.wikipedia.org/wiki/KISS_principle)，我决定从一个 [Alpine Linux](https://alpinelinux.org/about/) 基地开始我的容器**。** 

```
FROM alpine:latest 
```

Enter fullscreen mode Exit fullscreen mode

使用 Alpine Linux 作为基础映像将允许我用很少的开销来构建我的映像。另一方面，我不得不自己安装大多数依赖项。

来自的**语句指定了新图像的*基础图像*，所以每个 Dockerfile 文件都必须以它开始。**

## 收集依赖关系

通过挖掘我选择的 XMPP 服务器的文档([韵律](https://prosody.im/))，我得到了一个要安装的包的列表。

```
RUN apk --no-cache add --update bash \
ca-certificates \
lua-expat \
lua-filesystem \
lua-sec \
lua-socket \
mercurial \
python \
prosody 
```

Enter fullscreen mode Exit fullscreen mode

运行这个命令会在我们的 Docker 映像中创建一个新的*层*，之后的每个命令也是如此。Docker 容器是分层组装的，就像千层面一样。

每一层都与下面的层有所不同，所以我们的新层只包含通过包管理安装的新文件。当构建 Docker 映像时，我们基本上只是堆叠只读层，Docker 稍后会在上面添加一个可写层来存储运行时数据。

## 添加外部文件

韵律需要一个配置文件，因为我不想每次启动容器时都进行配置，所以我会在构建时提供一个静态文件。Docker 提供了两种方式将外部文件添加到图像中:[添加](https://docs.docker.com/engine/reference/builder/#add)和[复制](https://docs.docker.com/engine/reference/builder/#copy)。

现在**添加**和**复制**基本上做完全相同的事情:将文件和/或目录从给定的源复制到容器内的目的地。

与**复制**，**添加**相比，提供了一些额外的功能(只是一个非常简短的总结来突出区别):

1.  $source 是本地归档文件:归档文件将被解压缩，其内容将被复制。
2.  $source 是一个 URL:文件将被下载和复制。

**复制**语法如下:

```
COPY /path/to/config/on/host /path/to/config/in/container 
```

Enter fullscreen mode Exit fullscreen mode

## 建筑形象

在设置了实际的服务器配置之后，在韵律的情况下，它由一个文件组成，是时候[构建一个图像](https://docs.docker.com/engine/reference/commandline/image_build/)了。

```
docker image build -t prosody_xmpp . 
```

Enter fullscreen mode Exit fullscreen mode

这将基于当前目录中的 Docker 文件构建一个名为*韵律 _xmpp* 的 Docker 映像。

正在运行

```
docker image list 
```

Enter fullscreen mode Exit fullscreen mode

将打印可用图像的列表。

## 运行图像

新容器通过[运行](https://docs.docker.com/engine/reference/run/)它们来启动:

```
docker run $image 
```

Enter fullscreen mode Exit fullscreen mode

图像由名称或生成的图像 id 指定，因此不需要提供完整的 id。Docker 将运行由 id 的前几个给定文字唯一标识的图像。

# 挂载外部文件

为了启用 TLS，韵律需要证书。

尽管这是一个简单的解决方案，但我认为将任何私有证书数据添加到 Docker 映像中是不好的做法。相反，像私钥这样的敏感数据可以在运行时通过主机系统的[绑定挂载](https://docs.docker.com/engine/admin/volumes/bind-mounts/#use-a-read-only-bind-mount)文件传递给容器。

```
docker run --mount source=/source/on/host,target=/destination/in/container,readonly $image 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，文件和文件夹以 RW 模式装载，添加 readonly 标志将使装载只读。

## 持久存储

如前所述，Docker 在映像上添加了一个额外的可写层来保存运行时数据。

当一个容器被停止时，这个 RW 层被丢弃，因此任何运行时数据都会丢失。在我的 XMPP 服务器的情况下，这意味着任何数据，如聊天记录，收到的文件等。一旦容器停止，就会丢失。

通过 [volumes](https://docs.docker.com/engine/admin/volumes/volumes/) ，Docker 提供了一种在容器的多次运行中持久保存数据的方法。

[创建新卷](https://docs.docker.com/engine/admin/volumes/volumes/#create-and-manage-volumes)很简单:

```
docker volume create $volume_name 
```

Enter fullscreen mode Exit fullscreen mode

与图像类似，通过执行以下命令列出卷:

```
docker volume ls 
```

Enter fullscreen mode Exit fullscreen mode

卷是在运行容器时指定的[，类似于绑定挂载。](https://docs.docker.com/engine/admin/volumes/volumes/#start-a-container-with-a-volume) 

```
docker run --mount source=$volume_name,target=/location/in/container $image 
```

Enter fullscreen mode Exit fullscreen mode

## 启动时运行服务器

Dockerfiles 中使用了 [CMD](https://docs.docker.com/engine/reference/builder/#cmd) 命令来提供启动时运行的默认可执行文件。Docker 只允许一条 CMD 语句，在多条语句的情况下，只执行最后一条。

使用 CMD 启动 XMPP 服务器的一种方法是

```
CMD prosodyctl start 
```

Enter fullscreen mode Exit fullscreen mode

虽然使用 **CMD** 启动服务器完全没问题，但是还有一种运行程序的替代方式。到目前为止，XMPP 映像的配置方式是，容器将作为可执行文件运行，所以通过运行容器，我们的实际意图是启动一个程序，而这个程序恰好在容器中运行。

Dockers 将容器作为可执行文件运行的方式是[入口点](https://docs.docker.com/engine/reference/builder/#entrypoint)。

## 环境变量

我的 XMPP 映像的第一次迭代不支持定制和持久存储。因此，每次我启动服务器时，我都必须连接到容器，并在运行的容器中通过命令行进行初始配置。

有了使用卷的持久存储，在启动时定制服务器的一种方法是提供一个脚本作为入口点。

我不想向所有人开放我的服务器，所以 entrypoint 脚本的目的是有选择地注册一个新用户，然后通过 config 禁用注册。

注册需要三样东西:

1.  用户名
2.  领域
3.  密码

Docker 允许在启动时将这些值作为环境变量传递给容器。然后，启动脚本读取这些值，如果存在，则注册一个新用户。

```
docker run -e VAR1=value1 -e VAR2=value2 -e BOOLEAN $image 
```

Enter fullscreen mode Exit fullscreen mode

## 整理完毕

XMPP 容器运行的最后一个要求是端口。

Docker 容器提供了一个隔离的网络环境，因此在默认情况下，运行在容器内部的服务器从容器外部是无法到达的，反之亦然。

为了使服务器可用，需要[暴露端口](https://docs.docker.com/engine/reference/commandline/run/#publish-or-expose-port--p-expose)。

```
docker run -p 5222:5222 $image 
```

Enter fullscreen mode Exit fullscreen mode

这将把容器的端口 5222 绑定到主机上的端口 5222。指定韵律使用的所有端口就完成了容器服务器的设置。

## 结论

在服务器上使用 Docker 对我来说很有用。我学到了很多东西，并且喜欢构建一个工作设置的过程，所以我希望能够将我所有的运行服务放到容器中。

我希望你喜欢我的小故事，并为自己考虑容器。

再见

西蒙

附:你现在可以通过联系我；)