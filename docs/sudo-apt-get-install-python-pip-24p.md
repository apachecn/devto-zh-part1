# sudo apt-get 安装 python-pip

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-python-pip-24p>

### 安装

```
➜  ~ sudo apt-get install python-pip
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libexpat1-dev libpython-all-dev libpython-dev libpython2.7 libpython2.7-dev python-all python-all-dev
  python-dev python-pip-whl python-pkg-resources python-setuptools python-wheel python2.7-dev
Suggested packages:
  python-setuptools-doc
The following NEW packages will be installed:
  libexpat1-dev libpython-all-dev libpython-dev libpython2.7 libpython2.7-dev python-all python-all-dev
  python-dev python-pip python-pip-whl python-pkg-resources python-setuptools python-wheel
  python2.7-dev
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 30.9 MB of archives.
After this operation, 48.8 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libexpat1-dev amd64 2.1.0-7ubuntu0.16.04.3 [115 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libpython2.7 amd64 2.7.12-1ubuntu0~16.04.1 [1,070 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libpython2.7-dev amd64 2.7.12-1ubuntu0~16.04.1 [27.8 MB]
Get:4 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libpython-dev amd64 2.7.11-1 [7,728 B]                                                                                           
Get:5 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libpython-all-dev amd64 2.7.11-1 [992 B]                                                                                         
Get:6 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 python-all amd64 2.7.11-1 [978 B]                                                                                                
Get:7 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 python2.7-dev amd64 2.7.12-1ubuntu0~16.04.1 [276 kB]                                                                     
Get:8 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 python-dev amd64 2.7.11-1 [1,160 B]                                                                                              
Get:9 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 python-all-dev amd64 2.7.11-1 [1,000 B]                                                                                          
Get:10 http://us.archive.ubuntu.com/ubuntu xenial-updates/universe amd64 python-pip-whl all 8.1.1-2ubuntu0.4 [1,110 kB]                                                                      
Get:11 http://us.archive.ubuntu.com/ubuntu xenial-updates/universe amd64 python-pip all 8.1.1-2ubuntu0.4 [144 kB]                                                                            
Get:12 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 python-pkg-resources all 20.7.0-1 [108 kB]                                                                                      
Get:13 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 python-setuptools all 20.7.0-1 [169 kB]                                                                                         
Get:14 http://us.archive.ubuntu.com/ubuntu xenial/universe amd64 python-wheel all 0.29.0-1 [48.0 kB]                                                                                         
Fetched 30.9 MB in 26s (1,158 kB/s)                                                                                                                                                          
Selecting previously unselected package libexpat1-dev:amd64.
(Reading database ... 154018 files and directories currently installed.)
Preparing to unpack .../libexpat1-dev_2.1.0-7ubuntu0.16.04.3_amd64.deb ...
Unpacking libexpat1-dev:amd64 (2.1.0-7ubuntu0.16.04.3) ...
Selecting previously unselected package libpython2.7:amd64.
Preparing to unpack .../libpython2.7_2.7.12-1ubuntu0~16.04.1_amd64.deb ...
Unpacking libpython2.7:amd64 (2.7.12-1ubuntu0~16.04.1) ...
Selecting previously unselected package libpython2.7-dev:amd64.
Preparing to unpack .../libpython2.7-dev_2.7.12-1ubuntu0~16.04.1_amd64.deb ...
Unpacking libpython2.7-dev:amd64 (2.7.12-1ubuntu0~16.04.1) ...
Selecting previously unselected package libpython-dev:amd64.
Preparing to unpack .../libpython-dev_2.7.11-1_amd64.deb ...
Unpacking libpython-dev:amd64 (2.7.11-1) ...
Selecting previously unselected package libpython-all-dev:amd64.
Preparing to unpack .../libpython-all-dev_2.7.11-1_amd64.deb ...
Unpacking libpython-all-dev:amd64 (2.7.11-1) ...
Selecting previously unselected package python-all.
Preparing to unpack .../python-all_2.7.11-1_amd64.deb ...
Unpacking python-all (2.7.11-1) ...
Selecting previously unselected package python2.7-dev.
Preparing to unpack .../python2.7-dev_2.7.12-1ubuntu0~16.04.1_amd64.deb ...
Unpacking python2.7-dev (2.7.12-1ubuntu0~16.04.1) ...
Selecting previously unselected package python-dev.
Preparing to unpack .../python-dev_2.7.11-1_amd64.deb ...
Unpacking python-dev (2.7.11-1) ...
Selecting previously unselected package python-all-dev.
Preparing to unpack .../python-all-dev_2.7.11-1_amd64.deb ...
Unpacking python-all-dev (2.7.11-1) ...
Selecting previously unselected package python-pip-whl.
Preparing to unpack .../python-pip-whl_8.1.1-2ubuntu0.4_all.deb ...
Unpacking python-pip-whl (8.1.1-2ubuntu0.4) ...
Selecting previously unselected package python-pip.
Preparing to unpack .../python-pip_8.1.1-2ubuntu0.4_all.deb ...
Unpacking python-pip (8.1.1-2ubuntu0.4) ...
Selecting previously unselected package python-pkg-resources.
Preparing to unpack .../python-pkg-resources_20.7.0-1_all.deb ...
Unpacking python-pkg-resources (20.7.0-1) ...
Selecting previously unselected package python-setuptools.
Preparing to unpack .../python-setuptools_20.7.0-1_all.deb ...
Unpacking python-setuptools (20.7.0-1) ...
Selecting previously unselected package python-wheel.
Preparing to unpack .../python-wheel_0.29.0-1_all.deb ...
Unpacking python-wheel (0.29.0-1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up libexpat1-dev:amd64 (2.1.0-7ubuntu0.16.04.3) ...
Setting up libpython2.7:amd64 (2.7.12-1ubuntu0~16.04.1) ...
Setting up libpython2.7-dev:amd64 (2.7.12-1ubuntu0~16.04.1) ...
Setting up libpython-dev:amd64 (2.7.11-1) ...
Setting up libpython-all-dev:amd64 (2.7.11-1) ...
Setting up python-all (2.7.11-1) ...
Setting up python2.7-dev (2.7.12-1ubuntu0~16.04.1) ...
Setting up python-dev (2.7.11-1) ...
Setting up python-all-dev (2.7.11-1) ...
Setting up python-pip-whl (8.1.1-2ubuntu0.4) ...
Setting up python-pip (8.1.1-2ubuntu0.4) ...
Setting up python-pkg-resources (20.7.0-1) ...
Setting up python-setuptools (20.7.0-1) ...
Setting up python-wheel (0.29.0-1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ... 
```

