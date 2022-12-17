# 再次设置我的开发环境:我在依赖地狱中的一天

> 原文：<https://dev.to/jess/getting-my-dev-environment-set-up-again-my-day-in-dependency-hell>

在我开始大会的沉浸式网络开发的前一天，我们组有一个强制性的“installfest”披萨聚会。目光明亮的学生聚集在一起，第一次打开了他们的终端。

我们被告知输入这个:

```
bash <(curl -sL https://raw.githubusercontent.com/GA-WDI/installfest/master/builds/mac) 
```

Enter fullscreen mode Exit fullscreen mode

...就这样。我们不知道发生了什么，但我知道这是不可思议的，因为进度条被填满了，难以辨认的文字从窗口流过。如果我没有坐在那个教室里，我会以为我的电脑被黑了，或者类似的恐怖事情。我问我们的一位助教，我们毕业后是否能“做这个”,后来我发现，在教师休息室里，我被认为是那个想写 bash 脚本的女孩。

一年后的今天，我坐在一台新机器前，不知道如何设置我的开发环境。Git 和 Ruby 一直都在我身边。我也有几个星期没有编码了(对于实际开发来说，有惊人的大量非编码工作要做)，所以我很兴奋，渴望组装一台服务器。

## 我设置了什么

而不是分叉回购(有这么多好的——像[这个](https://github.com/thoughtbot/laptop)！)，我将进行(大部分)手动安装，并跟踪我下次在这里需要的所有东西。

*   铬
*   iTerm2
*   XCode +转到
*   atom(第一次尝试！再见崇高。)
*   atom shell 命令
*   mkdir ~/Code
*   克隆开发到回购
*   为 github 生成新的 ssh 密钥
*   为 dev.to 目录创建别名
*   根据我的喜好更新终端提示(包括表情符号)
*   家酿易于安装所有其他东西
*   红宝石
*   rbenv
*   一种数据库系统

## ...痛苦开始了

安装完上面的，我打了`bundle install`，手指交叉。

```
An error occurred while installing json (1.8.3), and Bundler cannot continue.
Make sure that gem install json -v 1.8.3 succeeds before bundling. 
```

Enter fullscreen mode Exit fullscreen mode

当然，`gem install json -v '1.8.3'`也打错了。这导致了安装`libv8`和尝试一大堆其他东西。比如把`if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi`加到我的 bash 个人资料里。

我在打这个的时候还是卡住了——我真的希望我能更好地记录我是如何摆弄的。我*认为*错误与我安装`rbenv`的方式有关，因为目录/路径与我的 gem 文件不一致。我应该运行应用程序的`ruby 2.3.0`，但是我的宝石被安装在这里:`/usr/local/lib/ruby/gems/2.4.0/`

在注意到这一点之前，我在键入`rbenv global`命令时也得到 Ruby 2.3.0，但是当我键入`ruby -v`命令时却看到 Ruby 2.4.0。我已经解决了这个问题，但是上面的问题仍然存在。

不管怎样，已经几个小时了，我需要咖啡因来提神。

â˜•ï¸â˜•ï¸â˜•ï¸â˜•ï¸â˜•ï¸â˜•ï¸

## 固定！

..我回来了。是的，这是一个`rbenv`问题。我最终卸载了`rbenv`并删除了所有的`.rbenv`文件。为了安全起见我还做了一个`brew uninstall ruby`。这一次，我确保在 ruby 之前执行`brew install rbenv`，并且实际上遵循了`rbenv`指令。在这一轮中，我意识到我最初并没有运行`rbenv init`命令。

工作几乎完美无缺——只需要下载 rubyracer 宝石并添加`-- --with-system-v8`来安装`libv8`宝石。

现在给我分配一张票。