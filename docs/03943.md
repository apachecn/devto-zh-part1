# sudo-v & & wget-NV-O-https://download.calibre-ebook.com/linux-installer.py

> 原文：<https://dev.to/psnebc/sudo--v--wget--nv--o--httpsdownloadcalibre-ebookcomlinux-installerpy-573>

### 更新口径 17.10.2017

### Linux 造币厂 LTS 罗莎 17.3 KDE

```
sudo -v && wget -nv -O- https://download.calibre-ebook.com/linux-installer.py | sudo python -c "import sys; main=lambda:sys.stderr.write('Download failed\n'); exec(sys.stdin.read()); main()"

[sudo] password for patrick: 
2017-10-17 18:22:57 URL:https://download.calibre-ebook.com/linux-installer.py [27407/27407] -> "-" [1]
Installing to /opt/calibre
Downloading tarball signature securely...
Will download and install calibre-3.9.0-x86_64.txz 
                                                                             Downloading calibre-3.9.0-x86_64.txz                                                                             
100% [====================================================================================================================================================================================]
                                                                                                                                                                                              Downloaded 62208052 bytes 
Checking downloaded file integrity... 
Extracting files to /opt/calibre ...
Extracting application files... 
Creating symlinks...
        Symlinking /opt/calibre/ebook-edit to /usr/bin/ebook-edit
        Symlinking /opt/calibre/ebook-polish to /usr/bin/ebook-polish
        Symlinking /opt/calibre/web2disk to /usr/bin/web2disk
        Symlinking /opt/calibre/markdown-calibre to /usr/bin/markdown-calibre
        Symlinking /opt/calibre/ebook-viewer to /usr/bin/ebook-viewer
        Symlinking /opt/calibre/ebook-convert to /usr/bin/ebook-convert
        Symlinking /opt/calibre/lrf2lrs to /usr/bin/lrf2lrs
        Symlinking /opt/calibre/calibre-debug to /usr/bin/calibre-debug
        Symlinking /opt/calibre/calibredb to /usr/bin/calibredb
        Symlinking /opt/calibre/ebook-device to /usr/bin/ebook-device
        Symlinking /opt/calibre/fetch-ebook-metadata to /usr/bin/fetch-ebook-metadata
        Symlinking /opt/calibre/calibre-server to /usr/bin/calibre-server
        Symlinking /opt/calibre/calibre-customize to /usr/bin/calibre-customize
        Symlinking /opt/calibre/lrfviewer to /usr/bin/lrfviewer
        Symlinking /opt/calibre/calibre to /usr/bin/calibre
        Symlinking /opt/calibre/calibre-parallel to /usr/bin/calibre-parallel
        Symlinking /opt/calibre/ebook-meta to /usr/bin/ebook-meta
        Symlinking /opt/calibre/calibre-smtp to /usr/bin/calibre-smtp
        Symlinking /opt/calibre/lrs2lrf to /usr/bin/lrs2lrf
Setting up command-line completion...
Installing zsh completion to: /usr/share/zsh/vendor-completions/_calibre
Installing bash completion to: /usr/share/bash-completion/completions/calibre
Setting up desktop integration...
Creating un-installer: /usr/bin/calibre-uninstall
Run "calibre" to start calibre 
```

Enter fullscreen mode Exit fullscreen mode

### 图片

[https://thepractical dev . S3 . Amazon AWS . com/I/rztjbfgziackl 1 l 961y . png](https://thepracticaldev.s3.amazonaws.com/i/rztjpbfgziackl1l961y.png)