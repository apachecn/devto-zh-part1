# 如何在 GitHub 页面上托管您的 Hugo 网站

> 原文：<https://dev.to/toyotarone/how-to-host-your-hugo-website-on-github-pages-4aa2>

# 雨果是什么？

[官方网站](https://gohugo.io/)

# 安装 Hugo

首先，安装 Hugo。
[安装雨果(官方)](https://gohugo.io/getting-started/installing/)

# 设置

遵循以下程序:
[快速启动](http://gohugo.io/getting-started/quick-start/)
可以跳过 step1。

关于第三步，一些网站可能会用`git clone`而不是`git submodule`来解释。
你可以选择任何一个，但是我推荐你使用`git submodule`，因为使用它可以让你追踪主题的最新版本。
如果您选择了`git clone`，您应该将主题文件夹添加到。gitignore 如下:

```
echo 'themes/' >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

# 添加内容

如果你选择了`gohugo-theme-ananke`作为你的主题，你应该会看到下面:
[入门(gohugo-theme-ananke)](https://themes.gohugo.io/gohugo-theme-ananke/#getting-started)
当然你可以用
添加你的第一个内容

```
hugo new posts/foobar.md 
```

Enter fullscreen mode Exit fullscreen mode

但是我推荐你把`gohugo-theme-ananke`里的`exampleSites`的内容复制过来，修补一下。

# Config your config.toml

如果你想把网站上传到你的 GitHub 页面，你应该修改`config.toml`。

```
baseURL = "https://<YOUR-USER-NAME>.github.io/" 
```

Enter fullscreen mode Exit fullscreen mode

部分`<YOUR-USER-NAME>`应该是你 github 的用户名。

如果你正在跟踪[快速启动](http://gohugo.io/getting-started/quick-start/)，你应该像官方程序一样添加下面一行:

```
theme = "ananke" 
```

Enter fullscreen mode Exit fullscreen mode

# 本地启动 Hugo

在下面执行

```
hugo server 
```

Enter fullscreen mode Exit fullscreen mode

雨果马上就要开始了。
让我们用您的网络浏览器访问`localhost:1313`。
你可以看到网站的美感。

# 建立你的网站

当你完成你的网站，请执行

```
hugo 
```

Enter fullscreen mode Exit fullscreen mode

Hugo 在`public`文件夹下建立你的网站。

# 上传您的网站

在你上传之前，你应该将`public`添加到你的`.gitignore`

```
echo 'public/' >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

并移动到公共文件夹

```
cd public 
```

Enter fullscreen mode Exit fullscreen mode

之后，`git push``public`文件夹的全部内容。

```
git init
git remote add origin https://github.com/<YOUR-USER-NAME>/<YOUR-USER-NAME>.github.io 
```

Enter fullscreen mode Exit fullscreen mode

然后，访问您的网站`https://<YOUR-USER-NAME>.github.io`