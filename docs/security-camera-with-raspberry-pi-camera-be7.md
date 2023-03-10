# 带 Raspberry Pi 摄像头的安全摄像头

> 原文：<https://dev.to/funkysi1701/security-camera-with-raspberry-pi-camera-be7>

所以我的第一个 Raspberry Pi 项目是一个安全摄像头。如果你读过我以前的 Raspberry Pi 帖子，你会知道我一直在玩相机，但我在 Pidora 上有一两个问题，所以我在这个例子中使用 Raspbian。

要使用相机模块拍照，您可以使用命令 raspistill，它有许多选项，您可以通过运行不带参数的命令来查看完整列表。我将列出几个我用过的选项。

-o filename:这将图像输出到一个文件名为 filename 的文件中-VF:vertical flip-HF:horizontal flip 这些在相机颠倒时很有用 etc-q number:jpg 图像的质量在 0 到 100 之间，默认图像大小约为 3 Mb，我将其减少到 quality = 10，这使我的图像大小为 300Kb，这更合理-a text:用一些文本注释您的图像，我发现文本需要以字符开头，数字仅对我无效。

我想用脚本来创建图像，所以我决定用时间和日期来命名我的脚本创建的文件。

这对于 bash 脚本来说相当容易。

```
DATE=$(date +”%Y-%m-%d_%H%M”)
raspistill -q 10 -vf -hf -a a$DATE -o /var/www/$DATE.jpg 
```

Enter fullscreen mode Exit fullscreen mode

第一个$DATE 是我添加到图像本身的日期，因为我在注释数字时遇到了问题，所以我在它的开头添加了一个额外的 a。

现在很容易让 CRON 每隔几分钟运行一次这个脚本，我决定每隔 2 分钟运行一次。

我的下一个脚本整理，所以你不会得到图像泛滥成灾。

```
cd /var/www
ls *.jpg > stills.txt
mencoder -nosound -ovc lavc -lavcopts vcodec=mpeg4:aspect=16/9:vbitrate=8000000 -vf scale=1920:1080 -o timelapse.avi -mf type=jpeg:fps=24 mf://@stills.txt
rm /var/www/*.jpg 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本的第三行做了一件非常聪明的事情，它从你拍摄的所有图像中创建了一个视频。为了让它工作，你需要安装 mencoder(sudo apt-get install mencoder)

这一切都很好，但如果这些图片被上传到其他地方，岂不是很酷？如果有人不怀好意，他们可能会破坏你的安全摄像头。我的第一个想法是使用类似 DropBox 的东西，但不幸的是，它不被 raspberry pi 上的处理器支持。然而，我发现 copy.com 非常适合同步文件。

要安装，只需运行 wget[http://copy.com/install/linux/Copy.tgz](http://copy.com/install/linux/Copy.tgz)并解压文件，将客户端安装到您的 raspberry pi 上。

```
./CopyConsole -daemon -u=gary@example.com -p=’mypass’ -root=/home/pi/Copy 
```

Enter fullscreen mode Exit fullscreen mode

此命令将您在根目录下设置的文件夹与 copy.com 同步。

我做的最后一件事是创建一个在网络服务器上运行的 php 文件，它允许你滚动浏览不同的图片。我尝试使用 javascript 自动滚动图像，但我发现手动方式更有用。我写的所有文件都添加到了我的 github 页面。