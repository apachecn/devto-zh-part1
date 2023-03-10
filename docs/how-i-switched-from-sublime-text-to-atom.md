# 我如何从崇高的文本转换到原子

> 原文：<https://dev.to/zackphilipps/how-i-switched-from-sublime-text-to-atom>

[![How I Switched from Sublime Text to Atom](img/c1baf9de079275f84e420e360aa64501.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J1v7ufy2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://zackphilipps.com/conteimg/2016/08/Screen-Shot-2016-08-01-at-13-26-04.png)

([原子材质深色](https://atom.io/themes/atom-material-ui)带[熊猫语法](http://panda.siamak.work/)和[猫怪](https://madmalik.github.io/mononoki/)字样)

当我第一次发现 Sublime Text 时，我爱上了它的可配置性和扩展其功能的包的可用性。当 Atom 第一次出现时，我尝试了一下，但很快就感到沮丧，因为仍然有一些问题需要解决。

快进大约两年后，我开始了在第三元素的新工作。整个开发团队都在使用 Atom，所以我想再试一次。

肯定有一段适应期。但是，几个星期后，我意识到 Atom 做了 Sublime 所做的一切，除了更好！还有更多！

* * *

[![How I Switched from Sublime Text to Atom](img/754bfaf7f888adc2632b3e3e9261ba69.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OabKIiC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://zackphilipps.com/conteimg/2016/08/atom-logo-1.jpg)

Atom 立即提供了默认包，您可以禁用这些包。我立即禁用了`wrap-guide`包，因为我无法执行贯穿我代码的那一行，而且我不支持 80 个字符的偏好。但这本身就是对 Sublime 的改进。这里还有一些。

#### 开箱即用的改进超越了 Sublime

1.  更好的默认主题和语法高亮显示
2.  更直观的设置面板
3.  拼写检查
4.  Markdown preview，它使得所有昂贵的 Markdown 编写应用程序完全无用
5.  Git 集成，不再需要 [GitGutter。](https://github.com/jisaacks/GitGutter) Atom 会通过改变文件/目录名的颜色和文件中的行号来指示对`git`项目的修改
6.  更好的自动完成建议，因为它会在整个项目中搜索与您键入的内容相匹配的内容，而不仅仅是您当前所在的文件
7.  [默认选择直到](https://github.com/xavi-/sublime-selectuntil)行为
8.  [sidebareenhancements](https://github.com/titoBouzout/SideBarEnhancements)默认行为
9.  [Atom 的包管理器](https://github.com/atom/apm)是捆绑的。不再需要按下 control-backtick，在线查找代码片段，并将其粘贴到控制台中🔥
10.  蓬勃发展的开发人员社区对所有这一切都负有责任

#### 重大调整

事实上，我不得不适应 Atom 的大部分工作是按键绑定。我在 Sublime 中设置了几个自定义的，在 Atom 中遇到了困难。这是因为我加错了。(它们区分大小写。)

在[一个前端开发者的崇高文本设置中提到，](https://dev.to/zackphilipps/a-front-end-developers-sublime-text-setup-temp-slug-48528)我已经习惯用`shift-cmd-d`到**删除**行和`alt-cmd-d`到**复制**行。嗯，我像这样添加我的键绑定:

```
'atom-workspace atom-text-editor:not([mini])':  
  'shift-cmd-D': 'unset!'
  'shift-cmd-D': 'editor:delete-line'
  'alt-cmd-D': 'editor:duplicate-lines' 
```

Enter fullscreen mode Exit fullscreen mode

*删除行*有效，但*重复行无效。这是因为 Atom 希望我输入`alt-cmd-shift-D`，因为我输入的是大写字母`D`。这是正确的做法:* 

```
'atom-workspace atom-text-editor:not([mini])':  
  'shift-cmd-D': 'unset!'
  'shift-cmd-D': 'editor:delete-line'
  'alt-cmd-d': 'editor:duplicate-lines' 
```

Enter fullscreen mode Exit fullscreen mode

需要习惯的另一件大事是`tab`键的行为。在 Sublime 中，您可以使用`tab`(很像一个终端)来扩展代码片段并使用建议的自动完成功能。在 Atom 中，`return`键似乎更适合自动完成，而`tab`仍然适用于代码片段。有点奇怪。

^:这可能是因为我使用了 Emmet，它允许`tab`扩展它所有的缩写。我最终添加了另一个键绑定。Emmet 默认的 keybinding 是`ctrl+e`展开缩写；我把它改成了`cmd-e`,因为我不再用它做其他任何事情，而且它对我的手指来说更直观。

* * *

现在，让我们来看看有趣的东西:包裹！我能够为 Sublime 中我喜爱的每个包找到替代品，所有这些包都达到或超过了它们的前辈。

我还创建了我的第一个 Atom 包。多亏了 [apm](https://github.com/atom/apm) ，发布变得异常容易。

#### 套餐

[Atom 美化](https://atom.io/packages/atom-beautify)——在 Atom 中美化 HTML、CSS、JavaScript、PHP、Python、Ruby、Java、C、C++、C#、Objective-C、CoffeeScript、TypeScript、Coldfusion、SQL 等等。

这取代了我的 Sublime 设置中的 JavaScript 美化和 T2 萨斯美化。如你所见，它处理的语言远不止这些。

Emmet–另一个节省时间的软件包，提供了许多有用的 HTML 片段。

[文件图标](https://atom.io/packages/file-icons)–指定文件扩展名图标和颜色以改善视觉效果。

[Language Blade](https://atom.io/packages/language-blade)–Laravel 中使用的 Blade 模板引擎的语法高亮显示。

[Language Twig](https://atom.io/packages/language-twig)–Twig 对 Atom 的支持。

[小于斜杠](https://atom.io/packages/less-than-slash)–当键入小于斜杠(`</`)时，添加 HTML 标签的自动结束。

让 Atom 成为更好的 Markdown 编辑器和更简单的静态博客工具。

我喜欢这个，因为它让 Atom 的行为更像 Ghost。我可以按下`cmd-b`来加粗我的减价文本，按下`cmd-k`来插入超链接，等等。

[分割比较](https://atom.io/packages/split-diff)–一个分割窗格比较工具。

[![How I Switched from Sublime Text to Atom](img/f4db7bb1315eba664a4dae8b47e39ebd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xx6HsE1L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://zackphilipps.com/conteimg/2016/08/split-diff2.gif)

[同步设置](https://atom.io/packages/sync-settings)–跨设备同步软件包设置、键盘映射和已安装的软件包。

[树形视图自动调整大小](https://atom.io/packages/tree-view-autoresize)–打开/关闭文件夹时自动调整树形视图的大小。

[![How I Switched from Sublime Text to Atom](img/e1cdb3f21dbedcd633f53af21c3fc220.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vTTl0dW_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://zackphilipps.com/conteimg/2016/08/tree-view-autoresize.gif)

ZP ACF 片段–这是一个高级定制字段 WordPress 插件的 Atom 片段集合。基于[高级定制字段片段的崇高文本](https://github.com/iamhexcoder/acf_snippets)包。

**更新:**我已经在我的堆栈中添加了几个更有用的包，包括 [atom-html-preview](https://atom.io/packages/atom-html-preview) ， [autocomplete-paths](https://atom.io/packages/autocomplete-paths) ， [git-time-machine](https://atom.io/packages/git-time-machine) ， [hyperlink-hyperclick](https://atom.io/packages/hyperlink-hyperclick) ， [merge-conflicts](https://atom.io/packages/merge-conflicts) ， [sublime-block-comment](https://atom.io/packages/sublime-block-comment) ，更多的语言和 linters。

“棉绒？”[阅读林挺代码如何改变我的生活。](http://zackphilipps.com/how-code-linting-changed-my-life/)