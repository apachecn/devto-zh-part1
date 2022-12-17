# 我如何组织我的文件

> 原文：<https://dev.to/roger/how-i-organize-my-files-81i>

> 我在 git 中备份了我所有的文件，基本上概括了我的生活。现在，我使用 bitbucket 来存储 git。
> 计划将来迁移到 GitHub。

我不只是组织我的代码，我组织我所有的文件。包括视频，图片和咆哮。将来还会有更多。

所有这些备份将被克隆到我所有的开发机器上。我一般都把它们命名为 root，所以' ~/root '。

下面是根的树形视图:

```
dev/
files/
images/
rants/
videos/ 
```

Enter fullscreen mode Exit fullscreen mode

#### 这里，我来解释一下。

*   `dev/`是为了我的作品。开发者的东西。
*   `files/`是为了文件。主要是 zip 文件的下载。
*   `images/`为图片。基本上我的照片，备份。
*   是为了我的咆哮。我通常在公共汽车上感到无聊。所以我打开我的 MacBook，在等车的时候开始咆哮。
*   `videos/`是我的视频。我没事干的时候，一般都会随便唱点屎，我录下来。尽管文件夹上写着`videos`其中一些不是视频。

`files/`、`images/`、`rants/`、&、`videos/`现在真的没有很多文件，因为我真的专注于`dev/`目录。

#### 在 dev/文件夹内

树形视图:

```
cdn/
fun/
packages/
works/ 
```

Enter fullscreen mode Exit fullscreen mode

*   `cdn/`是我的 CDN 的目录。主要是框架或客户文件。
*   是我放与作品无关的文件的地方。大部分是开源的贡献和东西。
*   是我放安装工具的地方。大概是一些框架配置和一些点文件。
*   是我放工作用品的地方。客户端源代码。是的，那些东西。

#### 趣味/文件夹内

树形视图:

```
experiments/
mine/
open-source/
shits/ 
```

Enter fullscreen mode Exit fullscreen mode

*   是我做实验的地方。
*   是我放个人密码的地方。大多是网站和算法。
*   是我做开源东西的地方。你知道，那些东西。
*   所有这些遗留下来的代码都在这里安息。

> 就是这样。让我知道你的想法。

## 更新

> 我最终将 git repo 分割在 dev/ folder 和其他所有文件中。

*   `dev/`现在叫做工作区。
*   其他所有东西现在都叫做`BEE`(基本上是其他所有东西)

哦，我还把`shits`从乐趣转移到了根本。(`mv fun/shits . && mv shits cemetery`)

> 现在叫`cemetery`。