# 每日 git 使用情况

> 原文：<https://dev.to/x0st/daily-git-usage-e0d>

嗨伙计们！
我们每天都在使用 Git 输入命令，比如`git push origin master`很多次。但是也许你有化名缩写为`git p`。渐渐的，就变得比较气人了。例如，我每天使用`git push origin {branch}`命令大约 20 次。`git commit -m "{message}"`也许 15。

我不喜欢 IDE 中内置的 Git 工具，并且根本不使用它们。

所以我决定让我的一些工作自动化。我写了一个脚本，它每天都在帮助我，我希望它也能帮助你。

叫做[便装-git](https://github.com/x0st/casual-git) 。

看起来如何

```
myproject master ➜ gh

  d  - push
  f  - push --force
  p  - pull
  o  - pull --force
  c  - commit
  a  - commit --amend
  s  - commit --smart
  l  - log --pretty
  h  - checkout --smart 
```

Enter fullscreen mode Exit fullscreen mode

我每天使用的几乎所有命令。

真的很好用！

如果您用像`feature/EGNYTE-20-watermarks-over-documents` :
这样的长名字来命名您的分支，这将特别有帮助

```
 d  - push
  f  - push --force
  p  - pull
  o  - pull --force
  c  - commit
  a  - commit --amend
  s  - commit --smart
  l  - log --pretty
  h  - checkout --smart

  Enter a branch name or a part of name: ma

  More than one git branch were found. 
  10 first branches are shown. 
  Please choose a desired branch. 

  [0] feature/EGNYTE-20-watermarks-over-documents
  [1] master
  [2] masterfix/EGNYTE-21-big-files-merge
  [3] masterfix/EGNYTE-26
  [4] masterfix/EGNYTE-29-big-files-merge
  [5] masterfix/EGNYTE-33
  [6] masterfix/EGNYTE-41

Switched to branch 'feature/EGNYTE-20-watermarks-over-documents' 
```

Enter fullscreen mode Exit fullscreen mode

或者文件路径太长，您只需要提交其中的几个:

```
myproject feature/EGNYTE-68-progress-bar-for-watermarking ➜ gh

  d  - push
  f  - push --force
  p  - pull
  o  - pull --force
  c  - commit
  a  - commit --amend
  s  - commit --smart
  l  - log --pretty
  h  - checkout --smart

  Delete files from the next commit:
  No staged files

  Add files to the next commit:
  [1] modified:  app/Http/Controllers/WatermarkController.php
  [2] modified:  app/Jobs/Egnyte/PublishWatermark/Job.php
  [3] modified:  app/Services/QueuedMessage/Queue.php
  [4] modified:  public/css/watermark.css
  [5] modified:  public/js/watermark.js
  [6] modified:  public/mix-manifest.json
  [7] modified:  resources/assets/js/watermark.js
  [8] modified:  resources/assets/scss/watermark.scss
  [9] modified:  resources/lang/en/watermark.php
  [10] modified:  resources/views/watermark/index.blade.php
  [11] modified:  webpack.mix.js

  Enter file numbers separating by spaces: 1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

[Github 回购](https://github.com/x0st/casual-git)

希望这能帮助到某个人！