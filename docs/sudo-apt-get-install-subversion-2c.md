# sudo apt-get 安装版本

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-subversion-2c>

```
➜  ~ sudo apt-get install subversion
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libapr1 libaprutil1 libserf-1-1 libsvn1
Suggested packages:
  db5.3-util subversion-tools
The following NEW packages will be installed:
  libapr1 libaprutil1 libserf-1-1 libsvn1 subversion
0 upgraded, 5 newly installed, 0 to remove and 1 not upgraded.
Need to get 1.687 kB of archives.
After this operation, 6.653 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libapr1 amd64 1.5.2-3 [86,0 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial/main amd64 libaprutil1 amd64 1.5.4-1build1 [77,1 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/main amd64 libserf-1-1 amd64 1.3.8-1 [45,5 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libsvn1 amd64 1.9.3-2ubuntu1.1 [1.170 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 subversion amd64 1.9.3-2ubuntu1.1 [308 kB]
Fetched 1.687 kB in 0s (2.241 kB/s) 
Selecting previously unselected package libapr1:amd64.
(Reading database ... 254675 files and directories currently installed.)
Preparing to unpack .../libapr1_1.5.2-3_amd64.deb ...
Unpacking libapr1:amd64 (1.5.2-3) ...
Selecting previously unselected package libaprutil1:amd64.
Preparing to unpack .../libaprutil1_1.5.4-1build1_amd64.deb ...
Unpacking libaprutil1:amd64 (1.5.4-1build1) ...
Selecting previously unselected package libserf-1-1:amd64.
Preparing to unpack .../libserf-1-1_1.3.8-1_amd64.deb ...
Unpacking libserf-1-1:amd64 (1.3.8-1) ...
Selecting previously unselected package libsvn1:amd64.
Preparing to unpack .../libsvn1_1.9.3-2ubuntu1.1_amd64.deb ...
Unpacking libsvn1:amd64 (1.9.3-2ubuntu1.1) ...
Selecting previously unselected package subversion.
Preparing to unpack .../subversion_1.9.3-2ubuntu1.1_amd64.deb ...
Unpacking subversion (1.9.3-2ubuntu1.1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up libapr1:amd64 (1.5.2-3) ...
Setting up libaprutil1:amd64 (1.5.4-1build1) ...
Setting up libserf-1-1:amd64 (1.3.8-1) ...
Setting up libsvn1:amd64 (1.9.3-2ubuntu1.1) ...
Setting up subversion (1.9.3-2ubuntu1.1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ... 
```

Enter fullscreen mode Exit fullscreen mode

### 链接

*   [https://help.ubuntu.com/community/Subversion](https://help.ubuntu.com/community/Subversion)