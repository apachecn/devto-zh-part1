# sudo apt-get 安装 python-软件-属性

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-python-software-properties-8c0>

```
➜  ~ sudo apt-get install python-software-properties 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  liblua5.1-0 libvpx3
Use 'sudo apt autoremove' to remove them.
The following additional packages will be installed:
  python-pycurl
Suggested packages:
  libcurl4-gnutls-dev python-pycurl-dbg python-pycurl-doc
The following NEW packages will be installed:
  python-pycurl python-software-properties
0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.
Need to get 64.0 kB of archives.
After this operation, 299 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 python-pycurl amd64 7.43.0-1ubuntu1 [43.3 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu xenial-updates/universe amd64 python-software-properties all 0.96.20.7 [20.7 kB]
Fetched 64.0 kB in 0s (96.3 kB/s)                
Selecting previously unselected package python-pycurl.
(Reading database ... 122145 files and directories currently installed.)
Preparing to unpack .../python-pycurl_7.43.0-1ubuntu1_amd64.deb ...
Unpacking python-pycurl (7.43.0-1ubuntu1) ...
Selecting previously unselected package python-software-properties.
Preparing to unpack .../python-software-properties_0.96.20.7_all.deb ...
Unpacking python-software-properties (0.96.20.7) ...
Setting up python-pycurl (7.43.0-1ubuntu1) ...
Setting up python-software-properties (0.96.20.7) ... 
```

Enter fullscreen mode Exit fullscreen mode