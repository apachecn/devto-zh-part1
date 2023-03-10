# 如何将所有 MySQL 数据库转储到单独的文件中

> 原文：<https://dev.to/daniel_werner/how-to-dump-all-mysql-databases-into-separate-files-4okc>

有时我们需要从 MySQL 服务器上转储所有的数据库。这可以在启用–all-databases 开关的情况下使用 mysqldump 实用程序来完成。在这种情况下，如果明确指定，mysqldump 会将所有数据库转储到标准输出或文件中。让我们假设这样一种情况，我们希望备份服务器上的所有数据库，一次只能恢复一个数据库。为了实现这一点，您需要一个接一个地转储所有的数据库，或者使用一个预先制作的脚本来为您完成繁重的工作。我在这里找到了一个简单的解决方法:[http://soniahamilton . WordPress . com/2005/11/16/backup-multiple-databases-into-separate-files/](http://soniahamilton.wordpress.com/2005/11/16/backup-multiple-databases-into-separate-files/)。我使用这个脚本作为起点，并对它做了一些改进:

1.  MySQL 用户名可以从命令行指定(它没有硬编码在文件中)

2.  出于安全原因，该脚本要求输入相应的 MySQL 密码(该密码不会被回显或保存在任何地方——正如您在 Unix 系统上所期望的那样)

3.  也可以从命令行指定输出目录

4.  支持 gzipped 输出文件(可选)

## 用法

下载 [dump-all-databases.sh](https://gist.github.com/daniel-werner/5ab30d2e5c566adaad3022f4da9e141d) 。使用 chmod +x dump-all-databases.sh 使其可执行。

运行它:

```
./dump-all-databases.sh -u user -o my\_backup\_dir -z 
```

命令行选项如下:

*   -u–MySQL 用户
*   -o–输出目录(可选，如果未指定，转储文件将放在当前目录中)
*   -z–启用 gzipped 输出(可选)

## 下载

[dump-all-databases.sh](https://gist.github.com/daniel-werner/5ab30d2e5c566adaad3022f4da9e141d)

### 系统要求

安装了 mysql 和 mysqldump 的 Unix/Linux 系统，对于 gzip 输出，还需要 gzip。

帖子[如何将所有 MySQL 数据库转储到单独的文件](https://www.wernerd.info/how-to-dump-all-mysql-databases-into-separate-files/)最先出现在[的 Daniel Werner](https://www.wernerd.info) 上。