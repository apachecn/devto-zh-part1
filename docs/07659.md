# 只需一步就可以初始化、填充 Git Repo 并将其发布到 GitHub

> 原文：<https://dev.to/cmilr/initialize-populate-and-post-your-git-repo-to-github-in-a-single-step>

我不知道你怎么样，但是我有点沉迷于节省时间...事实上，我很确定我在节省时间上比在编写代码上投入了更多的精力(哦，伙计，那是改天再讨论的话题。)

无论如何，我喜欢把我的代码放在 GitHub 上，但是我很恼火我不得不脱离 IDE 或者终端来做这件事——浏览浏览器感觉太慢了。

所以我把这个简单的功能放在一起，让在 GitHub 上启动一个项目变得快速简单，我想我应该分享一下。一旦你设置好了， ***这简直就是一步到位的交易。*T3】**

除了 git、GitHub 帐户和 bash 终端，这里唯一真正的依赖是 [**hub**](https://github.com/github/hub) ，这是一个优秀的 git 命令行工具。

我在 Mac 上工作，所以我通过[家酿](https://brew.sh/)——*$ brew 安装 hub*——但你可以仔细阅读他们的 GitHub 页面，了解[更多的安装选项](https://github.com/github/hub)。

一旦你安装了 hub 并开始工作，你应该能够将类似下面的代码放到你的**中。bash_profile** (确保将路径替换为与您自己的开发环境相关的路径)，并使用它在一个步骤中构建一个新的 repo。

下面的函数自动在我的本地 **Repos/** 目录下创建一个文件夹，在其中初始化一个存储库，用默认的**自述文件**、**许可证**和**填充 repo。gitignore** 文件，然后创建一个远程 GitHub repo，跟踪它，并进行第一次提交。它通过打开我的浏览器到 GitHub repo 来结束，这样我就可以验证一切，如果我喜欢的话还可以添加描述... ***全部来自于点击*** 。:)

```
function mkpub {
    printf "What would you like to name your repository? "
    read name
    #Replace with a local path to your repos
    cd '/CMiller/Dev/Repos/'
    mkdir $name
    cd $name
    git init
    #Replace with local paths to any default files you'd like to include
    #Note: the -f flag forces overwrite; use -i for interactivity if you prefer
    cp -f '/CMiller/Dropbox/Dev/GitInit/Public/README.md' .
    cp -f '/CMiller/Dropbox/Dev/GitInit/Public/LICENSE' .
    cp -f '/CMiller/Dropbox/Dev/GitInit/.gitignore' .
    hub create
    git add .
    git commit -m "First commit: add README, LICENSE, and .gitignore."
    git push -u origin master
    hub browse
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，要创建一个用于制作私有回购而不是公共回购的函数，只需在上面的“hub create”行添加一个'-p '标志即可— *hub create -p* 。

我希望你觉得这是有用的。

### 感谢光临！