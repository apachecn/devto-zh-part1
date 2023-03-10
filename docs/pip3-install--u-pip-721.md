# pip3 安装-U pip

> 原文：<https://dev.to/psnebc/pip3-install--u-pip-721>

### 2017 年 11 月 6 日安装注意事项

```
➜  ~ pip3 install -U pip
Collecting pip
  Using cached pip-9.0.1-py2.py3-none-any.whl
Installing collected packages: pip
Successfully installed pip-9.0.1 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip2 install -U pip 
Collecting pip
  Using cached pip-9.0.1-py2.py3-none-any.whl
Installing collected packages: pip
Successfully installed pip-8.1.1
You are using pip version 8.1.1, however version 9.0.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip3 install --upgrade pip                                                             
Requirement already up-to-date: pip in ./.local/lib/python3.5/site-packages 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip install --upgrade pip 
Requirement already up-to-date: pip in ./.local/lib/python2.7/site-packages 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip2 install --upgrade pip
Collecting pip
  Using cached pip-9.0.1-py2.py3-none-any.whl
Installing collected packages: pip
Successfully installed pip-8.1.1
You are using pip version 8.1.1, however version 9.0.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip2 install --upgrade pip
Collecting pip
  Using cached pip-9.0.1-py2.py3-none-any.whl
Installing collected packages: pip
Successfully installed pip-8.1.1
You are using pip version 8.1.1, however version 9.0.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip install --upgrade pip 
Requirement already up-to-date: pip in ./.local/lib/python2.7/site-packages 
```

Enter fullscreen mode Exit fullscreen mode

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
  check                       Verify installed packages have compatible dependencies.
  search                      Search PyPI for packages.
  wheel                       Build wheels from your requirements.
  hash Compute hashes of package archives.
  completion                  A helper command used for command completion.
  help Show help for commands.

General Options:
  -h, --help                  Show help.
  --isolated                  Run pip in an isolated mode, ignoring environment variables and user configuration.
  -v, --verbose               Give more output. Option is additive, and can be used up to 3 times.
  -V, --version               Show version and exit.
  -q, --quiet                 Give less output. Option is additive, and can be used up to 3 times (corresponding to WARNING, ERROR, and CRITICAL logging levels).
  --log <path>                Path to a verbose appending log.
  --proxy <proxy>             Specify a proxy in the form [user:passwd@]proxy.server:port.
  --retries <retries>         Maximum number of retries each connection should attempt (default 5 times).
  --timeout <sec>             Set the socket timeout (default 15 seconds).
  --exists-action <action>    Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup, (a)bort.
  --trusted-host <hostname>   Mark this host as trusted, even though it does not have valid or any HTTPS.
  --cert <path>               Path to alternate CA bundle.
  --client-cert <path>        Path to SSL client certificate, a single file containing the private key and the certificate in PEM format.
  --cache-dir <dir>           Store the cache data in <dir>.
  --no-cache-dir              Disable the cache.
 V --disable-pip-version-check
                              Don't periodically check PyPI to determine whether a new version of pip is available for download. Implied with --no-index. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip -V 
