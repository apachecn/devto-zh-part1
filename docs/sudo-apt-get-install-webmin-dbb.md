# 安装 webmin

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-webmin-dbb>

```
➜  ~ sudo vi /etc/apt/sources.list 
```

Enter fullscreen mode Exit fullscreen mode

添加到 source.list

```
➜ deb http://download.webmin.com/download/repository sarge contrib
➜ deb http://webmin.mirror.somersettechsolutions.co.uk/repository sarge contrib 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo wget http://www.webmin.com/jcameron-key.asc 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install webmin   
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  apt-show-versions libapt-pkg-perl libauthen-pam-perl libio-pty-perl libnet-ssleay-perl
The following NEW packages will be installed:
  apt-show-versions libapt-pkg-perl libauthen-pam-perl libio-pty-perl libnet-ssleay-perl webmin
0 upgraded, 6 newly installed, 0 to remove and 0 not upgraded.
Need to get 15.9 MB of archives.
After this operation, 163 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libnet-ssleay-perl amd64 1.72-1build1 [259 kB]
Get:2 http://download.webmin.com/download/repository sarge/contrib amd64 webmin all 1.860 [15.5 MB]
Get:3 http://us.archive.ubuntu.com/ubuntu xenial/universe amd64 libauthen-pam-perl amd64 0.16-3build2 [23.9 kB]
Get:4 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libio-pty-perl amd64 1:1.08-1.1build1 [30.2 kB]
Get:5 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libapt-pkg-perl amd64 0.1.29build7 [65.3 kB]
Get:6 http://us.archive.ubuntu.com/ubuntu xenial/universe amd64 apt-show-versions all 0.22.7 [29.6 kB]
Fetched 15.9 MB in 5s (2,650 kB/s)                                   
Selecting previously unselected package libnet-ssleay-perl.
(Reading database ... 125466 files and directories currently installed.)
Preparing to unpack .../libnet-ssleay-perl_1.72-1build1_amd64.deb ...
Unpacking libnet-ssleay-perl (1.72-1build1) ...
Selecting previously unselected package libauthen-pam-perl.
Preparing to unpack .../libauthen-pam-perl_0.16-3build2_amd64.deb ...
Unpacking libauthen-pam-perl (0.16-3build2) ...
Selecting previously unselected package libio-pty-perl.
Preparing to unpack .../libio-pty-perl_1%3a1.08-1.1build1_amd64.deb ...
Unpacking libio-pty-perl (1:1.08-1.1build1) ...
Selecting previously unselected package libapt-pkg-perl.
Preparing to unpack .../libapt-pkg-perl_0.1.29build7_amd64.deb ...
Unpacking libapt-pkg-perl (0.1.29build7) ...
Selecting previously unselected package apt-show-versions.
Preparing to unpack .../apt-show-versions_0.22.7_all.deb ...
Unpacking apt-show-versions (0.22.7) ...
Selecting previously unselected package webmin.
Preparing to unpack .../archives/webmin_1.860_all.deb ...
Unpacking webmin (1.860) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for systemd (229-4ubuntu19) ...
Processing triggers for ureadahead (0.100.0-19) ...
Setting up libnet-ssleay-perl (1.72-1build1) ...
Setting up libauthen-pam-perl (0.16-3build2) ...
Setting up libio-pty-perl (1:1.08-1.1build1) ...
Setting up libapt-pkg-perl (0.1.29build7) ...
Setting up apt-show-versions (0.22.7) ...
** initializing cache. This may take a while **
Setting up webmin (1.860) ...
Webmin install complete. You can now login to https://vagrant:10000/
as root with your root password, or as any user who can use sudo to run commands as root.
Processing triggers for systemd (229-4ubuntu19) ...
Processing triggers for ureadahead (0.100.0-19) ...
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo ufw allow 10000
Rules updated
Rules updated (v6)
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode