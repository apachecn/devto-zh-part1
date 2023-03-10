# SDKMAN！Java 的多个版本

> 原文：<https://dev.to/wpanas/sdkman-multiple-versions-of-java-39ad>

# 什么是 SDKMAN！

[SDKMAN！](https://sdkman.io/)是一款开发工具，可以让你安装和管理 Java、Scala、Groovy、Maven 等多种工具、库和编程语言统一在 JVM 平台下。它是为 GNU/Linux 和 MacOS 用户创建的。

# 安装

第一步是将这个命令粘贴到您的终端中并提交它。

```
curl -s "https://get.sdkman.io" | bash 
```

这将启动安装过程，可能需要您的输入。成功安装后，粘贴此命令，您就可以开始工作了。

```
source "$HOME/.sdkman/bin/sdkman-init.sh" 
```

# 用法

您可以使用以下命令列出所需 SDK 的所有可用版本:

```
sdk list <sdk> 
```

下面是 Groovy 的这个命令的输出。

```
sdk list groovy

================================================================================
Available Groovy Versions
================================================================================
     3.0.0-beta-3        2.4.8               2.1.4               1.7.6          
     3.0.0-beta-2        2.4.7               2.1.3               1.7.5          
     3.0.0-beta-1        2.4.6               2.1.2               1.7.4          
     3.0.0-alpha-4       2.4.5               2.1.1               1.7.3          
     3.0.0-alpha-3       2.4.4               2.1.0               1.7.2          
     3.0.0-alpha-2       2.4.3               2.0.8               1.7.1          
     3.0.0-alpha-1       2.4.2               2.0.7               1.7.0          
     2.6.0-alpha-4       2.4.1               2.0.6               1.6.9          
     2.6.0-alpha-3       2.4.0               2.0.5               1.6.8          
     2.6.0-alpha-2       2.3.11              2.0.4               1.6.7          
     2.6.0-alpha-1       2.3.10              2.0.3               1.6.6          
     2.5.8               2.3.9               2.0.2               1.6.5          
     2.5.7               2.3.8               2.0.1               1.6.4          
     2.5.6               2.3.7               2.0.0               1.6.3          
     2.5.5               2.3.6               1.8.9               1.6.2          
     2.5.4               2.3.5               1.8.8               1.6.1          
     2.5.3               2.3.4               1.8.7               1.6.0          
     2.5.2               2.3.3               1.8.6               1.5.8          
     2.5.1               2.3.2               1.8.5               1.5.7          
     2.5.0               2.3.1               1.8.4               1.5.6          
     2.4.17              2.3.0               1.8.3               1.5.5          
     2.4.16              2.2.2               1.8.2               1.5.4          
     2.4.15              2.2.1               1.8.1               1.5.3          
     2.4.14              2.2.0               1.8.0               1.5.2          
     2.4.13              2.1.9               1.7.11              1.5.1          
     2.4.12              2.1.8               1.7.10              1.5.0          
     2.4.11              2.1.7               1.7.9                              
     2.4.10              2.1.6               1.7.8                              
     2.4.9               2.1.5               1.7.7                              

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================ 
```

安装毫不费力。选择任何可用的版本或跳过它，安装当前稳定的版本。

```
sdk install <sdk> (<version>) 
```

让我们试着安装 Java 11。粘贴`sdk install java 11.0.4-hs-adpt`并点击回车。

```
sdk install java 11.0.4.hs-adpt

Downloading: java 11.0.4.hs-adpt

In progress...

######################################################################## 100,0%

Repackaging Java 11.0.4.hs-adpt...

Done repackaging...

Installing: java 11.0.4.hs-adpt
Done installing!

Do you want java 11.0.4.hs-adpt to be set as default? (Y/n): 

Setting java 11.0.4.hs-adpt as default. 
```

所有已安装的 Java 版本都可以在文件夹`$SDKMAN_DIR/candiadates/java`中找到。

```
ls $SDKMAN_DIR/candidates/java -l
total 8
drwxr-xr-x.  9 wpanas wpanas 4096 10-18 17:44 8u152-zulu
drwxr-xr-x. 10 wpanas wpanas 4096 11-04 16:22 9.0.1-zulu
lrwxrwxrwx.  1 wpanas wpanas   47 12-25 17:23 current -> /home/wpanas/.sdkman/candidates/java/11.0.4.hs-adpt 
```

您可以通过检查当前 Java 版本来检查安装是否成功。命令`java --version`在 Java 8 或任何以前的版本中都不能工作，所以让我们来试试。

```
java --version
openjdk 11.0.4 2019-07-16
OpenJDK Runtime Environment AdoptOpenJDK (build 11.0.4+11)
OpenJDK 64-Bit Server VM AdoptOpenJDK (build 11.0.4+11, mixed mode) 
```

正如您所看到的，Java 11 已经安装好并准备好工作了。如果您想切换默认 Java 的版本，请使用命令`sdk default java <version>`，但是您也可以使用`sdk use java <version>`切换当前终端会话的版本。如果你想检查你的代码在不同的 Java 版本上的表现，这是非常方便的。

# 总结

SDKMAN！是使用 Java、Groovy 或任何其他 JVM 语言的开发人员的必备工具。去看看吧，你不会失望的。