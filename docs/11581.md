# 在崇高文本中安装 Emmet (Zen 编码)

> 原文：<https://dev.to/adamkdean/install-emmet-zen-coding-in-sublime-text-1n65>

对于那些不知道的人来说，Zen Coding——或现在所知的[Emmet](http://docs.emmet.io/)——是一套文本编辑器插件，允许通过内容辅助以 HTML、XML、XSL 和其他结构化代码格式进行高速编码和编辑。(感谢维基百科！)

如果你还没有使用它，你应该使用，我将向你(或者未来的我)展示如何为 Sublime Text 安装它。请注意，美国用户必须使用不同的组合键，所以如果菜单没有弹出，请尝试其他组合键。

你要做的第一件事是打开崇高的文本。如果您还没有安装软件包管理器，那么通过打开控制台`Ctrl` + `'`(在 Linux 中您可能需要使用`Ctrl` +`)并粘贴以下内容来安装它:

```
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read()) 
```

按下回车键，等待安装。完成后，按`Ctrl` + `Shift` + `P`调出命令面板，键入`Install`，按 enter 选择`Package Control: Install package`。

这应该会弹出一个新菜单，你现在可以输入`Emmet`并按回车键。给它几秒钟的安装时间，现在你可以开始了，你已经安装了 Emmet 或者 Zen 编码，可以更有效地编写你的 HTML 和 CSS 了。