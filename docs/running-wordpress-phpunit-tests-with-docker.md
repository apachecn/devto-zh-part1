# 用 Docker 运行 WordPress PHPUnit 测试

> 原文：<https://dev.to/foresthoffman/running-wordpress-phpunit-tests-with-docker>

[![](img/d39fe6dafc0a8a877409d5a703072a88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nvlEMqF8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://forest.stfhs.net/forest/wp-content/uploads/2017/04/docker-wordpress-phpunit-820x461.3.jpg)

我最近把我的 web 开发工作区从 MAMP 转移到了 LAMP with Docker。由于我的主机上的鲁比和 RVM 的问题，这个转换很困难。现在它工作了，一切都好了！

当我开始摆弄 Docker 时，我的目标只是看看我是否能复制我的 WordPress 开发环境。之后，我做了一些改进。现在，我已经适当地划分了每个项目。每个 WordPress 项目都有自己的 WordPress 安装、MySQL 服务器、错误日志、插件和主题目录。

唯一缺少的是能够运行单元测试！

我依稀记得上次我设置 PHPUnit 时，让核心 WordPress 测试在 PHPUnit 上运行是一件令人沮丧的事情。这一次同样令人沮丧。我发现一个不幸的缺乏最新和完整的资源来处理这个过程，所以我做了这篇文章来填补空白！我希望这能帮助一些人避免我经历的同样的挫折。如果没有，呃，至少我为未来的自己记录了这个过程。

我在运行`$ phpunit`时不断收到的错误是一些 HTML 内容和大量错误。它的本质是，`<h1>Error establishing a database connection</h1>`。

我没有在每个 WordPress 项目中包含一个测试数据库，而是创建了一个 MySQL 容器供我的所有项目共享。我还将容器配置为每当 Docker 守护进程启动时重新启动，这样它将始终可用。

### 设置 WordPress 核心测试

在与我的其他 WordPress 项目(包含单独的`Dockerfile`和`docker-compose.yml`文件)相同的目录中，我创建了一个`wordpress-develop`目录。在这个新目录中，我克隆了 WordPress 核心测试库。

```
$ svn co https://develop.svn.wordpress.org/trunk/ wordpress-develop 
```

Enter fullscreen mode Exit fullscreen mode

然后我在`wordpress-develop/`中准备了`wp-tests-config.php`文件。我在文件的底部找到了各个全局变量被设置的位置，并将它们更新如下:

```
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'root' );
define( 'DB_PASSWORD', 'root' );
define( 'DB_HOST', '127.0.0.1:914' );

# ...

define( 'WP_TESTS_DOMAIN', 'localhost' );
define( 'WP_TESTS_TITLE', 'Testing' ); 
```

Enter fullscreen mode Exit fullscreen mode

这将数据库名设置为 wordpress，用户名和密码设置为 root，域设置为 localhost。我之前遇到的问题是数据库主机没有识别出值`'localhost:914'`是有效的。当我从`wordpress-develop/`中运行`phpunit`时，这导致连接数据库的尝试失败。通过将`localhost`换成一个原始 IP，`127.0.0.1` PHPUnit 就可以连接了。实际上，我在一个相关的[堆栈交换](http://wordpress.stackexchange.com/questions/203883/database-connection-problem-with-phpunit-and-wordpress)评论中发现了这个修复建议。

我选择 914 端口，因为这些 [UNIX 端口表](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)表明 914-986 范围是正式未分配的。我需要一个不会碍事的端口，而且我的机器上没有使用 914。我跑`$ curl http://127.0.0.1:914`只是为了检查一下。

### 创建 MySQL 守护进程

结合使用`docker run`和`restart`选项，可以创建一个 MySQL 容器，每当 Docker 守护进程启动时，它就会启动。这包括主机重新启动的时间。

```
$ docker run --name wptest-mysql --env MYSQL_ROOT_PASSWORD=root --env MYSQL_DATABASE=wordpress --env MYSQL_USER=root --env MYSQL_PASSWORD=root -p 0.0.0.0:914:3306 --restart=always -d mysql:5.7 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令包含了我们在`wp-tests-config.php`文件中提供的所有数据，还不止这些。`name`选项决定了容器的名称；为了便于识别，我选择了`wptest-mysql`，因为`wordpress-develop-mysql`有点长。`env`选项重复了几次，为容器提供了必要的环境变量。`p`选项将主机上的一个端口映射到容器；因此，`0.0.0.0:914`将主机上的端口`914`映射到容器上的端口`3306`。带有`always`值的`restart`选项表示 Docker 应该尝试重启容器，不管它是如何退出的。`d`选项指示容器基于的图像。我用的是[MySQL 5.7 版](https://hub.docker.com/_/mysql/)。

此时，运行`$ docker ps -a`应该会显示使用本地端口 914 运行的新的`wptest-mysql`容器。

```
$ docker ps -a
24d9f30b5286        mysql:5.7           "docker-entrypoint..."   46 hours ago        Up 46 hours         0.0.0.0:914->3306/tcp   wptest-mysql 
```

Enter fullscreen mode Exit fullscreen mode

如果容器运行正常，导航到您的`wordpress-develop`目录并运行`$ phpunit`。如果一切运行正常，你会看到 WordPress 测试安装开始运行，然后是测试。

### 在别处运行 WordPress 测试

运行 WordPress 核心测试很棒，但是我真的需要运行我自己的测试。为了做到这一点，我选择了 WordPress 测试的路径，并将其分配给我的`~/.bash_profile`中的`WP_TESTS_DIR`环境变量。基于项目的`tests/bootstrap.php`文件依赖于这个环境变量，它需要来自`wordpress-develop`目录的必要的 WordPress 文件。

从我的`~/.bash_profile` :
复制粘贴

```
export DOCKER="$HOME/Documents/Programming/docker"
export WP_DEVELOP_DIR="$DOCKER/wordpress/wordpress-develop"
export WP_TESTS_DIR="$WP_DEVELOP_DIR/tests/phpunit" 
```

Enter fullscreen mode Exit fullscreen mode

如果您不需要其他环境变量，您可以像这样将它们组合在一起(显然，使用您机器上的正确路径):

```
export WP_TESTS_DIR="$HOME/Documents/Programming/docker/wordpress/wordpress-develop/tests/phpunit" 
```

Enter fullscreen mode Exit fullscreen mode

运行`$ . ~/.bash_profile`激活更改。之后，运行项目各自的`phpunit.xml`文件和`tests`目录旁边的`$ phpunit`,应该会运行本地测试，正如预期的那样。

就这样，沃特！