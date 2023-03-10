# 如何在 Linux (Ubuntu)上安装 phpmyadmin

> 原文：<https://dev.to/xeroxism/how-to-install-phpmyadmin-on-linux-ubuntu-4mdn>

[![phpmyadmin_fossnaija](img/230479db98ade4e8bf3d9b818f4386ae.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2017/08/phpmyadmin.png?ssl=1)

通过在终端运行命令，可以使用命令行管理 MySQL 数据库服务器。但是使用图形用户界面(phpmyadmin)更方便，也更简单——尤其是对于那些不习惯在终端上输入命令的人来说。

phpMyAdmin 是一个用<u>[PHP](https://php.net/)</u>编写的免费开源 GUI 软件工具，旨在通过 Web 处理<u>[MySQL](https://www.mysql.com/)</u>的管理。phpMyAdmin 支持 MySQL 和 MariaDB 上的多种操作。

经常使用的操作(管理数据库、表、列、关系、索引、用户、权限等)可以通过用户界面执行，同时您仍然能够直接执行任何 SQL 语句。还可以以流行的文件格式(CSV、SQL、XML、PDF、Word、Excel、LaTeX 等)执行数据的导出和导入。phpMyAdmin 是跨平台的，是最流行的 MySQL 管理工具之一。

在这篇文章中，我们将把它安装在 Linux 操作系统上。但是在我们开始之前，请确保您系统上已经安装了 Apache、MySQL 和 PHP。如果没有，就在这里做。

## 安装 phpMyAdmin。

使用 Ubuntu 软件包管理器 apt(您可以使用其他软件包管理器来管理各自的 Linux 发行版)，打开终端。

**sudo apt-get 安装 phpmyadmin**

然后开始安装。出现提示时，从“配置 phpMyAdmin”对话框中选择“Apache2”。当询问 MySQL 用户名和密码时，输入“ *root* 作为用户名，输入 *YOUR_MYSQL_PASSWORD* 作为密码。

安装完成后，将 phpMyAdmin 配置为被本地 web 服务器识别。

在您喜欢的文本编辑器中打开 apache 配置文件；

**sudo gedit/etc/Apache 2/Apache 2 . conf**

并在文件的底部添加下面一行(您可以在文件的任何地方添加，我只是在这里选择底部，以便您可以方便地访问它进行修改):

*Include/etc/phpmyadmin/Apache . conf*

然后重新启动 web 服务器；

**sudo 服务 apache2 重启**

现在，在您的浏览器中输入以下 url

<u>*[https://localhost/phpmyadmin](https://localhost/phpmyadmin)*</u>

如果您还没有输入用户名和密码，您应该会看到一个登录页面。

用户名= root

密码=您的 MYSQL 密码

[![phpmyadmin_login_fossnaija](img/70d6c071ce34ae3ef4fa55fa8d568d36.png)T2】](https://i2.wp.com/fossnaija.com/wp-content/uploads/2017/08/PMA-e1501723966144.png?ssl=1)

图片:化石人. COM

如果成功，将出现 phpMyAdmin 仪表板:

[![phpmyadmin_homepage_fossnaija](img/c63e0883ce6614902739976ab23fa5e3.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2017/08/PMA-homepage-e1501724058455.png?ssl=1)

phpmyadmin 网页。

*祝贺你！*

Linux 'NG 快乐！ _

帖子[如何在 Linux (Ubuntu)](https://fossnaija.com/install-phpmyadmin-linux-ubuntu/) 上安装 phpmyadmin 最早出现在 [Foss Naija](https://fossnaija.com) 上。