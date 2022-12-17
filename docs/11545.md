# 如何忽略现有文件

> 原文：<https://dev.to/adamkdean/how-to-gitignore-an-existing-file-4f7j>

您现在想要忽略一个文件，比如一个编译过的 css 文件，但是 git 已经在跟踪它了。

首先，将路径添加到您的`.gitignore`文件:

```
src/path/to/file/style.css 
```

Enter fullscreen mode Exit fullscreen mode

然后，在 git bash 中，我们想要清除缓存，所以运行以下命令:

```
git rm -r --cached .
git add .
git commit -m ".gitignore is now working" 
```

Enter fullscreen mode Exit fullscreen mode

现在推动你的改变，摆脱这些烦人的文件！

更多信息:[http://stack overflow . com/questions/11451535/git ignore-not-work](http://stackoverflow.com/questions/11451535/gitignore-not-working)