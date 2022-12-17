# 自动化您的 Mac 设置并保持最新

> 原文：<https://dev.to/jorinvo/automate-your-mac-setup-and-keep-it-up-to-date-17l>

公司有脚本来启动和运行他们的开发机器是很常见的。我们中的许多人还创建了自己的版本，以便在新的计算机上快速运行我们的个人设置。

几年前，我也开始在我的装置上这样做。我遇到的一个问题是，我的设置很快就过时了，当我用它来设置一台新机器时，它肯定与我以前的设置不匹配；更有可能是彻底坏了，根本跑不动了。

我想解决这个问题，让我的设置脚本更有用。在过去的一年左右，我一直在使用一种新的设置脚本。称之为设置脚本可能是错误的。我的设置脚本现在是一个更新脚本。

所需的变化相当小。您只需要确保每个命令都是等幂的——脚本中的每个命令都必须工作相同，不管您连续执行它多少次。

最棒的是，现在您还可以使用这个脚本来更新您的系统，并且您有了一个以可重现的方式描述所有设置的文件。

我大约每周运行[我的设置脚本](https://github.com/jorinvo/dotfiles/blob/master/setup-mac)来获得最新的软件。

实际上是什么样的？

一些改变很简单，用
`mkdir -p`替换每个`mkdir`命令来忽略错误。

文件只有在不存在时才应该被链接。我的助手函数看起来是这样的:

```
link_to() {
  if [ ! -e $2 ]
  then ln -s $1 $2
    printf "\nLinked $2"
  fi
} 
```

Enter fullscreen mode Exit fullscreen mode

[自制软件](https://brew.sh/)应该只安装尚未安装的软件包(如果你还没有使用`brew`，帮你自己一个忙，马上停下来检查一下；没有它你就不想装软件)。在我的例子中，我将包保存在一个单独的 [`brew.txt`](https://github.com/jorinvo/dotfiles/blob/master/brew.txt) 文件中。为了只安装新的包，我像这样过滤它们:

```
comm -23 \
  <(sort brew.txt) \
  <( \
    { \
      brew ls --full-name; \
      brew cask ls | sed -e 's#^#Caskroom/cask/#'; \
    } \
    | sort \
  ) \
  | xargs brew install 
```

Enter fullscreen mode Exit fullscreen mode

此外，我还删除旧水龙头。这迫使我跟踪`brew.txt`文件中的所有包。

```
# Remove old taps
comm -13 <(sort brew.txt) <(brew leaves | sort) \
  | xargs brew rm

# Remove old cask taps
comm -13 \
  <(sort brew.txt) \
  <(brew cask ls | sed -e 's#^#Caskroom/cask/#') \
  | xargs brew cask rm 
```

Enter fullscreen mode Exit fullscreen mode

你也可以将上述方法用于其他源，如 [npm 包](https://www.npmjs.com/)和 [RubyGems](https://rubygems.org/) 。

这可能对你没有帮助，除非你正在使用 Vim，但是我也设置和更新了 [Neovim](https://neovim.io/) 和 [vim-plug](https://github.com/junegunn/vim-plug) 这里:

```
# Neovim
test -d ~/.vim || mkdir ~/.vim
if [ ! -d ~/.config/nvim ]
then ln -s ~/.vim ~/.config/nvim
  ln -s ~/.vimrc ~/.config/nvim/init.vim
  ln -s /usr/share/vim/vim74/spell/ ~/.config/nvim/
  printf "\nLinked Neovim config"
fi

## vim-plug
if [ ! -e ~/.config/nvim/autoload/plug.vim ]
then printf "\nInstalling vim-plug"
  curl -fLo ~/.config/nvim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
fi nvim +PlugInstall +PlugUpgrade +PlugUpdate +PlugClean! +UpdateRemotePlugins +qall 
```

Enter fullscreen mode Exit fullscreen mode

看看我的[点文件](https://github.com/jorinvo/dotfiles)了解更多。也许您会发现一些新的技巧添加到您的 Vim 或 Bash 设置中。
我很想听听你对你的自动设置的一些部分的看法！