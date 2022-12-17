# 用 Php 压缩文件和文件夹

> 原文：<https://dev.to/erhankilic/php-zipmaster---zip-files-and-folders-with-php-d25>

大家好。这是我的第一篇文章。如果有任何错误，请不要查找。

在 PHP 中压缩文件和创建 zip 文件很容易，但是当您在作业中包含文件夹时，情况就不同了。你可能想像我一样自动备份你的服务器上的文件，你可能想有一个解决方案。我有一个 vps 的许多网站，我希望这些网站能够自动备份。我的解决方案是用 PHP 编写一个脚本来创建一个 cronjob。

它使用简单，你可以随心所欲地使用它。如果你想提高，请不要犹豫。

样本使用；

*T2？php*

*包含‘zip master . PHP’；*

*$ zip = new zip master \ zip master(' backup/test . zip '，' test _ folder ')；*
*$ zip->archive()；*

你可以在这里到达项目[。](https://github.com/erhankilic/ZipMaster)