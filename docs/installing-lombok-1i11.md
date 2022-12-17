# 安装 Lombok

> 原文：<https://dev.to/committedsw/installing-lombok-1i11>

[![Lombok, Indonesia](img/c479380b01be921716eb278c80baa1e7.png)T2】](///static/atilla-taskiran-386870-342da5d49e5aaf02d48045b3454abe69-7a1db.jpg)

Lombok 现在在 Spring Cloud 中使用，尽管许多 Java 开发人员都避免使用它，但是去掉样板文件的想法对我们所有人都有吸引力。现在我们在 JDK 8 站有了兰姆达斯，这是采取额外措施进一步减少脂肪的好时机。

安装过程中似乎有很多问题，所以我们从 Eclipse Mars 或 Spring 工具套件的安装经验中总结了一个指南。

如果 Eclipse 和 Spring 工具套件正在运行，请关闭它们。

从他们的[网站](https://projectlombok.org/download.html)下载 Lombok。然后你应该运行 lombok.jar:

```
java -jar lombok.jar 
```

您将看到一个小 GUI，要求您选择 Eclipse 安装。如果你已经安装到一个默认位置，它可能会自动找到它们，但是假设没有，那么你应该浏览并选择 *ini* 文件——也就是 eclipse.ini 或 STS.ini。

Lombok 安装程序将在 ini 文件中添加几行:

```
openFile
-vmargs
-Dosgi.requiredJavaVersion=1.7
-Xms40m
-XX:MaxPermSize=256m
-Xverify:none
-Xmx1200m
-javaagent:/path/to/sts-bundle/sts-3.7.1.RELEASE/lombok.jar 
```

我们使用 Java 8，所以将上面的行改为`-Dosgi.requiredJavaVersion=1.8`。保存文件。

通常您通过 GUI 运行 Eclipse/STS，但是这一次我们需要通过命令行运行。假设您已经在路径上安装了这些(如果没有，您应该导航到安装目录并从那里运行)，那么您可以运行:

```
# If you run Eclipse
eclipse -clean

# If you run STS
STS -clean 
```

在 Ubuntu 上，从命令行运行似乎可以运行 find，但不能从启动器运行(例如从 Unity 菜单)。如果 Unity 启动器脚本直接进入 Jar 文件，它也会覆盖一些设置。查看文件~/中的 Exec 行。local/share/applications/spring-tool-suite . desktop(或 eclipse.desktop):

```
[Desktop Entry]
Encoding=UTF-8
Version=1.0
Type=Application
Name=Spring - Spring Tool Suite
Icon=spring_tool_suite.png
Path=/path/to/Applications/sts-bundle/sts-3.7.1.RELEASE
Exec=/usr/bin/java -D...lots of defines here... -jar /path/tp/sts-bundle/sts-3.7.1.RELEASE//plugins/org.eclipse.equinox.launcher_1.3.100.v2015$
StartupNotify=false
StartupWMClass=Spring Tool Suite
OnlyShowIn=Unity;
X-UnityGenerated=true 
```

我们将 Exec 行改为:

```
Exec=/path/to/Applications/sts-bundle/sts-3.7.1.RELEASE/STS 
```

最后，当你进入 eclipse，运行 Project > Clean > All projects，然后 Maven 更新所有项目。

这种综合/腰带和背带对我们有效，但似乎也有效！

鸣谢:照片由[阿蒂拉·塔斯基兰](https://unsplash.com/photos/8Qz2rYSF_dE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