# sudo dpkg-I MySQL-work bench-community-6 . 3 . 9-1 Ubuntu 16.04-amd64 . deb

> 原文：<https://dev.to/psnebc/sudo-dpkg--i-mysql-workbench-community-639-1ubuntu1604-amd64deb-dl7>

```
#
# https://dev.mysql.com/downloads
#
cd /home/psnc/Downloads 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  Downloads sudo dpkg -i mysql-workbench-community-6.3.9-1ubuntu16.04-amd64.deb 
Selecting previously unselected package mysql-workbench-community.
(Reading database ... 335043 files and directories currently installed.)
Preparing to unpack mysql-workbench-community-6.3.9-1ubuntu16.04-amd64.deb ...
Unpacking mysql-workbench-community (6.3.9-1ubuntu16.04) ...
dpkg: dependency problems prevent configuration of mysql-workbench-community:
 mysql-workbench-community depends on libgtkmm-3.0-1v5 (>= 3.18.0); however:
  Package libgtkmm-3.0-1v5 is not installed.
 mysql-workbench-community depends on libpcrecpp0v5 (>= 7.7); however:
  Package libpcrecpp0v5 is not installed.

dpkg: error processing package mysql-workbench-community (--install):
 dependency problems - leaving unconfigured
Processing triggers for shared-mime-info (1.5-2ubuntu0.1) ...
Unknown media type in type 'all/all'
Unknown media type in type 'all/allfiles'
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for desktop-file-utils (0.22+linuxmint1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Errors were encountered while processing: mysql-workbench-community 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  Downloads sudo apt-get -f install Reading package lists... Done
Building dependency tree       
Reading state information... Done
Correcting dependencies... Done
The following additional packages will be installed:
  libgtkmm-3.0-1v5 libpcrecpp0v5
The following NEW packages will be installed:
  libgtkmm-3.0-1v5 libpcrecpp0v5
0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
1 not fully installed or removed.
Need to get 967 kB of archives.
After this operation, 6.179 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgtkmm-3.0-1v5 amd64 3.18.0-1 [816 kB]
Get:2 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 libpcrecpp0v5 amd64 2:8.41-1.1+ubuntu16.04.1+deb.sury.org+3 [152 kB]
Fetched 967 kB in 1s (518 kB/s)                                              
Selecting previously unselected package libgtkmm-3.0-1v5:amd64.
(Reading database ... 336313 files and directories currently installed.)
Preparing to unpack .../libgtkmm-3.0-1v5_3.18.0-1_amd64.deb ...
Unpacking libgtkmm-3.0-1v5:amd64 (3.18.0-1) ...
Selecting previously unselected package libpcrecpp0v5:amd64.
Preparing to unpack .../libpcrecpp0v5_2%3a8.41-1.1+ubuntu16.04.1+deb.sury.org+3_amd64.deb ...
Unpacking libpcrecpp0v5:amd64 (2:8.41-1.1+ubuntu16.04.1+deb.sury.org+3) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Setting up libgtkmm-3.0-1v5:amd64 (3.18.0-1) ...
Setting up libpcrecpp0v5:amd64 (2:8.41-1.1+ubuntu16.04.1+deb.sury.org+3) ...
Setting up mysql-workbench-community (6.3.9-1ubuntu16.04) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ... 
```

Enter fullscreen mode Exit fullscreen mode