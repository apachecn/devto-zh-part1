# 使用运行计划的控制台应用程序。Net Core、Cron 和 Docker

> 原文：<https://dev.to/alexhyettdev/running-scheduled-console-apps-with-net-core-cron-and-docker-441e>

我是……的超级粉丝。目前网核。主要是因为我可以在我的 Mac 上进行本地开发，而且我可以在 Docker 容器中运行它们(我提到过我喜欢 Docker 吗？).

与。Net Core 你可以构建任何你目前可以用 vanilla 构建的应用程序。Net，比如控制台应用，Web APIs，MVC 应用。如你所知，控制台应用程序通常有两种风格，一直运行和按时间表运行。通常，预定的控制台应用程序只是挂在 Windows 任务调度器上，然后就被遗忘了(直到它们崩溃)。

鉴于。Net Core 应该是跨平台的，Windows 任务调度器的跨平台等价物是什么？我看了看构建定时器循环或使用 FluentScheduler 之类的框架，但这感觉就像给本来很小的控制台应用程序增加了体积。

然后有朋友提醒我 Docker 包含的都是运行 Linux 的。当然，我们可以简单地使用 Cron！

## 万福克朗

对于外行来说，Cron 是 Windows 任务调度程序的 Linux 版本。它没有 UI，但是由/etc/cron.d/中的文件控制。

正如我发现的那样，在 Docker 中使用. Net 核心控制台应用程序成功运行 cron 会带来一些问题！

## 问题 1:没有环境变量

在我的第一次尝试中，我创建了一个调度文件，该文件简单地使用 cron 在一个调度上运行我的 dotnet 核心控制台应用程序。

我的文件是这样的:

```
* * * * * root dotnet /app/dotnet-cron.dll >> /var/log/cron.log 2>&1

#* * * * * *
#| | | | | |
#| | | | | +-- Year (range: 1900-3000)
#| | | | +---- Day of the Week (range: 1-7, 1 standing for Monday)
#| | | +------ Month of the Year (range: 1-12)
#| | +-------- Day of the Month (range: 1-31)
#| +---------- Hour (range: 0-23)
#+------------ Minute (range: 0-59)
# Cron requires a blank space at the end of the file so leave this here. 
```

Enter fullscreen mode Exit fullscreen mode

这就引出了第一个问题。Cron 在自己的环境中运行，去掉了所有的环境变量。

通常，Docker 容器中的任何环境变化都是使用环境变量来控制的。使用 Microsoft 扩展，您可以使用 AddEnvironmentVariables 来重写 appsettings.json 文件中的设置。

您可能习惯于看到 docker 合成文件中指定的环境变量，如下所示:

```
Database:ConnectionString=Server=192.168.1.45;uid=db_user;pwd=THG763hdN; 
```

Enter fullscreen mode Exit fullscreen mode

当我们处理编译过的映像时，唯一的解决方法是在运行时导出您的环境变量，并在您的应用程序运行之前让 cron 设置它们。

我的方法是使用 printenv 并把它们都写到一个文件中，用 export 命令把它们放在前面。这让我们看到了第二个问题。

## 问题 2:无效的环境变量

让我们看看当我们导出一个环境变量时会发生什么:

```
bash: export: `Database:ConnectionString=Server=192.168.1.45': not a valid identifier 
```

Enter fullscreen mode Exit fullscreen mode

通过我只能假设是黑魔法，Docker 设法设置环境变量，即使它们有无效字符。在这种情况下，是冒号(:)导致了问题。幸运的是。Net Core 接受一个双下划线，这与 Linux 导出函数一起工作。

在调查这件事的时候，我确实发现了一些有趣的事情。如果使用双下划线，当 CMD 行在 docker 文件末尾运行时，环境变量可用。但是，如果您使用冒号，这些冒号实际上是在您的容器建立之后设置的。

Docker Compose 还通过设置 affinity:container 环境变量(它不仅包含一个冒号，还包含一个双等号)将另一个曲线球扔进了混合物中！

最后，我使用下面的脚本将所有环境变量输出到一个单独的脚本中。我不得不使用 sed 将值用引号括起来，否则这又会引起另一个令人头痛的问题。

```
#!/bin/bash

echo '#!/bin/bash' > /app/set_env.sh
printenv | sed '/^affinity:container/ d' | sed -r 's/^([a-zA-Z_]+[a-zA-Z0-9_-]*)=(.*)$/export \1="\2"/g' >> /app/set_env.sh
chmod +x /app/set_env.sh 
```

Enter fullscreen mode Exit fullscreen mode

然后我的 Dockerfile 在运行 cron 之前运行这个脚本:然后我只告诉运行下面的脚本，它设置环境变量并运行我的控制台应用程序。

```
#!/bin/bash

# Set environment variables copied from container
source /app/set_env.sh;

# Run your dotnet console app
dotnet /app/dotnet-cron.dll 
```

Enter fullscreen mode Exit fullscreen mode

## 问题 3:跨平台考虑

最后一个问题只发生在我让其他人在他们的机器上构建并运行我的 Docker 映像的时候。它在 Mac 用户中运行得很好，但是对于 Windows 用户来说，生成的图像永远不会按时运行。

我以前见过这个问题。那些有 Linux 和 Windows 操作经验的人会知道我在说什么，回车。Windows 行尾不同于 Linux，因为它们不仅包含新行(\n)，还包含回车(\r)。

所以在 Dockerfile 文件中，我使用 sed 从所有基于 Linux 的文件中去掉了回车。该命令如下所示:

```
sed -i 's/\r//' export_env.sh 
```

Enter fullscreen mode Exit fullscreen mode

## 最终结果

最终的结果可以在我的 GitHub 页面上看到:[https://github.com/alexhyett/dotnetcron](https://github.com/alexhyett/dotnetcron)如果这有用或者你知道更好的方法，请在下面评论。