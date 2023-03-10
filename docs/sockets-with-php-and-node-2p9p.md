# 使用 PHP 和 Node 的套接字

> 原文：<https://dev.to/sarfraznawaz2005/sockets-with-php-and-node-2p9p>

我希望通过套接字实现实时通知系统，而不必使用任何第三方服务，如[推](https://pusher.com/)等。我只是想能够从 PHP 端向客户端发送通知，并立即在 web 应用程序上显示它们，类似于脸书通知。

首先，我遇到了棘轮库，它工作得很好，但在我的主机(siteground)上设置安全连接(https)时出现了问题，尝试了 apache 代理模块和互联网上告诉我的一切，但都无济于事(似乎 siteground 在 https 上有棘轮问题)，所以最后我不得不放弃棘轮。

然后我想到将 [socket.io](https://socket.io/) 与 Node+PHP 一起使用，通过我的研究，我发现了 [elephant.io](https://github.com/Wisembly/elephant.io) 库(尽管它最近没有更新),这个库在非安全和安全协议上都工作得非常好，允许我们通过基于节点的服务器从 PHP 发送和接收消息。

下面是我让我的通知系统工作的步骤。

**安装 elephant.io**

对于您的 PHP 应用程序，通过 composer:
安装`elephant.io`

```
composer require wisembly/elephant.io 
```

**安装节点依赖关系**

在您的项目根目录下创建一个目录，并在该目录下创建一个名为`package.json`的文件，其中包含以下内容:

```
{
    "name": "elephantIO_example_emitter",
    "version": "3.0.0",
    "main": "server.js",

    "scripts": {
        "start": "supervisor --debug server.js"
    },

    "dependencies": {
        "socket.io": "~1",
        "winston": "*"
    }
} 
```

在新创建的目录上运行命令`npm install --save`。这将安装`socket.io`和记录器库。

在同一个新创建的目录下，创建一个文件`server.js`，其内容为:

```
var server = require('http').createServer(),
    io = require('socket.io')(server),
    logger = require('winston'),
    port = 1337;

// Logger config
logger.remove(logger.transports.Console);
logger.add(logger.transports.Console, { colorize: true, timestamp: true });
logger.info('SocketIO > listening on port ' + port);

io.on('connection', function (socket){
    var nb = 0;

    logger.info('SocketIO > Connected socket ' + socket.id);

    socket.on('broadcast', function (message) {
        ++nb;
        logger.info('ElephantIO broadcast > ' + JSON.stringify(message));

        // send to all connected clients
        io.sockets.emit("broadcast", message);
    });

    socket.on('disconnect', function () {
        logger.info('SocketIO : Received ' + nb + ' messages');
        logger.info('SocketIO > Disconnected socket ' + socket.id);
    });
});

server.listen(port); 
```

通过键入`node server.js`在节点上运行`server.js`文件，您应该会看到服务器已经在指定端口上启动的消息。

**客户端**

将以下 javascript 代码放入应用程序的页面/页脚:

```
<script src='//cdnjs.cloudflare.com/ajax/libs/socket.io/1.7.4/socket.io.min.js'></script>

<script>
var socket = io.connect('//127.0.0.1:1337');

socket.on('connect', function () {
    console.log('connected');

    socket.on('broadcast', function (data) {
        //console.log(data);
        //socket.emit("broadcast", data);
        alert(data.text);
    });

    socket.on('disconnect', function () {
        console.log('disconnected');
    });
});
</script> 
```

**从 PHP 发送通知**

以下是向所有连接的客户端发送消息的方法:

```
require __DIR__. '/vendor/autoload.php';

use ElephantIO\Client;
use ElephantIO\Engine\SocketIO\Version1X;

$client = new Client(new Version1X('//127.0.0.1:1337'));

$client->initialize();
// send message to connected clients
$client->emit('broadcast', ['type' => 'notification', 'text' => 'Hello There!']);
$client->close(); 
```

这就是全部了。

**在生产现场安装和运行节点**

我在`CentOSv6`上，按照[这个指南](https://www.a2hosting.com/kb/installable-applications/manual-installations/installing-node-js-on-managed-hosting-accounts)安装了 node。然后，我创建了一个简单的 php 文件，它将由 cron 运行，这样，如果节点服务器没有运行，它将自动启动/重启:

```
$nodePath = 'your node binary path here';
$filePath = 'your server.js file path';
shell_exec($nodePath . ' ' . $filePath); 
```

然后在 cron 中指定该文件在指定时间间隔运行。

**重要提示**

*   我从最初安装它的 PHP 脚本中获取节点路径时遇到了一点问题，所以我将 nodejs 文件夹复制到我的`public_html`文件夹(应用程序的根目录)。
*   在生产网站上，我必须键入实际的服务器 IP，而不是客户端代码上的`127.0.0.1`，例如，而不是`var socket = io.connect('//127.0.0.1:1337');`，它必须是`var socket = io.connect('//SERVER_IP_HERE:1337');`
*   如果你在让它在 https 上工作时有问题，请查看[问题部分](https://github.com/Wisembly/elephant.io/issues?utf8=%E2%9C%93&q=)来解决它。
*   查看`elephant.io`的**示例**文件夹，了解如何从 PHP 和 Javascript 发送或接收消息。![](img/3c133f92ed0fa27fe45e7b7518c74d20.png)