# 在不离开命令行的情况下获取 GitHub URLs

> 原文：<https://dev.to/cmilr/grab-your-github-urls-without-leaving-the-command-line>

为了与我之前的帖子保持一致，即[在一个步骤](https://dev.to/cmilr/initialize-populate-and-post-your-git-repo-to-github-in-a-single-step)中初始化、填充和发布一个 repo 到 GitHub，我想分享一个快速的单行 bash 别名，我用它在命令行中列出我的 GitHub urls。

```
alias ghrepos="curl -s -i -H 'Authorization: token $GHUB_TOKEN' \
    https://api.github.com/user/repos | grep -o 'https://github.com[^\"]*.git'" 
```

Enter fullscreen mode Exit fullscreen mode

要使用上述别名，您只需将$GHUB_TOKEN 替换为您自己的来自 GitHub 的 ***个人访问令牌*** 。

将此功能放在手边有很多好处，您可以使用它快速:

*   导航到回购的 url。
*   将一个存储库克隆到您的本地计算机。
*   或者只是在一个简单的列表中仔细阅读您的远程 repos，就像这样:

```
$ ghrepos

https://github.com/cmilr/Atom-Prefs.git
https://github.com/cmilr/Bash-Prefs.git
https://github.com/cmilr/Blender-Playground-v1.1.git
https://github.com/cmilr/Blender-Prefs.git
https://github.com/cmilr/CMillerCo-HTML.git
https://github.com/cmilr/CMillerCo-Rails.git
https://github.com/cmilr/DeadSimple-Blender-3D-Cheat-Sheet.git
https://github.com/cmilr/DeadSimple-Pixel-Perfect-Camera.git
https://github.com/cmilr/Git-Beautify-For-MacOS-Terminal.git
https://github.com/cmilr/HTML-Essential-Training.git
https://github.com/cmilr/iOS-Postprandial-Timer.git
https://github.com/cmilr/iOS-Programming-Big-Nerd-Ranch.git
https://github.com/cmilr/iOS-SwiftCalc.git
https://github.com/cmilr/Learning-iOS-App-Dev-1.git
https://github.com/cmilr/Learning-iOS-App-Dev-2.git
https://github.com/cmilr/Learning-iOS-App-Dev-3.git
https://github.com/cmilr/Unity2D-Components.git
https://github.com/cmilr/Unity3D_ImporterDefaults.git
https://github.com/cmilr/Visual-Studio-For-Mac-Dark-Syntax-HoneyBees.git
https://github.com/cmilr/VS-Prefs.git
https://github.com/cmilr/Xcode-Prefs.git 
```

Enter fullscreen mode Exit fullscreen mode

我希望你和我一样觉得这很有用。

## 感谢光临！