Enter fullscreen mode Exit fullscreen mode

### Pip 版本

```
➜  ~ pip -V
pip 8.1.1 from /usr/lib/python2.7/dist-packages (python 2.7) 
```

Enter fullscreen mode Exit fullscreen mode

### 皮普帮助

```
➜  ~ pip -h

Usage:   
  pip <command> [options]

Commands:
  install Install packages.
  download                    Download packages.
  uninstall                   Uninstall packages.
  freeze                      Output installed packages in requirements format.
  list                        List installed packages.
  show                        Show information about installed packages.
  search                      Search PyPI for packages.
  wheel                       Build wheels from your requirements.
  hash Compute hashes of package archives.
  completion                  A helper command used for command completion
  help Show help for commands.

General Options:
  -h, --help                  Show help.
  --isolated                  Run pip in an isolated mode, ignoring environment variables and user configuration.
  -v, --verbose               Give more output. Option is additive, and can be used up to 3 times.
  -V, --version               Show version and exit.
  -q, --quiet                 Give less output.
  --log <path>                Path to a verbose appending log.
  --proxy <proxy>             Specify a proxy in the form [user:passwd@]proxy.server:port.
  --retries <retries>         Maximum number of retries each connection should attempt (default 5 times).
  --timeout <sec>             Set the socket timeout (default 15 seconds).
  --exists-action <action>    Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup.
  --trusted-host <hostname>   Mark this host as trusted, even though it does not have valid or any HTTPS.
  --cert <path>               Path to alternate CA bundle.
  --client-cert <path>        Path to SSL client certificate, a single file containing the private key and the certificate in PEM format.
  --cache-dir <dir>           Store the cache data in <dir>.
  --no-cache-dir              Disable the cache.
  --disable-pip-version-check
                              Don't periodically check PyPI to determine whether a new version of pip is available for download. Implied with --no-index. 
```

Enter fullscreen mode Exit fullscreen mode