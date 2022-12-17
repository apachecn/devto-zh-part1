# Sparrowdo 提供的流浪者

> 原文：<https://dev.to/melezhik/vagrant-provisioning-with-sparrowdo-4bb9>

# 雀儿无业游民支援

随着 [Sparrowdo](https://github.com/melezhik/sparrowdo) 项目的最新提交，可以提供流浪者盒子。

界面可能会改变，但是对于那些好奇正在发生什么的人来说:

# 旋转你的流浪主机

```
$ vagrant up 
```

Enter fullscreen mode Exit fullscreen mode

# 创建一些 Sparrowdo 场景

```
$ nano sparrowfile 
```

Enter fullscreen mode Exit fullscreen mode

```
#!perl6
directory "/opt/my/directory";
file "/opt/my/directory/file", %( content => "hello" );
package-install "nginx";
service-start "nginx"; 
```

Enter fullscreen mode Exit fullscreen mode

# 通过 ssh 在流浪主机上运行 sparrowdo 场景

```
$ sparrowdo --vagrant --bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

# 这为什么有用？

*   您不希望为浮动 shell 供应而烦恼，这可能很困难
*   您将供应逻辑和引导逻辑(`sparrowfile`和`Vagrantfile`)分开
*   你想在你的流浪者盒子上尝试漂亮的 Sparrowdo 配置管理工具吗