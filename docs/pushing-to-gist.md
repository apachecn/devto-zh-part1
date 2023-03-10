# 推动要点

> 原文：<https://dev.to/onmyway133/pushing-to-gist>

### 创造新的要旨

*   去[https://gist.github.com/](https://gist.github.com/)创建一个新的要点
*   注意，您需要包含`filename` + `extension`来启用自动语言标记
*   点击`Add file`添加更多文件

### 克隆要旨

*   如果您已经启用了`2 factor authentication`，您需要将`personal acccess token`与`https`一起使用，或者使用`ssh`。

> 如果您启用了双因素身份验证，或者如果您正在访问使用 SAML 单点登录的组织，则必须提供个人访问令牌，而不是输入 HTTPS Git 的密码。
> 
> *   你应该启用`2 factor authentication`。了解更多[https://help . github . com/articles/https-cloning-errors/# provide-access-token-if-2fa-enabled](https://help.github.com/articles/https-cloning-errors/#provide-access-token-if-2fa-enabled)
> *   比如用我的 gist[https://gist . github . com/on myway 133/c 486939 f 82 fc 4d 3 a 8 ed 4 be 21538 FDD 32](https://gist.github.com/onmyway133/c486939f82fc4d3a8ed4be21538fdd32)`ssh url`就是`git@gist.github.com:c486939f82fc4d3a8ed4be21538fdd32.git`
> *   正常运行 git 命令

```
git remote add origin git@gist.github.com:c486939f82fc4d3a8ed4be21538fdd32.git
git clone 
```

Enter fullscreen mode Exit fullscreen mode

*   你默认有分支`master`

```
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

### 忽略目录

> remote: Gist 不支持目录。

*   在我的例子中，我使用的是 node，所以我需要忽略`node_modules`目录
*   如有必要，还需要进行跟踪

```
git rm --cached -r .
git add . 
```

Enter fullscreen mode Exit fullscreen mode

* * *

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://www.producthunt.com/posts/push-hero-2)
*   [快速访问-在 Mac 菜单栏中组织文件](https://www.producthunt.com/posts/quick-access)
*   [帧记录器-带帧记录器 gif 和视频](https://www.producthunt.com/posts/frame-recorder)
*   [其他应用](https://onmyway133.github.io/projects/)

❤️❤️😇😍🤘❤️❤️