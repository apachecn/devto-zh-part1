# packer+ansi ble-docker file = awesome container

> 原文：<https://dev.to/dzeban/packer--ansible---dockerfile--awesomecontainer-2e17>

作为一名时髦的软件工程师，我使用 Docker 是因为这是一种没有环境设置麻烦的测试软件的好方法。但是作为一个 SRE/德沃普斯类型的人，我也创造我自己的图像——为了 CI 环境，为了实验，有时甚至是为了生产。

我们都知道 Docker 图像是由 Docker 文件组成的，但是在我看来，Docker 文件是愚蠢的——它们很脆弱，会使图像臃肿，看起来像垃圾。对我来说，构建 Docker 图像是一项单调乏味的工作，直到我找到了一个可行的方法。当你有了第一个可行的剧本的时候，你永远不会回头。我立即对 Ansible 的简单自动化工具感到感激，并开始使用 Ansible 提供 Docker 容器。在那段时间里，我找到了 [Ansible Container 项目](http://docs.ansible.com/ansible-container/)，并试图使用它，但在 2016 年，它还没有为我准备好。在我发现 [Hashicorp 的 Packer](https://www.packer.io/) 具有 Ansible provisioning 支持后不久，我就使用这个强大的组合来构建我所有的 Docker 映像。

此后，我想向您展示一个例子，说明它们是如何一起工作的，但首先让我们回到我关于 Dockerfiles 的观点。

## docker files 为什么傻

简而言之，因为 Dockerfile 中的每一行都创建了一个新层。虽然看到分层的 fs 并能够为其他图像重用这些层是很棒的，但在现实中，这是疯狂的。您的图像大小不受控制地增长，现在您有一个用于 python 应用程序的 2GB 图像，并且 90%的图层未被重用。所以，实际上，你不需要所有这些层。

为了挤压图层，你可以使用一些额外的步骤，比如调用 [docker-squash](http://jasonwilder.com/blog/2014/08/19/squashing-docker-images/) ，或者你必须给出尽可能少的命令。这就是为什么在真实的生产 layer 文件中我们会看到太多的`&&`因为用`&&`链接`RUN`命令会创建一个单独的层。

为了说明我的观点，请看最流行的 docker 图像之一的两个 docker 文件——[Redis](https://github.com/docker-library/redis/blob/99a06c057297421f9ea46934c342a2fc00644c4f/3.2/Dockerfile)和 [nginx](https://github.com/nginxinc/docker-nginx/blob/3ba04e37d8f9ed7709fd30bf4dc6c36554e578ac/mainline/stretch/Dockerfile) 。这些 docker 文件的主要部分是带有换行符转义的巨大命令链，带有 sed 和 cleanup 的就地配置补丁作为最后一个命令。

```
RUN set -ex; \
    \
    buildDeps=' \
 wget \
  \
 gcc \
 libc6-dev \
 make \
 '; \
    apt-get update; \
    apt-get install -y $buildDeps --no-install-recommends; \
    rm -rf /var/lib/apt/lists/*; \
    \
    wget -O redis.tar.gz "$REDIS_DOWNLOAD_URL"; \
    echo "$REDIS_DOWNLOAD_SHA *redis.tar.gz" | sha256sum -c -; \
    mkdir -p /usr/src/redis; \
    tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1; \
    rm redis.tar.gz; \
    \ # disable Redis protected mode [1] as it is unnecessary in context of Docker
# (ports are not automatically exposed when running inside Docker, but rather explicitly by specifying -p / -P)
# [1]: https://github.com/antirez/redis/commit/edd4d555df57dc84265fdfb4ef59a4678832f6da
    grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 1$' /usr/src/redis/src/server.h; \
    sed -ri 's!^(#define CONFIG_DEFAULT_PROTECTED_MODE) 1$!\1 0!' /usr/src/redis/src/server.h; \
    grep -q '^#define CONFIG_DEFAULT_PROTECTED_MODE 0$' /usr/src/redis/src/server.h; \
# for future reference, we modify this directly in the source instead of just supplying a default configuration flag because apparently "if you specify any argument to redis-server, [it assumes] you are going to specify everything"
# see also https://github.com/docker-library/redis/issues/4#issuecomment-50780840
# (more exactly, this makes sure the default behavior of "save on SIGTERM" stays functional by default)
    \
    make -C /usr/src/redis -j "$(nproc)"; \
    make -C /usr/src/redis install; \
    \
    rm -r /usr/src/redis; \
    \
    apt-get purge -y --auto-remove $buildDeps 
```

Enter fullscreen mode Exit fullscreen mode

所有这些疯狂都是为了避免图层创建。这就是我想问一个问题的地方——这是 2017 年最好的做事方式吗？真的吗？对我来说，所有这些 Dockerfiles 看起来就像一个穷人的 bash 脚本。天哪，我讨厌巴什。但另一方面，我喜欢容器，所以我需要一个整洁的方式来对抗这种疯狂。

## 在 Dockerfile 中可回答

我们可以编写一个可重用的 Ansible 角色，从剧本中调用它，而不是放入原始的 bash 命令，剧本将在 Docker 容器中使用来提供它。

我是这样做的

```
FROM debian:9

# Bootstrap Ansible via pip
RUN apt-get update && apt-get install -y wget gcc make python python-dev python-setuptools python-pip libffi-dev libssl-dev libyaml-dev
RUN pip install -U pip
RUN pip install -U ansible

# Prepare Ansible environment
RUN mkdir /ansible
COPY . /ansible
ENV ANSIBLE_ROLES_PATH /ansible/roles
ENV ANSIBLE_VAULT_PASSWORD_FILE /ansible/.vaultpass

# Launch Ansible playbook from inside container
RUN cd /ansible && ansible-playbook -c local -v mycontainer.yml

# Cleanup
RUN rm -rf /ansible
RUN for dep in $(pip show ansible | grep Requires | sed 's/Requires: //g; s/,//g'); do pip uninstall -y $dep; done
RUN apt-get purge -y python-dev python-pip
RUN apt-get autoremove -y && apt-get autoclean -y && apt-get clean -y
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp* /usr/share/doc/*

# Environment setup
ENV HOME /home/test
WORKDIR /
USER test

CMD ["/bin/bash"] 
```

Enter fullscreen mode Exit fullscreen mode

将这个 Docker 文件放到您的 Ansible repo 的根目录下，它将使用您的剧本、角色、库存和金库密码来构建 Docker 映像。

它有效，可重复使用，例如，我有一些应用于 docker 容器和裸机的基本角色，在 Ansible 中配置更容易维护。但还是觉得很尴尬。

## 带供应器的封隔器

于是我更进一步，开始用 Packer。Packer 是一款专门用于创建机器图像的工具。它不仅可用于构建容器映像，还可用于为 AWS 和 GCP 等云提供商构建虚拟机映像。

它立刻让我联想到文档中的这些句子:

> Packer 在不使用 Docker 文件的情况下构建 Docker 容器。通过不使用 Docker 文件，Packer 能够为容器提供不依赖于 Docker 的可移植脚本或配置管理系统。它也有一个简单的心理模型:你提供容器就像你提供一个普通的虚拟化或专用服务器一样。

这就是我之前想用我的解析 docker 文件实现的目标。

所以让我们来看看如何建立 Redis 的形象，几乎是相同的官方。

### 用 Packer 和 Ansible 构建 Redis 图像

首先，让我们创建一个游乐场目录

```
$ mkdir redis-packer && cd redis-packer 
```

Enter fullscreen mode Exit fullscreen mode

Packer 由 JSON 格式的声明性配置控制。这里是我们的:

```
{  "builders":  [{  "type":  "docker",  "image":  "debian:jessie-slim",  "commit":  true,  "changes":  [  "VOLUME /data",  "WORKDIR /data",  "EXPOSE 6379",  "ENTRYPOINT [\"docker-entrypoint.sh\"]",  "CMD [\"redis-server\"]"  ]  }],  "provisioners":  [{  "type":  "ansible",  "user":  "root",  "playbook_file":  "provision.yml"  }],  "post-processors":  [[  {  "type":  "docker-tag",  "repository":  "docker.io/alexdzyoba/redis-packer",  "tag":  "latest"  }  ]]  } 
```

Enter fullscreen mode Exit fullscreen mode

把这个放到`redis.json`文件中，让我们弄清楚这意味着什么。

首先，我们描述我们的建设者——我们要建立什么样的形象。在我们的例子中，这是一个基于`debian:jessie-slim`的 Docker 图像。`commit: true`告诉我们在完成所有设置后，我们希望提交更改。另一个选项是用[的`export_path`选项](https://www.packer.io/docs/builders/docker.html#required-)导出到 tar 存档。

接下来，我们描述我们的 provisioner，这是 Ansible 将介入游戏的地方。封隔器支持两种模式——[本地](https://www.packer.io/docs/provisioners/ansible-local.html)和[远程](https://www.packer.io/docs/provisioners/ansible.html)。

本地模式(`"type": "ansible-local"`)意味着 Ansible 将在 Docker 容器中启动——就像我之前的设置一样。但是 Ansible 不会被 Packer 安装，所以你必须自己用 [`shell` provisioner](https://www.packer.io/docs/provisioners/shell.html) 来安装——类似于我在 Dockerfile 中的 Ansible 自举。

远程模式意味着 Ansible 将在您的构建主机上运行，并通过 SSH 连接到容器，因此您不需要在 Docker 容器中安装完整的 ansi ble——只需要一个 Python 解释器。

因此，我使用远程 Ansible 作为根用户连接并启动`provision.yml`行动手册。

供应完成后，打包程序进行后处理。我只是做了标记的图像，但你也可以推到 Docker 注册表。

现在我们来看看 provision.yml 剧本:

```
--------

- name: Provision Python
  hosts: all
  gather_facts: no
  tasks:
    - name: Boostrap python
      raw: test -e /usr/bin/python || (apt-get -y update && apt-get install -y python-minimal)

- name: Provision Redis
  hosts: all

  tasks:
    - name: Ensure Redis configured with role
      import_role:
        name: alexdzyoba.redis

    - name: Create workdir
      file:
        path: /data
        state: directory
        owner: root
        group: root
        mode: 0755

    - name: Put runtime programs
      copy:
        src: files/{{ item }}
        dest: /usr/local/bin/{{ item }}
        mode: 0755
        owner: root
        group: root
      with_items:
        - gosu
        - docker-entrypoint.sh

- name: Container cleanup
  hosts: all
  gather_facts: no
  tasks:
    - name: Remove python
      raw: apt-get purge -y python-minimal && apt-get autoremove -y

    - name: Remove apt lists
      raw: rm -rf /var/lib/apt/lists/* 
```

Enter fullscreen mode Exit fullscreen mode

行动手册包括 3 个行动:

1.  为 Ansible 提供 Python
2.  使用我的角色提供 Redis
3.  容器清理

要为 Ansible 提供容器(或任何其他主机)，我们需要安装 Python。但是如何通过 Ansible 为 Ansible 安装 Python 呢？对于这种情况，有一个特殊的 Ansible [`raw`模块](https://docs.ansible.com/ansible/2.4/raw_module.html)——它不需要 Python 解释器，因为它通过 SSH 执行裸 shell 命令。我们需要用`gather_facts: no`来调用它，以跳过调用在 Python 中完成的事实收集。

Redis 配置是通过[我的责任角色](https://galaxy.ansible.com/alexdzyoba/redis/)完成的，其步骤与官方 Redis Dockerfile 完全相同——它创建`redis`用户和组，下载源代码 tarball，禁用保护模式，编译它并进行构建后清理。在 Github 上查看详情[。](https://github.com/alexdzyoba/ansible-redis)

最后，我们通过移除 Python 和清理包管理内容来清理容器。

只剩下 2 个东西——gosu 和 docker-entrypoint.sh 文件。这些文件以及打包器配置和可执行角色可从[我的 redis-packer Github repo](https://github.com/alexdzyoba/redis-packer) 获得

最后，我们要做的就是像这样发射它

```
$GOPATH/bin/packer build redis.json 
```

Enter fullscreen mode Exit fullscreen mode

您可以在本要点的[中看到示例输出](https://gist.github.com/dzeban/e556361b2bc1fca2f12803af4f284ad7)

最后，我们得到了一张比官方图片还要小一点的图片:

```
$ docker images
REPOSITORY TAG IMAGE ID CREATED SIZE
docker.io/alexdzyoba/redis-packer latest 05c7aebe901b 3 minutes ago 98.9 MB
docker.io/redis 3.2 d3f696a9f230 4 weeks ago 99.7 MB 
```

Enter fullscreen mode Exit fullscreen mode

## 有什么弊端吗？

当然，我的解决方案也有缺点。首先，你必须学习新的工具——封隔器和 Ansible。但是我强烈建议学习 Ansible，因为在您的项目中，其他类型的自动化也需要它。你确实能自动完成任务，对吗？

第二个缺点是，现在容器构建更多地涉及到所有的打包配置、可转换的角色和剧本等等。按代码行数计算，现在有 174 行代码

```
$ (find alexdzyoba.redis -type f -name '*.yml' -exec cat {} \; && cat redis.json provision.yml) | wc -l
174 
```

Enter fullscreen mode Exit fullscreen mode

而原来只有 77:

```
$ wc -l Dockerfile
77 Dockerfile 
```

Enter fullscreen mode Exit fullscreen mode

我再次建议你走这条路，因为:

1.  它是可重复使用的。您不仅可以为容器应用 Redis 角色，还可以为 EC2 实例或裸机服务或几乎任何使用 SSH 运行 Linux 的东西应用 Redis 角色。
2.  它是可维护的。几个月后再回来，你仍然会明白发生了什么，因为 Packer 配置，剧本和角色是结构化，甚至是注释的。您可以使用一个简单的`packer build redis.json`命令来构建映像，以生成就绪的和带标签的映像。
3.  它是可扩展的。您可以使用几乎相同的角色，通过简单地传递`redis_version`和`redis_download_sha`变量来提供 Redis 版本 4.0.5。不需要新的 docker 文件。

## 结论

这就是我现在的 Docker 图像构建设置。这对我来说效果很好，我现在有点喜欢这个过程。我也想再看看 Ansible Container，但那将是另一篇文章，所以请继续关注——这个博客有 [Atom feed](https://dev.to/feed) ,我也在 twitter @AlexDzyoba 上发表了