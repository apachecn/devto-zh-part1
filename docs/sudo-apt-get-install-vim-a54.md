# sudo apt-get 安装 vim

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-vim-a54>

```
vagrant@vagrant:~$ sudo add-apt-repository ppa:jonathonf/vim
 Backport of Vim from as far upstream as will build.

https://github.com/vim/vim/releases

I won't be packaging each and every patch version on release unless it has an obvious impact on the Linux version; it's just not time-effective.

(Also: check out the credit on 8.0.0005 ;)

***

Total downloads at 2017-09-26: 88,570

Total downloads of 8.0.0786: 3,090
Total downloads of 8.0.1008: 1,884
 More info: https://launchpad.net/~jonathonf/+archive/ubuntu/vim
Press [ENTER] to continue or ctrl-c to cancel adding it

gpg: keyring `/tmp/tmpt1pb21qo/secring.gpg' created
gpg: keyring `/tmp/tmpt1pb21qo/pubring.gpg' created
gpg: requesting key F06FC659 from hkp server keyserver.ubuntu.com
gpg: /tmp/tmpt1pb21qo/trustdb.gpg: trustdb created
gpg: key F06FC659: public key "Launchpad PPA for J Fernyhough" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
OK 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ sudo apt-get update     
Hit:1 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease                                                                         
Hit:2 http://security.ubuntu.com/ubuntu xenial-security InRelease                                                                           
Get:3 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease [18.0 kB]                                            
Hit:4 http://us.archive.ubuntu.com/ubuntu xenial InRelease                        
Hit:5 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease                                  
Hit:6 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                
Hit:7 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease                                
Get:8 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main amd64 Packages [4848 B]
Get:9 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main i386 Packages [4828 B]
Get:10 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main Translation-en [2780 B]
Fetched 30.5 kB in 3s (10.1 kB/s)                   
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ sudo apt-get install vim
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  libyaml-0-2 python-crypto python-ecdsa python-httplib2 python-jinja2 python-markupsafe python-paramiko python-pkg-resources python-setuptools python-six python-yaml sshpass
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  vim-common vim-runtime vim-tiny xxd
Suggested packages:
  ctags vim-doc vim-scripts vim-gtk-py2 | vim-gtk3-py2 | vim-athena-py2 | vim-nox-py2 indent
The following NEW packages will be installed:
  xxd
The following packages will be upgraded:
  vim vim-common vim-runtime vim-tiny
4 upgraded, 1 newly installed, 0 to remove and 3 not upgraded.
Need to get 7432 kB of archives.
After this operation, 1757 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main amd64 vim amd64 2:8.0.1176-0york1~16.04 [1123 kB]
Get:2 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main amd64 vim-tiny amd64 2:8.0.1176-0york1~16.04 [459 kB]                                                                        
Get:3 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main amd64 vim-runtime all 2:8.0.1176-0york1~16.04 [5535 kB]                                                                      
Get:4 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main amd64 vim-common all 2:8.0.1176-0york1~16.04 [179 kB]                                                                        
Get:5 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main amd64 xxd amd64 2:8.0.1176-0york1~16.04 [136 kB]                                                                             
Fetched 7432 kB in 24s (301 kB/s)                                                                                                                                                            
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
(Reading database ... 85870 files and directories currently installed.)
Preparing to unpack .../vim_2%3a8.0.1176-0york1~16.04_amd64.deb ...
Unpacking vim (2:8.0.1176-0york1~16.04) over (2:7.4.1689-3ubuntu1.2) ...
Preparing to unpack .../vim-tiny_2%3a8.0.1176-0york1~16.04_amd64.deb ...
Unpacking vim-tiny (2:8.0.1176-0york1~16.04) over (2:7.4.1689-3ubuntu1.2) ...
Preparing to unpack .../vim-runtime_2%3a8.0.1176-0york1~16.04_all.deb ...
Adding 'diversion of /usr/share/vim/vim80/doc/help.txt to /usr/share/vim/vim80/doc/help.txt.vim-tiny by vim-runtime'
Adding 'diversion of /usr/share/vim/vim80/doc/tags to /usr/share/vim/vim80/doc/tags.vim-tiny by vim-runtime'
Unpacking vim-runtime (2:8.0.1176-0york1~16.04) over (2:7.4.1689-3ubuntu1.2) ...
Removing 'diversion of /usr/share/vim/vim74/doc/help.txt to /usr/share/vim/vim74/doc/help.txt.vim-tiny by vim-runtime'
Removing 'diversion of /usr/share/vim/vim74/doc/tags to /usr/share/vim/vim74/doc/tags.vim-tiny by vim-runtime'
Preparing to unpack .../vim-common_2%3a8.0.1176-0york1~16.04_all.deb ...
Unpacking vim-common (2:8.0.1176-0york1~16.04) over (2:7.4.1689-3ubuntu1.2) ...
Selecting previously unselected package xxd.
Preparing to unpack .../xxd_2%3a8.0.1176-0york1~16.04_amd64.deb ...
Unpacking xxd (2:8.0.1176-0york1~16.04) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Setting up xxd (2:8.0.1176-0york1~16.04) ...
Setting up vim-common (2:8.0.1176-0york1~16.04) ...
Installing new version of config file /etc/vim/vimrc ...
Setting up vim-runtime (2:8.0.1176-0york1~16.04) ...
Setting up vim (2:8.0.1176-0york1~16.04) ...
Setting up vim-tiny (2:8.0.1176-0york1~16.04) ...
Installing new version of config file /etc/vim/vimrc.tiny ... 
```

Enter fullscreen mode Exit fullscreen mode