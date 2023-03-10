# 《企业代理背后》

> 原文：<https://dev.to/shriharshmishra/behind-the-corporate-proxy-2jd8>

多年来，我一直在努力通过公司代理，即访问互联网被阻止，因为代理的详细信息没有正确设置。通常，大多数软件文档不会方便地提供这些细节，人们可以搜索文档。为了赢得这场战争，开发人员不得不进行这些无用的战斗。

[![Corporate Proxy!](img/6030d818a5b735ab0ff3d10529810f86.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J7EusXey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23gzqqcm64vigsopypfx.jpg)

我希望我能做连姆·尼森做的事！但我已经赢得了一些战斗，并为一篇短文积累了一些弹药，这篇短文可能会对许多处于类似情况的人有用。在这里，我列出了在以下场景中设置代理详细信息的一些方法:

### 奔跑在代理人身后的流浪者

#### 在您的客户操作系统中设置代理

这取决于客户操作系统。我使用 Ubuntu，所以我发现用代理细节更新`/etc/environment`文件是一个简单的选择。

```
 > cat /etc/environment 
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
    HTTP_PROXY=http://proxy_host:port
    HTTPS_PROXY=http://proxy_host:port
    FTP_PROXY=
    NO_PROXY=localhost,127.0.0.1
    http_proxy=http://proxy_host:port
    https_proxy=http://proxy_host:port
    ftp_proxy=
    no_proxy=localhost,127.0.0.1 
```

#### Use vagrant-proxyconf plugin

有了这个插件，你可以将代理服务器的详细信息直接设置到流浪者文件中。要安装插件，请在您的 shell 上运行以下命令:

```
> vagrant plugin install vagrant-proxyconf 
```

如果你不能下载公司代理背后的插件(废话！)，然后你可以从 [Ruby Gems](https://rubygems.org/gems/vagrant-proxyconf/versions/1.5.2) 站点下载 gem 文件，使用下面的命令安装插件。

```
> vagrant plugin install /path/to/my-plugin.gem 
```

并将以下内容添加到流浪文件中:

```
Vagrant.configure("2") do |config|
  if Vagrant.has_plugin?("vagrant-proxyconf")
    config.proxy.http     = "http://<your-proxy-host>:<port>/"
    config.proxy.https    = "http://<your-proxy-host>:<port>/"
    config.proxy.no_proxy = "localhost,127.0.0.1"
  end
  # ... other stuff
end 
```

现在你应该可以从你的流浪客人那里访问互联网了。 <sup id="fnref1">[1](#fn1)</sup>

* * *

### 代理后面运行 Docker

当您在代理后面的 docker 上工作时，确保代理设置被传递并在不同阶段可用变得很重要——例如，映像创建、容器执行和运行服务。这两种情况都有不同的命令选项。

#### 构建图像时设置代理

您可能希望在构建映像时下载一些软件，以便每个容器都预装了这些软件。虽然您可以将代理细节硬编码为 docker 文件中的环境变量，但不推荐这样做。它妨碍了图像的可移植性，并且不必要地使图像创建的容器可以访问互联网。更好的选择是在使用如下所示的`build-arg`选项构建映像时传递代理信息:

```
> http_proxy=<yourproxyserverhost>:<port>
> https_proxy=<yourproxyserverhost>:<port>
> docker build --build-arg http_proxy=$http_proxy --build-arg https_proxy=$https_proxy -t <your-image> . 
```

`build-arg`选项允许您使用`ARG`指令向 docker 文件中定义的环境变量传递一个值。然而，传递代理细节要容易一些。对于代理详细信息，Dockerfile 允许您通过 Dockerfile 中的`ARG`指令跳过提及代理环境变量。 <sup id="fnref2">[2](#fn2)</sup>

#### 运行容器时设置代理

如上所述，在映像中设置代理细节或者输入每个新容器来设置代理细节是不可取的。Docker 提供了一种更好的方法，在 run 命令中使用`-e`或`--env`选项。该选项可用于将代理设置作为环境变量传递给容器。

```
> docker container run -e http_proxy nginx #..........................(1)
> docker container run -e https_proxy=<proxyhost:port nginx #.........(2) 
```

注意上面两个命令的区别。如果已经在 docker 主机上定义了`http_proxy`,我们可以直接将它的值传递给容器，只需将变量名传递给`-e`选项，如上面第#(1)行所示。如果您想提供一个替代值，您可以传递 name=value 对，如上面的第(2)行所示。最后，您还可以在一个文件中设置所有代理变量，并使用`--env-file`选项一次性传递所有变量。

```
> cat all-proxy-vars.txt 

http_proxy=http://proxy_host:port
https_proxy=http://proxy_host:port
ftp_proxy=
no_proxy=localhost,127.0.0.1

> docker container run --env-file ./all-proxy-vars.txt nginx 
```

Docker CLI 命令上的环境选项提供了快速调整和测试图像的方法。 <sup id="fnref3">[3](#fn3)</sup>

#### 为 Docker Compose 设置代理——最简单的事情。

虽然上述步骤在您试验图像和容器时会很方便。一旦完成了容器和图像细节，最好的方法就是为构建图像的服务创建一个合成文件。Docker compose 允许您使用`args`指令轻松传递环境细节。您只需要提及代理环境变量的名称，如下所示:

```
version: '2.3'

services: 
    ngnix-service:
        image: my-nginx-image
        build: 
            context: .
            args: # Environment variables available at build-time
                - http_proxy
                - https_proxy
                - no_proxy
        environment: # Environment variables available at container run-time
            - https_proxy
            - http_proxy
            - no_proxy
        volumes: 
......... 
```

如代码片段中所述，定义为`build > arg`级别的环境变量仅在构建映像时可用。然而，`environment`环境变量的级别列表在容器运行时可用。

这些是我打过并以某种方式赢了的公司代理权争夺战。很久以前，我很难通过一个 NTLM 的代理。但是一个叫做 CNTLM 的很酷的工具救了我一命。然而，我没有现成的场景或步骤添加到本文中。

有一点是肯定的，一旦你解决了这样的问题，你就可以很快发现一个新软件的类似问题。当我战斗并赢得更多这样的战斗时，我会添加到这篇文章中。我希望你这篇文章能帮你节省一些时间。

* * *

1.  在这里可以找到关于 vagger-proxy conf 插件的详细说明。[http://tmatilai.github.io/vagrant-proxyconf/](http://tmatilai.github.io/vagrant-proxyconf/)T2】↩

2.  Docker 官方文档解释了如何使用`build-args`[https://docs . docker . com/engine/reference/command line/build/# set-build-time-variables-build-arg](https://docs.docker.com/engine/reference/commandline/build/#set-build-time-variables-build-arg)[↩](#fnref2)

3.  Docker CLI 命令上的环境选项。[https://docs . docker . com/engine/reference/command line/run/# set-environment-variables-e-env-env-file](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e-env-env-file)T2】↩