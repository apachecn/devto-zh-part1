# Ubuntu 安装程序

> 原文：<https://dev.to/mohanarpit/ubuntu-setup>

新操作系统或新笔记本电脑带来的兴奋是显而易见的。但是安装您需要完成工作的应用程序可能会涉及一点苦差事。我以 shell 脚本的形式将设置正式化，以确保我需要的大多数基本应用程序都是自动安装的。你可以在 https://github.com/mohanarpit/Ubuntu-Initializer 找到完整的剧本。

您需要做的就是复制脚本`install_script.sh`并执行它。它安装的一些应用程序有:

*   **源代码管理**:SVN Git
*   **语言** : Python，Java，Php，Go，Nodejs
*   **编辑**:原子
*   数据库 : Mysql
*   **服务器** : Tomcat，Apache2
*   **实用程序** : Tmux、fail2ban、jq 等。

如果您还没有密钥对，它甚至会创建您的密钥对。正如你所看到的，它是面向网络开发者的，但是没有什么能阻止你定制脚本来增加/减少你需要的应用程序。

这些年来，这个脚本为我和我的团队节省了大量寻找和安装基本应用程序的时间。

最初发布在[我的博客](https://blog.arpitmohan.com/Ubuntu-Initializer)