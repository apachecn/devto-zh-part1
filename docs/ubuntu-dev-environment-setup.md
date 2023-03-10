# Ubuntu 开发环境设置

> 原文：<https://dev.to/dexterbrylle/ubuntu-dev-environment-setup>

我有一台供个人使用的 Macbook Pro，并且已经设置好了工作流程。然而，在我的日常工作中，我被分配了一台运行 Windows 的机器。我从 2013 年开始改用 macOS，这种改变在效率和生产率方面让我有些退步。所以我决定在 Virtualbox 上运行 Ubuntu 16.04。

以下是我用来帮助您启动和运行的一些工具。

# Ubuntu 开发环境设置

### 铬

```
sudo apt install -y chromium-browser 
```

Enter fullscreen mode Exit fullscreen mode

### 铬

```
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -

sudo sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'

sudo apt install -y google-chrome-stable 
```

Enter fullscreen mode Exit fullscreen mode

### 节点

*V6 . x*T2】

```
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -

sudo apt install -y nodejs 
```

Enter fullscreen mode Exit fullscreen mode

*V8 . x*T2】

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -

sudo apt-install -y nodejs 
```

Enter fullscreen mode Exit fullscreen mode

### zsh 和 oh-my-zsh

安装 zsh

```
sudo apt install zsh 
```

Enter fullscreen mode Exit fullscreen mode

安装 oh-my-zsh

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

Enter fullscreen mode Exit fullscreen mode

*你可以在这里阅读更多关于 oh-my-zsh*

修复了将 zsh/oh-my-zsh 设置为默认值的问题

*

```
 chsh -s $(which zsh)
    ```

* You may need to logoff and login again for the changes to take effect

### Visual Studio Code

Add the repository 
```

Enter fullscreen mode Exit fullscreen mode

curl[https://packages.microsoft.com/keys/microsoft.asc](https://packages.microsoft.com/keys/microsoft.asc)| gpg-dearmor>微软. gpg

sudo mv Microsoft . gpg/etc/apt/trusted . gpg . d/Microsoft . gpg

sudo sh-c ' echo " deb[arch = amd64][https://packages.microsoft.com/repos/vscode](https://packages.microsoft.com/repos/vscode)stable main ">/etc/apt/sources . list . d/vs code . list '

```
 Upate cache and install vs code 
```

Enter fullscreen mode Exit fullscreen mode

sudo apt 更新

sudo 安装 code #或 code-insiders

```
 ### Sublime Text 3 
```

Enter fullscreen mode Exit fullscreen mode

https://download.sublimetext.com/sublimehq-pub.gpg| sudo apt-key add-

echo " deb[https://download.sublimetext.com/](https://download.sublimetext.com/)apt/stable/" | sudo tee/etc/apt/sources . list . d/sublime-text . list

sudo apt 更新

sudo 安装 sublime-text

```
 ### GitKraken 
```

Enter fullscreen mode Exit fullscreen mode

https://release.gitkraken.com/linux/gitkraken-amd64.tar.gz

塔尔-xvzf gitkraken-amd64.tar.gz

```
 or 
```

Enter fullscreen mode Exit fullscreen mode

[https://release . git krak . com/Linux/git krak en-amd64 . deb【注:http://release . git krak . com/git krak . deb】的缩写形式](https://release.gitkraken.com/linux/gitkraken-amd64.deb)

d kg-I git krak en-amd65 . deb

```
 ### MongoChef

Download the .tar file [here](https://studio3t.com/download/) 
```

Enter fullscreen mode Exit fullscreen mode

CD ~/下载

tar-xvzf studio-3t Linux-x64 . tar . gz

cd studio-3t-linux-x64/bin

。/studio-3t.sh

```
 ### Spotify 
```

Enter fullscreen mode Exit fullscreen mode

sudo apt-key adv-key server hkp://key server . Ubuntu . com:80-recv-keys bbebdcb 318 ad 50 EC 6865090613 b 00 f1 FD 2c 19886 0 df 731 e 45 ce 24 f 27 eeeb 1450 efdc 8610341d 9410

echo deb[http://repository.spotify.com](http://repository.spotify.com)稳定非免费| sudo tee/etc/apt/sources . list . d/Spotify . list

sudo apt 更新

sudo 安装 y- spotify 客户端

```
 I hope this may serve as a reference to people/devs with a fresh install of Ubuntu.

Thanks! 
```

Enter fullscreen mode Exit fullscreen mode