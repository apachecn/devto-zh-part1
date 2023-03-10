# 爵士、杰基尔和斯威夫特 2.1

> 原文：<https://dev.to/ladvien/jazzy-jekyll-and-swift-21-437a>

我想花点时间写下我计划如何记录未来的 Swift 项目。

### 步骤一:安装

我发现[爵士](https://github.com/Realm/jazzy)。这是一个命令行工具，用于记录 Xcode 项目。有趣的是，它是少数几个与 Swift 2+兼容的软件之一。无论如何，起床出门是一点也不痛苦的。

我发现的一个问题是 Swift 经常改变版本，所以你可能要等一会儿，让开发者跟上。然而，当更新发布时，很容易让事情恢复正常，只需在终端中运行`sudo gem update`。

### 第二步:跑步

要运行 Jazzy，你只需在 Mac 上打开一个终端窗口，导航到 Xcode 项目的顶层文件夹，然后运行`jazzy`。您应该会得到类似下面这样的结果，

```
 Running xcodebuild
Parsing ViewController.swift (1/3)
Parsing bleTableViewController.swift (2/3)
Parsing AppDelegate.swift (3/3)
building site
jam out ♪♫ to your fresh new docs in `docs` 
```

Enter fullscreen mode Exit fullscreen mode

这里有一点非常重要。默认情况下，Jazzy *只记录公共方法。因此，你*必须*把旗子
递给杰西*

```
 jazzy --min-acl internal private 
```

Enter fullscreen mode Exit fullscreen mode

这是你得到一切的唯一途径。否则，您很可能会得到一个空项目。不得不艰难地发现这一点。[参考解决方案](https://github.com/realm/jazzy/issues/255)。

如果 jazzy 正确解析了所有内容，那么您的项目文件夹中将会有一个`docs`文件夹。此 docs 文件夹是一个伪网站版本。

### 第三步:推送页面

要获得在线文档，请将`docs`文件夹复制到 Jekyll 站点地图中的适当目录下。提交和推送。现在，您的项目的文档对于被称为人类知识的全局单例是显而易见的。要获得文档索引的链接，请添加以下内容，

`[Test](director_you_chose_to_put_docs_folder/docs/index.html)`

这里有一个例子:[行为蓝牙文档](///jazzy/behavioralBluetooth/index.html)

### 第四步:记录你的项目

我不会重复爵士乐的指令。简而言之，你可以在代码中的注释中加入 Markdown，解析器会提取注释，并将它们与它们接近的方法等相关联。

[爵士指令](http://ericasadun.com/2015/06/14/swift-header-documentation-in-xcode-7/)

### 补遗

显然，从文档过程中排除文件有点棘手。

```
 jazzy  --exclude /Users/Ladvien/Desktop/behavioralBluetooth/behavioralBluetooth/AppDelegate.swift,/Users/Ladvien/Desktop/behavioralBluetooth/deviceState.swift 
```

Enter fullscreen mode Exit fullscreen mode

它应该看起来像上面的，"- exclude "(不是双破折号)，后跟要排除的文件的绝对路径。然后是逗号(“，”)，没有空格，然后是您希望排除的第二个文件的绝对路径。通过执行以下操作，很容易将它变成一个脚本。

```
 1\. From the terminal type: nano my_jazzy_script
2\. Paste the finished command, such as the one listed above.
3\. "Write-Out" the changes.
4\. Exit nano.
5\. At the terminal type: chmod +x my_jazzy_script (this makes the file executable).
6\. To run the script type: ./my_jazzy_script 
```

Enter fullscreen mode Exit fullscreen mode