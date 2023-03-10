# 使用 git 子树部署到 gh 页面

> 原文：<https://dev.to/letsbsocial1/deploying-to-gh-pages-with-git-subtree>

[![Git Version Control](img/9e81b0c45f638ef23e541f80c8f2c421.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MQ_bhTVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yw4gcbphx45mw061np2c.png)

所以我前几天刚刚用 React 完成了一个 todo 应用。这是我第二次在 [egghead.io](https://egghead.io/) 上学习“构建您的第一个生产质量 React 应用程序”课程。第一次完全按照课程，用“创建-反应-app”。然而，我在控制台中不断遇到错误。可能有些是由于我的错误，但有些肯定与 *create-react-app* 有关。问题是，如果不运行 eject 命令来查看到底是什么，我就无法找出问题所在。如果我那么做了，就没有回头路了！嗯，我就是不喜欢这样。我决定学习更多关于`Webpack 2+`的知识，这样我就可以开始使用它提供的新特性来创建我自己的`React`工作流，从而只添加我需要的特性。我喜欢为我的工作流程创建自己的自动化流程。我努力使用`Gulp`、`templating engines`和`Webpack`为常规的`ES6+ JavaScript`应用程序创建无缝流程。叫我书呆子，但是我发现它非常有趣和非常有用！我还没有深入到`Hugo`的更复杂的工作流程中，但那最终也会发生。

在`Webpack 2+`到来的这段旅程中，我在 Udemy 上遇到了一个很棒的课程，叫做“Webpack 2:完整的开发者指南”,作者是 Stephen Grider。它没有涵盖所有内容，但是它无疑为了解`Webpack 2`的新特性提供了一个坚实的基础。这些变化当然是为了回应在`JavaScript`和`ES6+`发生的大检修。起初我有点怀疑，因为我认为没有什么可以取代`Gulp`工作流的模块化。但是，在更好地了解`Webpack 2`和`React`之后，我开始意识到`Webpack 2+`工作流比`Gulp`工作流更适合`React`。斯蒂芬·格里德(Stephen Grider)等开发人员曾经将`Gulp`与`React`一起使用，在版本 2 推出时切换到专门使用`Webpack`。这说明了一些问题！

所以当我更好地了解新的`Webpack`和`React`时，我也被介绍了新的`Git`命令。有人向我介绍了一种不同的、更有效的部署 Github 的`gh-pages`的方式，这太棒了！

几年前当我第一次开始使用`gh-pages`时，我会创建一个`gh-pages`分支，签入它，删除我不需要的或者妨碍项目成功部署的文件和文件夹，然后将项目推送到远程的`gh-pages`分支。每次我在我的主分支中做一个变更，我会进入`gh-pages`分支，做一个`git rebase master`将主分支中的变更合并到`gh-pages`，然后将这些变更推送到远程`gh-pages`分支。当我开始创建更复杂的 JavaScript 应用程序时，我发现这种方法既麻烦又浪费时间。我寻找替代方案。我就是这样被介绍给`Gulp`的。当我使用`Webpack`掌握了`Gulp`工作流程的时候，也是我决定接手`React`的时候。这大约是`Webpack 2`发布的时间。使用不带`Gulp`的`Webpack`意味着将我的部署调整到`gh-pages`。在`Gulp`中，我使用了 npm 包`gulp-gh-pages`，并用`deploy.js`创建了一个`Gulp`部署任务。但是当我开始在我的`React`项目中使用`Webpack`而不使用`Gulp`时，我不得不重新审视我的方法。

经过大量的研究和学习，我发现了`"git subtree"`。我不得不说，我真的很喜欢它。这是迄今为止我遇到的最快的`gh-pages`部署流程！

事情是这样的:

首先完成您的项目，以便为部署到您的`gh-pages`地点做好生产准备。

接下来，运行命令`git checkout -b gh-pages`。这将创建一个新的分支`gh-pages`，并通过一个命令将您签出到新的`gh-pages`分支。

您需要确保将一个空分支推送到您的远程`gh-pages`分支。为此，运行`git rm -rf .`命令。`rm`的意思是移除，r 代表递归。`f`代表力量。而`.`的意思是根中的一切。换句话说，就是项目中的所有文件夹以及这些文件夹中的所有文件。有时候`rm -r`就是不切，就得跑`rm -rf`。`rm -rf .`在一个命令中删除所有内容。

接下来，您必须准备并提交这些变更。您可以在一个命令中准备并提交一条提交消息:

`git commit -am "First commit to gh-pages branch"`

`am`中的`a`是 git add 的缩写，它将你的更改分阶段进行，`m`是`git commit -m`的缩写。此外，确保您记住提交消息的开始和结束引号，否则您将在终端窗口中被扣为人质。如果碰巧发生了这种情况，你可以用键盘上的`ctrl + c`命令关闭你的终端窗口实例。当您没有添加右引号时，它会退出提示>。但是，`Bash/zsh`允许您在>提示符后输入结束引号。然后按回车键。要了解更多关于退出 git 提交消息的信息，请访问 StackOverflow 线程:[我如何退出我的 git 提交消息？](https://stackoverflow.com/questions/26228848/how-do-i-exit-my-git-commit-message-im-not-in-the-vim-i-used-the-commit-m)。

现在，您已经准备好将这些更改推送到您的远程`gh-pages`分支机构。你可以用`git push origin gh-pages`这样做。

接下来，我们需要建立我们的`git subtree`,以使流程正常运行。我们首先要回到主分支。我们通过运行命令`git checkout master`来做到这一点。与`gulp-gh-pages`一样，`git subtree`的伟大之处在于，我们不必在`gh-pages`分支机构部署到远程！很酷，对吧？还能节省大量时间。因此，我们接下来运行的关键`git`命令是:

git 推送原点`git subtree split --prefix dist gh-pages` :gh-pages - force

(git subtree 之前和 dist gh-pages 之后需要一个反勾号。由于降价，它没有出现在这里。如有必要，请参考下面的相关文章进行进一步的澄清。)

这一切意味着什么？首先，一个`git subtree`允许你插入任何存储库作为另一个的子目录。它允许子项目包含在主项目的子目录中，可以选择包含子项目的整个历史。在我们的例子中，子目录是从主分支推到远程`gh-pages`分支的`dist`文件夹。子项目是`dist`文件夹中的文件。一个`subtree`仅仅是一个子目录，可以按照你想要的任何方式提交、分支和合并到你的项目中。话虽如此，让我们看看命令的其余部分。我们正在项目根目录下的`dist`文件夹中创建一个`git subtree`，而`--split`就像它听起来那样。它将`dist`从项目的剩余部分中分离出来，将其转换成一个子目录。`--prefix dist`意味着您正在发出信号，表明`dist`是您的项目中的目录，该目录已被选择为将被放入子目录的文件夹，该子目录将被推送到远程`gh-pages`分支。并且只有包含在该子目录中的内容才会被推送到 gh 页面。`:gh-pages --force`表示您在`origin`强制将`gh-pages`分支推到`remote gh-pages`分支。

由于您将来可能会对项目进行更改，并且不希望连续编写像`git subtree push --prefix dist origin gh-pages`这样的长命令，所以您可以在您的`package.json`中添加一个本地脚本。我创建了以下内容:

`"deploy": "npm run build && git subtree push --prefix dist origin gh-pages"`

我把它提升了一个档次。我将构建脚本和部署脚本结合在一起。这样，每当我对我的项目进行更改时，我首先运行 build 命令，这需要删除以前的 dist 构建，用当前构建替换它，然后将它推送到远程 gh-pages 分支。这可确保您的构建与项目中的最新更改保持同步。

因此，不仅我的编码随着时间的推移而发展，我的 devops 技能也在发展。它反映了更复杂的应用程序对更高工作流效率的需求。

然而，我再怎么强调理解您所实现的命令的每一个方面的必要性也不为过。你需要知道你在做什么，而不是盲目地执行只关注最终目标而忽略过程的命令。否则你作为开发者是不会成长的！我也不能过分强调掌握`Git`和掌握终端中的`Command Line`是多么重要。`Git`用于分布式版本控制，而`Command Line`使你永远不必离开终端窗口。一个伟大的时间节省者。最后，熟能生巧！或者至少...几乎如此！

编码快乐！

**相关链接:**

[一行部署您的网站到 gh-pages](http://www.damian.oquanta.info/posts/one-line-deployment-of-your-site-to-gh-pages.html)

[Git 子树的力量](https://developer.atlassian.com/blog/2015/05/the-power-of-git-subtree/)

[Webpack 2:完整的开发者指南](https://www.udemy.com/webpack-2-the-complete-developers-guide/)