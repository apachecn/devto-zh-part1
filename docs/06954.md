# 最低进度版本

> 原文：<https://dev.to/patricktingen/minimum-progress-version-3p1h>

直到目前的第 23 版，首页都很高兴地说你至少需要 10.1 亿来运行数据挖掘器。

今天，原来那是个谎言 [![😦](img/accbf2b739e1f9c7bc3d05c51434a011.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UJDcIXuh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f626.png)

主程序使用 FINALLY 语句，这是在 10.1C 版本中引入的，因此这是您可以用来运行 DataDigger 的最低版本。

如果你在 10.1B 上卡住了，还想用 DataDigger，只要打开 DataDiggerLib.p 搜索“终于”:

[![101b_upgrade](img/d32f795248f0eb2cf34b9a94bf1a06f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YBhPttyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2017/06/101b_upgrade.png%3Fw%3D840)

完全删除 FINALLY 块。重启时，DataDigger 会重新编译自己。