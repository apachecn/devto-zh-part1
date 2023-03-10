# 备份和恢复 Meteor Mongodb 数据库的超快速方法

> 原文：<https://dev.to/diskdrive/super-quick-way-to-back-up-and-restore-a-meteor-mongodb-database-44ed>

如果你曾经计划在你的 [Meteor](http://meteor.com/) 解决方案中做任何实验，并且不想破坏你现有的数据，这里有一个快速备份和恢复它的方法。

首先，您不能使用 Meteor mongo 命令行，因为那里不存在 Mongodump，所以您必须在您的机器上安装完整的 Mongodb 工具集。我刚刚使用 brew 在我的 Mac 上安装，但说明位于[http://docs . MongoDB . org/manual/tutorial/install-MongoDB-on-OS-x/](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-os-x/)

要备份，只需执行以下命令

mongodump -h 127.0.0.1 -端口 3001 -d

这会将备份放在相对于运行命令的文件夹的文件夹 dump/中。一般我喜欢把它放在文件夹里作为我的 app 解决方案。

要恢复到上次备份的状态，只需执行以下命令

mongorestore -h 127.0.0.1 -端口 3001 -d 陨石坠落转储/

更改数据库名称，这也允许您拥有多个备份。