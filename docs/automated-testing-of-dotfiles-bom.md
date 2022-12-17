# 点文件的自动化测试

> 原文：<https://dev.to/michaelmior/automated-testing-of-dotfiles-bom>

几年前，我开始根据扎克·霍尔曼的[网络文件回购](https://github.com/holman/dotfiles)来管理我的网络文件。他的设置非常好，我发现适应自己的目的相对容易。我的工作流程通常包括做一些我满意的局部修改，然后推进到我自己的 [GitHub fork](https://github.com/michaelmior/dotfiles) 。

我最终发现的大问题是，我没有完全捕捉到重现我的环境的正确步骤。每当我试图在一台新机器上安装我的点文件时，我都会遇到几个错误，最终我会解决这些错误。该修复并不总是导致在另一台机器上可再现的东西。我想要一个解决方案，让我能够自动测试我的点文件是否会在每次推送到 GitHub 时干净地安装，所以我求助于 [Docker](https://www.docker.com/) 。

传统的 CI 服务在使用所有需要安装的软件包时会有点麻烦。Docker Hub 让事情变得又好又简单。我的 [Dockerfile](https://github.com/michaelmior/dotfiles/blob/a9eae90d466958948a53b3b583d69eba844ed8f7/Dockerfile) 只是安装必要的操作系统包，添加一个新用户，然后尝试运行我的安装脚本。我目前没有任何其他测试，除了确保脚本没有错误地退出，但这已经帮我省了很多麻烦。

**更新**:从那以后，我开始使用 [Travis CI](https://travis-ci.org/michaelmior/dotfiles) ，就像我在其他项目中做的那样。事实证明这比我想象的要容易。我仍然没有明确地添加任何测试，但即使能够确认安装步骤成功，也有助于确保没有任何问题。