# sudo 安装 zsh

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-zsh-kp>

```
vagrant@vagrant:~$ sudo apt-get install zsh
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  zsh-common
Suggested packages:
  zsh-doc
The following NEW packages will be installed:
  zsh zsh-common
0 upgraded, 2 newly installed, 0 to remove and 3 not upgraded.
Need to get 3822 kB of archives.
After this operation, 14.2 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 zsh-common all 5.1.1-1ubuntu2 [3170 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 zsh amd64 5.1.1-1ubuntu2 [651 kB]                                                                                                
Fetched 3822 kB in 10s (365 kB/s)                                                                                                                                                            
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
        LANGUAGE = "en_US:",
        LC_ALL = (unset),
        LC_CTYPE = "de_LU.UTF-8",
        LANG = "en_US"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US").
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
Selecting previously unselected package zsh-common.
(Reading database ... 86860 files and directories currently installed.)
Preparing to unpack .../zsh-common_5.1.1-1ubuntu2_all.deb ...
Unpacking zsh-common (5.1.1-1ubuntu2) ...
Selecting previously unselected package zsh.
Preparing to unpack .../zsh_5.1.1-1ubuntu2_amd64.deb ...
Unpacking zsh (5.1.1-1ubuntu2) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up zsh-common (5.1.1-1ubuntu2) ...
Setting up zsh (5.1.1-1ubuntu2) ...
update-alternatives: using /bin/zsh5 to provide /bin/zsh (zsh) in auto mode
update-alternatives: using /bin/zsh5 to provide /bin/rzsh (rzsh) in auto mode 
```

Enter fullscreen mode Exit fullscreen mode