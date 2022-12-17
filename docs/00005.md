# 在树莓派上使用榆树

> 原文：<https://dev.to/danielkun/using-elm-on-a-raspberry-pi-42pi>

在你的树莓派上运行榆树

Elm 是一种令人敬畏的编程语言，它让你感到舒适、强大和安全。由于我在几个 Raspberry pi 上安装了一个 Kubernetes 集群，并为几个实验构建了一个前端(参见 [kube-alive](https://github.com/daniel-kun/kube-alive) ),所以我需要在 Raspberry pi 上编译 Elm 代码。不幸的是，没有通过 npm 或任何其他方式发布的 arm CPU 架构的官方 Elm 二进制文件。

```
 daniel@kubepi-red:~ $ npm install elm
    > elm@0.18.0 install /home/daniel/node_modules/elm
    > node install.js

    Unfortunately, there are currently no Elm Platform binaries available for your operating system and architecture.

    If you would like to build Elm from source, there are instructions at https://github.com/elm-lang/elm-platform#build-from-source

    npm WARN This failure might be due to the use of legacy binary "node"
    npm WARN For further explanations, please read
    /usr/share/doc/nodejs/README.Debian 
```

Enter fullscreen mode Exit fullscreen mode

所以我开始自己用 raspbian (stretch)上的树莓 Pi 来编译 Elm for arm。这被证明是相当乏味的，因为两个因素是一个糟糕的组合:不是所有版本的 Elm 依赖项(尤其是 Cabal)都像预期的那样工作并且兼容。编译一个版本的 Cabal 需要大约*天*。(嗯，至少比 24 小时多一点。)

好消息是，它非常简单，如果你按照本教程中的步骤，并使用与我完全相同的版本，应该可以毫无困难地工作。仍然需要大约两天的时间来安装和编译您需要的所有东西。更好的消息是，我已经为您做了这件事，并且可以通过 docker 提供一种更快的方式在您的 Raspberry Pi 上运行 Elm。

# 捷径:使用预建的 docker 图像

如果您不想等那么长时间来构建 Elm，您可以使用我预先构建的 docker 映像，您可以在您的 raspi 上运行它。在 Docker Hub 上找到的是[Daniel Kun/elm-rasp bian-arm 32v 7](https://hub.docker.com/r/danielkun/elm-raspbian-arm32v7/)。

只需运行

```
docker run -it danielkun/elm-raspbian-arm32v7 bash 
```

Enter fullscreen mode Exit fullscreen mode

在这个容器中，您可以像往常一样使用`elm`命令。注意，一旦关闭 shell，在这个`docker run`会话中发生的所有事情都将被丢弃。这就是为什么您应该将一个目录作为一个卷挂载到容器中——这些更改是直接对您的文件系统进行的，并且是永久的。

要运行 elm docker 容器，将当前目录挂载到/code 并正确设置所有内容，请执行

```
docker run -it --rm -v "$(pwd):/code" -w "/code" -e "HOME=/tmp" -u $UID:$GID -p 8000:8000 danielkun/elm-raspbian-arm32v7 
```

Enter fullscreen mode Exit fullscreen mode

如果您想更长时间地坚持这一点，您可以将您的本地“elm”命令作为它的别名，所有参数都将被追加，这样您就可以运行“elm make”、“elm reactor”等。就像它安装在您的本地计算机上一样。像这样设置别名:

```
alias elm='docker run -it --rm -v "$(pwd):/code" -w "/code" -e "HOME=/tmp" -u $UID:$GID -p 8000:8000 danielkun/elm-raspbian-arm32v7 elm' 
```

Enter fullscreen mode Exit fullscreen mode

(这个戏法归功于来自 https://github.com/maport/docker-elm[的 *maport*](https://github.com/maport/docker-elm)

# 自己造榆树

如果您对使用 docker 映像不满意，您还可以自己构建 Elm，前提是您有足够的时间。

Elm 只依赖少数几个平台，但是这些平台非常庞大，并且不是所有的平台都可以作为二进制文件获得:

*   llvm-3.5(对于 ghc，可以二进制形式获得)
*   ghc 7.10.3(可以二进制形式获得)
*   cabal-install 1.22.6(必须编译，因此安装速度很慢)

除了这些较大的依赖项之外，还有一些可以通过 apt 安装的包，其中许多将在通过 cabal 构建 Elm 时下载和编译。

所以让我们从安装可以从 apt 获得的包开始，因为这是最容易的部分:

```
sudo apt-get update && sudo apt-get install -y wget curl make libgmp-dev libnss3 apt-utils libnss-lwres libnss-mdns netbase ca-certificates cmake g++ libtinfo-dev git zlib1g-dev 
```

Enter fullscreen mode Exit fullscreen mode

作为构建过程的一部分，为了以后使用 elm 命令，我们需要 llvm、cabal 和 elm 的几个目录在我们的路径中:

```
echo 'export PATH="$PATH:$HOME/elm/llvm-3.5/bin:$HOME/.cabal/bin:$HOME/elm/Elm-Platform/0.18/.cabal-sandbox/bin/"' >> ~/.bashrc && source ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

现在，下载我们以后需要的一切:

```
mkdir ~/elm-downloads && cd ~/elm-downloadswget http://releases.llvm.org/3.5.2/clang+llvm-3.5.2-armv7a-linux-gnueabihf.tar.xz
wget https://downloads.haskell.org/~ghc/7.10.3/ghc-7.10.3-armv7-deb8-linux.tar.bz2
wget http://www.haskell.org/cabal/release/cabal-install-1.22.6.0/cabal-install-1.22.6.0.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

然后将包解压到构建 Elm 的专用目录:

```
mkdir ~/elm && cd ~/elm
tar xf ~/elm-downloads/clang+llvm-3.5.2-armv7a-linux-gnueabihf.tar.xz && mv clang+llvm-3.5.2-armv7a-linux-gnueabihf llvm-3.5
tar xf ~/elm-downloads/ghc-7.10.3-armv7-deb8-linux.tar.bz2
tar xf ~/elm-downloads/cabal-install-1.22.6.0.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

LLVM 现在可以从它被提取的地方使用了。但是，GHC 需要安装:

```
cd ~/elm/ghc-7.10.3/
./configure
make install 
```

Enter fullscreen mode Exit fullscreen mode

这应该相当快，因为它不构建任何东西，只是复制文件。接下来，去完成困难的(也是耗时的)任务:建造阴谋集团——安装

```
cd ~/elm/cabal-install-1.22.6.0
./bootstrap.sh 
```

Enter fullscreen mode Exit fullscreen mode

现在去喝杯咖啡。和你的妻子和孩子出去几个小时。然后睡觉，第二天早上醒来，去工作，然后，下班后，它可能已经完成了。提醒你一下，我用的是最新的树莓派 3，它仍然用了大约 10 个小时左右。例如，如果你有一个树莓 Pi 2，它可能需要更长的时间。

但终究会结束的。当它运行时，您现在可以下载并编译 Elm。(记住:你的路径已经设置正确。)注意，我必须修复安装脚本的一些小问题，因为它设置了“split-Objs: True”，这似乎与 GHC 或 cabal 版本不兼容，所以我将其更改为“split-Objs: False”。

```
curl -sSL https://raw.githubusercontent.com/elm-lang/elm-platform/master/installers/BuildFromSource.hs \
 | sed "s/split-objs: True/split-objs: False/" > BuildFromSource.hs && runhaskell BuildFromSource.hs 0.18 
```

Enter fullscreen mode Exit fullscreen mode

这同样需要一天左右的时间。我没有精确测量过。之所以需要这么长时间，是因为在 Elm 建立之前，有相当多的 cabal 依赖项将被下载和建立。

万岁！您现在应该可以使用“elm”命令了！祝您在 raspi 上玩得开心，并做一些很酷的物联网项目。

最后，您可以通过删除不再需要的下载和临时文件来进行大量清理:

```
rm -rf ~/elm-downloads ~/elm/ghc-7.10.3 ~/elm/cabal-install-1.22.6.0 
```

Enter fullscreen mode Exit fullscreen mode

榆树赢了！