# 使用 Raspberry Pi 3 建立公共 web 服务器

> 原文：<https://dev.to/vatsalyagoel/setting-up-a-public-web-server-using-a-raspberry-pi-3-3n89>

[![Setting up a public web server using a Raspberry Pi 3](img/c079d90aacfbaf665f92b1cba568de0b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pNDUfe6I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vatsalyagoel.com/conteimg/2018/06/Raspberry_PI.jpeg)

最近，我得到了一台 Raspberry Pi 3 Model B。出于建立家庭自动化系统的愿望，我首先想看看是否可以通过我家的互联网连接公开访问 Pi。如果不是我的 ISP 在 NAT-1 交换机后锁定路由器，这将是最简单的任务。这粉碎了我想做自己想做的事情的梦想。

我开始寻找在线解决方案，以安全地从 Pi 到 internet 建立隧道，并获得一个可公开访问的域。我就是在这里遇到了 [Ngrok](https://ngrok.com/) ，这是一种允许你在一台机器上隧道化一个端口并将其公开的服务。他们的用例围绕着没有部署的演示和测试。然而，我试图重新利用它在我的 Raspberry Pi 上设置一个网络服务器。

## 设置树莓派

我们需要什么:

*   树莓派 3
*   至少 8 GB 空间的微型 SD 卡(建议 16 GB)
*   SD 卡读卡器
*   Raspbian 操作系统可以从[这里](https://www.raspberrypi.org/downloads/raspbian/)下载
*   可以从[这里](https://etcher.io/)下载的蚀刻机
*   互联网连接(无线或有线)

从 raspbian 下载页面下载`Raspbian Stretch with Desktop`。将微型 SD 卡插入读卡器。从下载中解压 Raspbian 映像，并使用 Etcher 将映像写入 SD 卡。

将 micro SD 卡插入 Pi 并打开电源。在第一次启动时，您没有办法远程连接到 Pi。要配置远程访问，请使用 HDMI 端口将 Pi 插入显示器。连接鼠标和键盘以配置系统。转到`Menu > Preferences > Raspberry Pi Configuration`并在`localisation`选项卡下配置您的时区、区域设置和键盘布局。In 需要这样做，因为默认区域设置为英国，而键盘布局不是我使用的标准布局。进入`Interfaces`选项卡，启用`SSH`和`VNC`。这样，您就可以远程访问 Pi，而无需连接任何外围设备。

打开一个新的终端会话并运行`passwd`以更改密码。默认密码是`raspberry`

## 配置远程访问

打开一个新的终端会话，运行`ifconfig`并记下 IP 地址

[![Setting up a public web server using a Raspberry Pi 3](img/7b9c346c639e87a02633e83df9a9af03.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--l3s9yFIA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vatsalyagoel.com/conteimg/2017/10/ifconfig.PNG)

运行以下命令:

```
$ sudo apt-get update
$ sudo apt-get install realvnc-vnc-server
$ reboot
$ sudo systemctl enable vncserver-x11-serviced.service
$ sudo systemctl start vncserver-x11-serviced.service 
```

现在应该可以通过图形和终端远程访问您的 Raspberry Pi 了。现在，您可以断开任何不需要连接的外围设备。

要以图形方式连接到 Raspberry Pi，请下载 [RealVNC Viewer](https://www.realvnc.com/en/connect/download/viewer/)

启动 VNC 浏览器。转到`File > New Connection`在`VNC server`字段中输入上述 IP，并给出您能记住的 Pi 友好名称。双击您在仪表板上看到的新系统，连接到 pi。你应该看到一个这样的窗口。

[![Setting up a public web server using a Raspberry Pi 3](img/de0be45ddfe4528bdea1a2192482dd9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8ZhCXS2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vatsalyagoel.com/conteimg/2017/10/vncViewer-1.PNG) 

输入`pi`作为用户名，之前设置的密码作为密码。

现在，您应该有一个到 Pi 的远程连接。

## 设置 web 服务器

对于这个任务，我决定使用 Nginx，因为它是轻量级的。如果需要，您可以决定使用 Apache。

要设置 Nginx:

```
$ sudo apt-get install nginx
$ sudo systemctl enable nginx.service
$ sudo systemctl start nginx.service 
```

在浏览器的树莓派中浏览到 127.0.0.1，您应该会看到一个`Welcome to Nginx`页面。

[![Setting up a public web server using a Raspberry Pi 3](img/b3749c8656f46350f42c03beda382d3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OluS3D0g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vatsalyagoel.com/conteimg/2017/10/welcome-to-nginx-window-5x3.png) 

要更改站点的内容，请导航到`/var/www/nginx`并将内容更改到您想要部署的站点。

## 公开访问网站

现在我们已经有了 web 服务器设置，我们希望它可以公开访问。这就是 Ngrok 的用武之地。前往[https://ngrok.com](https://ngrok.com)注册账号。记下他们给你的认证令牌。这将用于设置隧道。

[通过运行以下命令下载](https://ngrok.com/download)并安装 Ngrok 的 Linux ARM 版本

```
$ cd /tmp 
$ wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-arm.zip
$ sudo mkdir /etc/ngrok
$ unzip ./ngrok-stable-linux-arm.zip -d /etc/ngrok/
$ rm ./ngrok-stable-linux-arm.zip
$ ln -s /etc/ngrok/ngrok /usr/local/bin/ngrok 
```

用下面的内容创建一个新文件`~/.ngrok2/ngrok.yml`。这是 Ngrok 开始建立隧道时查看的配置文件。如果你想保护对网站的访问，你可以添加一个认证选项`auth: "username:password"`，这将通过基本认证来保护你的网站

```
authtoken: your-auth-token
tunnels:
  pi:
    proto: http
    addr: 80 
```

一旦我们配置了 Ngrok，我们需要设置它作为服务运行。这样我们就不需要保持终端会话运行。

创建一个具有`root`权限的新文件`lib/systemd/system/ngrok.service`。

```
[Unit]
Description=Ngrok
After=multi-user.target

[Service]
Type=simple
ExecStart=/etc/ngrok/ngrok start -config /home/ **yourusername** /.ngrok2/ngrok.yml --all
Restart=on-abort

[Install]
WantedBy=multi-user.target 
```

要启动服务

```
$ systemctl daemon-reload
$ sudo systemctl enable ngrok.service
$ sudo systemctl start ngrok.service 
```

导航到`http://127.0.0.1:4040/status`，你应该会看到你的 ngrok 域名。

[![Setting up a public web server using a Raspberry Pi 3](img/1438156b75ef16e7da27d273be42aab0.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--xkAfCq0f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://vatsalyagoel.com/conteimg/2017/10/ngrok-status.PNG)

如果你想设置一个自定义域名，它只适用于付费计划。查看价格，如果你觉得有用，选择最适合你的计划。

导航到您从仪表板获得的 URL。恭喜你！现在，您在 Raspberry Pi 上拥有了一个公共 web 服务器。