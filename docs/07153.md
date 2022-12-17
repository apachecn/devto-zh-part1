# 不和谐地使用 Travis CI

> 原文：<https://dev.to/legolord208/using-travis-ci-with-discord>

一个鲜为人知的事实是 Travis CI [不支持不和](https://github.com/travis-ci/travis-tasks/pull/71)。
很长一段时间以来，这件事一直困扰着我，但从来没有足够的时间去做任何事情...直到现在。
所以我决定用 [Techtony96 的 PHP 脚本](https://github.com/Techtony96/TravisCI-Webhooks/)，把它转换成我的 [Go webserver](https://legolord208.github.io/krakeone/) 。

用起来很简单！
[![Imgur](img/d458766474ddbd85a4c1716a76d27b81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RYtGM7Na--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/QNSWghp.png) 
举例:[https://krake . one/Travis-ci/123456789123456789/abcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrqrstuwxyz](https://krake.one/travis-ci/123456789123456789/abcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyzabcdefghijklmnopqrstuvwxyz)

尽情享受吧！