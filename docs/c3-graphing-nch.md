# C3 制图

> 原文：<https://dev.to/ladvien/c3-graphing-nch>

## 把数据搅成信息

我代表一家机构处理大量数据，但没有很多钱。探索免费使用的开源工具是我高效工作的关键。

最近，我写了一系列关于如何使用 R 和 SQL 对无家可归者管理信息系统数据进行排序的文章。

*   [HMIS、R 和 SQL](https://ladvien.com/hmis-r-sql-introduction/)
*   [HMIS SQL-R 脚本片段](https://ladvien.com/attachment-iii-aka-the-zombie/)

这些数据对于地方政府帮助无家可归的个人快速、适当地获得住房至关重要。

但是 R 和 SQL 还没有提供的一个领域是在线交互式仪表板。数据是一方面，但易于消化的信息才是告知利益相关者该系统如何结束无家可归现象的关键。

在其他项目中，我试图将图形生成为图像并上传到静态链接。然后，每次数据改变时重新生成替换图像。但是，大多数网站服务器缓存图像，所以这并不理想。

这促使我尝试学习 D3。

*   [D3.js](https://d3js.org/)

我不想撒谎，我已经被语言、ide 和库搞糊涂了。我已经克服了大部分的挑战。但是我从来没有对 D3 的布局和语法感到如此困惑。我在 D3 工作时的阅读障碍感阻止了我在这上面花太多时间。

但是最近我决定再试一次——这次我运气好，找到了 C3.js。

*   [C3.js](http://c3js.org/)

本质上，C3 是一个大大简化了 D3 的库。它将构建一个图形归结为作为 JSON 对象传递给 C3 图形构建器的一组选项。

此代码:

```
var chart = c3.generate({
    data: {
        x: 'Date',
        y: '# Individuals',
        xFormat: '%Y-%m-%d',
        url: 'https://ladvien.com/projects/d3/data/trendsInTX601.csv',
        type: 'line',
        // colors: {
        //     Count: '#990000'
        // }
        names: {
            NumberHomeless: "Homeless",
            NumberInRRH: "Rapid Rehousing",
            NumberInPSH: "Permanent Supportive Housing"
        }
    },

    title: {
        text: "Homeless or Formerly Homeless in TX-601"
    },

    legend: {
        show: true
    },

    axis: {
        x: {
            type: 'timeseries',
            tick: {
                count: 4,
                format: '%Y-%m-%d',
                // rotate: 90,
                multiline: false,

                culling: {
                    max:5 
                }
            }
        },
        y: {
            max: 3000,
            min: 0,
            label: "# Individuals"
            // Range includes padding, set 0 if no padding needed
            // padding: {top:0, bottom:0}
        },
    },

    point: {
        r: 0
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

使用此 CSV:

*   [trendsInTX601.csv](https://ladvien.com/projects/d3/data/trendsInTX601.csv)

产生如下图形:

## 一个嗝

我在设置时遇到了一点小问题。似乎最新版本的 d3(版本 4.0)已经对它的 API 进行了大部分修改。在这种情况下，它不会与 C3 合作。但是 D3 v3 还是可以从 D3 CDN:

```
<script src="https://d3js.org/d3.v3.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

调用这个库并按照 C3 网站的说明，你可以在很短的时间内生成图形。

*   [C3 -入门](http://c3js.org/gettingstarted.html)

## 安全并按时更新数据

现在我有能力使用 R 和 SQL 对我的数据进行排序，并且我可以使用 D3 和 C3 快速生成图形，如果这其中的许多可以自动化就好了。幸运的是，我遇到了一些其他工具，这些工具使得替换我的 C3 图上的数据变得非常容易。

### [T1】Rsync](#rsync)

Rsync 主要是一个 Linux 工具，但是它也可以在 Windows 上使用。这很好，因为它允许你快速协调两个文件树(想想手动的 Dropbox)。

它还允许您通过 SSH 连接将本地文件树与服务器文件树同步。例如，我使用下面的命令将上面提到的数据同步到服务器

```
rsync -avz /Users/user/data/js-practice/d3/* ladvien@ladvien.com:/usr/share/nginx/html/projects/d3/ 
```

Enter fullscreen mode Exit fullscreen mode

运行此命令后，它会提示输入密码来访问服务器。然后，它将继续同步两个文件树。俏皮！

这允许我快速更新图表上的数据。现在，如果有一种方法可以自动插入我的密码，那么我可以写一个脚本来自动完成整个过程。

#### 蟒蛇钥匙圈

Python Keyring 是一个允许你从你的 PC 的 Keyring 中保存和检索密码的工具。

*   [蟒蛇钥匙圈](https://pypi.python.org/pypi/keyring)

它兼容:

*   Mac OS X 钥匙扣
*   Freedesktop Secret Service(需要 secretstorage)
*   KWallet(需要 dbus)
*   Windows 凭据库

如果你已经安装了 Python，你可以用 Pip:
安装密匙环工具

```
$pip install keyring 
```

Enter fullscreen mode Exit fullscreen mode

之后，您可以使用命令行工具将密码存储在密匙环中。您需要用您的服务器登录名替换`username`。

```
$keyring set system username 
```

Enter fullscreen mode Exit fullscreen mode

并使用
进行检索

```
$keyring get system username 
```

Enter fullscreen mode Exit fullscreen mode

这太棒了。这意味着我们可以将密码存储在密匙环中，并从脚本中安全地检索它。

太好了！现在，我们可以编写一个脚本，让 Rsync 与服务器同步本地的任何数据更改。对吗？嗯，差不多了。我们还需要一件工具。

### SSHPass

使用 Rsync 从脚本远程同步文件有一个问题。当从脚本中调用 Rsync 时，它不会等待参数传递给工具。叹气。

幸运的是，我不是唯一有这个问题的人，一个工具被创造出来解决这个问题。

*   [SSHPass](https://linux.die.net/man/1/sshpass)

如果你在 Mac 上，你需要使用 Brew 来安装 SSHPass。

```
brew install https://raw.githubusercontent.com/kadwanev/bigboybrew/master/Library/Formula/sshpass.rb 
```

Enter fullscreen mode Exit fullscreen mode

我们走吧！现在我们可以自动化整个过程。

我写了这个脚本来做脏活:

```
#!/bin/sh
PASSWORD=("$(keyring get system ladvien.com)")
ECHO ""
ECHO "****"
ECHO "* Updating D3 Projects     *"
ECHO "****"
ECHO ""
sshpass -p "$PASSWORD" rsync -avz /Users/user/data/js-practice/d3/* root@ladvien.com:/usr/share/nginx/html/projects/d3/ 
```

Enter fullscreen mode Exit fullscreen mode

### 克朗

好吧！整个过程中最后一点糖。让我们创建一个 Cron 作业。这将按照我们选择的时间间隔在后台运行脚本。

对我来说，我有一个每周一提取数据并运行主脚本的员工。因此，当我知道有新数据可用时，我将设置我的自动化脚本在星期二更新我的 C3 图数据。

*   [如何在 Mac 上创建 Cron 作业](https://ole.michelsen.dk/blog/schedule-jobs-with-crontab-on-mac-osx.html)

您可以使用 Nano 来编辑您的 Cron 作业列表。

```
env EDITOR=nano crontab -e 
```

Enter fullscreen mode Exit fullscreen mode

为了在星期二运行 Cron 作业，我们将第五个星号设置为 2。

```
* * * * 2 /the/path/to/our/update_script.sh 
```

Enter fullscreen mode Exit fullscreen mode

并且不要忘记使`update_script.sh`可执行。

```
chmod +x update_script.sh 
```

Enter fullscreen mode Exit fullscreen mode

我是一个用钢锯黑客！