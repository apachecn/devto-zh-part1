# sudo dpkg -i 流浪者 _2.0.0_x86_64.deb

> 原文：<https://dev.to/psnebc/sudo-dpkg--i-vagrant200x8664deb-9kp>

### 下载

*   [https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html)

```
➜  ~ cd Downloads 
➜  Downloads l
total 76M
drwxr-xr-x  2 psnc psnc 4,0K Oct 23 22:00 .
drwxr-xr-x 24 psnc psnc 4,0K Oct 23 22:01 ..
-rw-rw-r--  1 psnc psnc  76M Oct 23 22:00 vagrant_2.0.0_x86_64.deb
➜  Downloads sudo dpkg -i vagrant_2.0.0_x86_64.deb 
Selecting previously unselected package vagrant.
(Reading database ... 245841 files and directories currently installed.)
Preparing to unpack vagrant_2.0.0_x86_64.deb ...
Unpacking vagrant (1:2.0.0) ...
Setting up vagrant (1:2.0.0) ...
➜  Downloads vagrant --version 
```

Enter fullscreen mode Exit fullscreen mode