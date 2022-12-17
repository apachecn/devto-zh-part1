# 在崇高的文本中减少突出显示

> 原文：<https://dev.to/adamkdean/install-less-highlighting-in-sublime-text-4nik>

默认情况下，Sublime Text 不会减少高亮显示，但是它非常容易安装。

我从我之前关于如何在 Sublime Text 中安装 Zen Coding/Emmet 的博文中摘录了以下内容。

> 你要做的第一件事是打开崇高的文本。如果您还没有安装软件包管理器，那么通过打开控制台`Ctrl` + `'`并粘贴以下内容来安装它:
> 
> `import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())`
> 
> 按下回车键，等待安装。完成后，按`Ctrl` + `Shift` + `P`调出命令面板，键入`Install`，按 enter 选择`Package Control: Install package`。

菜单出现后，键入`LESS`并选择第一个包。

享受吧。