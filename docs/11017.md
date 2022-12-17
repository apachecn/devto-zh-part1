# 我的 emacs 设置

> 原文：<https://dev.to/nflamel/my-emacs-setup-74i>

前段时间转到 emacs。在使用它之前，我是一个 Vim 用户，在使用了相当长一段时间之后，我决定尝试 emacs，看看它能提供什么。

我一开始用的是 [prelude](https://github.com/bbatsov/prelude) ，效果很好，我也很满意。我在上面发现的唯一问题就是太想念模态版了。所以，在 prelude 的一段时间后，我搬到了[的太空中心](http://spacemacs.org/)。

Spacemacs 将 emacs 与一个非常理智的配置放在一起，为 [evil](https://www.emacswiki.org/emacs/Evil)

它工作得很好，如果你是一个 Vim 用户，你正在考虑尝试 emacs，这是一个很好的方法。如果过了一段时间你不想再使用模态版了，你可以配置它以“emacs 方式”工作，它将和 evil 一样工作。我不打算解释 Spacemcas 上所有酷的东西，因为你可以检查他们的页面和惊人的文档，它解释了一切。

我对 emacs 的定制很少，我主要使用 spacemacs 的默认配置:

## Emacs 作为守护进程

启动 spacemacs 需要一点时间，所以让它工作的最好方法是将 emacs 作为一个守护进程启动，然后使用`emacsclient`命令连接到它。

默认情况下，启动 spacemcas 会将其作为一个守护进程启动，但有时我会发现自己从桌面环境启动 Spacemacs，然后连接到它进行小版本的操作，如 quick git commits。现在我在我的开发机器上使用 [Arch Linux](https://www.archlinux.org/) ，所以我开始玩 [systemd](https://wiki.archlinux.org/index.php/systemd) 来为我启动它。事实证明，这是一个非常快速的胜利。arch 和 systemd 的一个好处是，您可以设置 systemd 让用户管理单元文件(不是由 root 启动)。为此，您只需在`~/.config/systemd/user/emacs.service`上创建单元

这是我用在太空相机上的那个:

```
[Unit]
Description=Emacs daemon

[Service]
Type=forking
ExecStart=/usr/bin/emacs --daemon
ExecStop=/usr/bin/emacsclient --eval "(kill-emacs)"
Restart=always

[Install]
WantedBy=default.target 
```

Enter fullscreen mode Exit fullscreen mode

添加文件后只需做:

```
systemctl --user enable emacs # to make it start with the machine
systemctl --user start emacs # to start it right away
systemctl --user stop emacs # to stop the daemon
systemctl --user restart emacs # to restart the daemon 
```

Enter fullscreen mode Exit fullscreen mode

## 从终端连接到 emacs 守护进程

现在，要连接到您的 emacs 守护进程，您只需做`emacsclient -t`。

我通常在我的 zshrc:
中将它设置为默认编辑器

```
export EDITOR="emacsclient -t" 
```

Enter fullscreen mode Exit fullscreen mode

然后我为它设置了一个别名:

```
alias e=$EDITOR 
```

Enter fullscreen mode Exit fullscreen mode

然后，当我想编辑一个文件时，我只需要:`e filename.ext`

## 从桌面环境连接到 emacs 守护进程

这对于在终端中进行快速编辑来说非常酷，但是大多数时候我在桌面环境中使用 emacs 窗口。我也希望能够连接到守护进程，所以我最终将它添加到了`~/.local/share/applications/spacemacs.desktop` :

```
[Desktop Entry]
Name=Spacemacs
GenericName=Text Editor
Comment=Edit text
MimeType=text/english;text/plain;text/x-makefile;text/x-c++hdr;text/x-c++src;text/x-chdr;text/x-csrc;text/x-java;text/x-moc;text/x-pascal;text/x-tcl;text/x-tex;application/x-shellscript;text/x-c;text/x-c++;
Exec=emacsclient -c %F
Icon=~/.emacs.d/core/banners/img/spacemacs.png
Type=Application
Terminal=false
Categories=Development;TextEditor;
StartupWMClass=Emacs 
```

Enter fullscreen mode Exit fullscreen mode

`-c`标志要求 emacsclient 创建一个由窗口管理器控制的新框架。在我的例子中是 Gnome Shell。

## Emacs 定制

正如我之前所说，我几乎使用“香草”太空 MAC。它的大部分配置对我做的每件事都足够好了，除了几件事。

所有这些配置都在`~/.spacemacs`的`dotspacemcas/user-config()`功能中

### 字运动:

如果你是 Vim 用户，你会期望在`method-name`中移动 1 个单词会把你带到最后一个`e`。在 emacs 中情况并非如此。用 ruby 和 javascript 来改:

```
(add-hook 'js2-mode-hook #'(lambda () (modify-syntax-entry ?_ "w")))
(add-hook 'ruby-mode-hook #'(lambda () (modify-syntax-entry ?_ "w"))) 
```

Enter fullscreen mode Exit fullscreen mode

### 用 ruby 编辑 rabl 文件:

```
;; *.rabl files are ruby
(add-to-list 'auto-mode-alist '("\\.rabl" . ruby-mode))) 
```

Enter fullscreen mode Exit fullscreen mode

### 用弹丸选择项目时打开项目根

```
;; Go to project root folder when switching to it
(setq projectile-switch-project-action 'projectile-dired) 
```

Enter fullscreen mode Exit fullscreen mode

### 有一个单独的 emacs 定制文件。

Emacs 有一个简单的定制机制[(参见)](https://www.gnu.org/software/emacs/manual/html_node/emacs/Easy-Customization.html)。我大部分时间不使用它，但是有些时候 emacs 会因为它而把东西存储在`~/.spacemacs`文件中，即使你不想要它。为了避免这种情况，我使用了一个单独的文件来进行定制。为此，将此添加到您的`dotspacemacs/user-init()` :

```
(setq custom-file "~/.emacs-custom.el")
(load custom-file) 
```

Enter fullscreen mode Exit fullscreen mode

所有定制都将指向`~/.emacs-custom.el`，而不是您的 dotspacemacs 文件。

### 其他

我还定制了其他东西，比如我加载的图层等...但不多。如果你好奇，你可以查看我的 dotspacemacs 文件

我肯定我错过了一些东西，但这是大部分。享受 emacs 和良好的黑客！