pip 9.0.1 from /home/psnc/.local/lib/python2.7/site-packages (python 2.7) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip3 -V
pip 9.0.1 from /home/psnc/.local/lib/python3.5/site-packages (python 3.5) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip3 list
DEPRECATION: The default format will switch to columns in the future. You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
apt-clone (0.2.1)
apt-xapian-index (0.47)
apturl (0.5.2)
asciinema (1.4.0)
blinker (1.3)
Brlapi (0.6.4)
chardet (2.3.0)
command-not-found (0.3)
cryptography (1.2.3)
defer (1.0.6)
dirspec (13.10)
httplib2 (0.9.1)
idna (2.0)
louis (2.6.4)
lxml (3.5.0)
oauthlib (1.0.3)
oneconf (0.3.9)
pexpect (4.0.1)
Pillow (3.1.2)
pip (9.0.1)
piston-mini-client (0.7.5)
psutil (3.4.2)
ptyprocess (0.5)
pyasn1 (0.1.9)
pycrypto (2.6.1)
pycups (1.9.73)
pycurl (7.43.0)
pygobject (3.20.0)
PyICU (1.9.2)
PyJWT (1.3.0)
python-apt (1.1.0b1)
python-debian (0.1.27)
python-xapp (1.0.0)
pyxdg (0.25)
reportlab (3.3.0)
requests (2.9.1)
sessioninstaller (0.0.0)
setproctitle (1.1.8)
six (1.10.0)
system-service (0.3)
thin-client-config-agent (0.8)
ubuntu-drivers-common (0.0.0)
ufw (0.35)
urllib3 (1.13.1)
virtkey (0.63.0)
virtualenv (15.1.0)
xkit (0.0.0) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ pip2 list 
ansible (2.4.1.0)
attrs (15.2.0)
BeautifulSoup (3.2.1)
blinker (1.3)
chardet (2.3.0)
configglue (1.1.2)
configobj (5.0.6)
cryptography (1.2.3)
debtags (2.0)
defer (1.0.6)
dirspec (13.10)
duplicity (0.7.6)
ecdsa (0.13)
enum34 (1.1.2)
feedparser (5.1.3)
httplib2 (0.9.1)
idna (2.0)
ipaddress (1.0.16)
Jinja2 (2.8)
lockfile (0.12.2)
lxml (3.5.0)
Mako (1.0.3)
MarkupSafe (0.23)
netifaces (0.10.4)
oauthlib (1.0.3)
oneconf (0.3.9)
PAM (0.4.2)
paramiko (1.16.0)
pexpect (4.0.1)
Pillow (3.1.2)
pip (8.1.1)
piston-mini-client (0.7.5)
protobuf (2.6.1)
ptyprocess (0.5)
pyasn1 (0.1.9)
pyasn1-modules (0.0.7)
pycrypto (2.6.1)
pycups (1.9.73)
pycurl (7.43.0)
pygobject (3.20.0)
pyinotify (0.9.6)
PyJWT (1.3.0)
pyOpenSSL (0.15.1)
pyparted (3.10.7)
pyserial (3.0.1)
pysmbc (1.0.15.5)
python-apt (1.1.0b1)
python-debian (0.1.27)
pyxdg (0.25)
PyYAML (3.11)
reportlab (3.3.0)
service-identity (16.0.0)
setuptools (20.7.0)
six (1.10.0)
Twisted (16.0.0)
uTidylib (0.2)
vboxapi (1.0)
virtualenv (15.1.0)
zope.interface (4.1.3) 
```

Enter fullscreen mode Exit fullscreen mode

```
pip install -U pip setuptools
[sudo] password for psnc: 
The directory '/home/psnc/.cache/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
The directory '/home/psnc/.cache/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Requirement already up-to-date: pip in /home/psnc/.local/lib/python2.7/site-packages
Collecting setuptools
  Downloading setuptools-36.6.0-py2.py3-none-any.whl (481kB)
    100% |████████████████████████████████| 481kB 1.7MB/s 
Installing collected packages: setuptools
  Found existing installation: setuptools 20.7.0
    Uninstalling setuptools-20.7.0:
      Successfully uninstalled setuptools-20.7.0
Successfully installed setuptools-36.6.0 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  sudo pip3 install -U pip setuptools
The directory '/home/psnc/.cache/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
The directory '/home/psnc/.cache/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Requirement already up-to-date: pip in /home/psnc/.local/lib/python3.5/site-packages
Collecting setuptools
  Downloading setuptools-36.6.0-py2.py3-none-any.whl (481kB)
    100% |████████████████████████████████| 481kB 1.4MB/s 
Installing collected packages: setuptools
Successfully installed setuptools-36.6.0 
```

Enter fullscreen mode Exit fullscreen mode