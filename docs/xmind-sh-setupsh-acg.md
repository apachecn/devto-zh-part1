# xmind sh setup.sh

> 原文：<https://dev.to/psnebc/xmind-sh-setupsh-acg>

```
➜  /opt  cd /opt/xmind 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  xmind sh setup.sh 
[setup] Installing dependencies....
E: Could not open lock file /var/lib/dpkg/lock - open (13: Permission denied)
E: Unable to lock the administration directory (/var/lib/dpkg/), are you root?
➜  xmind sudo sh setup.sh
[sudo] password for psnc: 
[setup] Installing dependencies....
Reading package lists... Done
Building dependency tree       
Reading state information... Done
libc6 is already the newest version (2.23-0ubuntu9).
libglib2.0-0 is already the newest version (2.48.2-0ubuntu1).
libgtk2.0-0 is already the newest version (2.24.30-1ubuntu1.16.04.2).
libwebkitgtk-1.0-0 is already the newest version (2.4.11-0ubuntu0.1).
The following additional packages will be installed:
  default-jre-headless
Suggested packages:
  default-java-plugin
The following NEW packages will be installed:
  default-jre default-jre-headless lame
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 51,1 kB of archives.
After this operation, 151 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 default-jre-headless amd64 2:1.8-56ubuntu2 [4.380 B]
Get:2 http://archive.ubuntu.com/ubuntu xenial/main amd64 default-jre amd64 2:1.8-56ubuntu2 [980 B]
Get:3 http://archive.ubuntu.com/ubuntu xenial/universe amd64 lame amd64 3.99.5+repack1-9build1 [45,7 kB]
Fetched 51,1 kB in 0s (232 kB/s)
Selecting previously unselected package default-jre-headless.
(Reading database ... 337079 files and directories currently installed.)
Preparing to unpack .../default-jre-headless_2%3a1.8-56ubuntu2_amd64.deb ...
Unpacking default-jre-headless (2:1.8-56ubuntu2) ...
Selecting previously unselected package default-jre.
Preparing to unpack .../default-jre_2%3a1.8-56ubuntu2_amd64.deb ...
Unpacking default-jre (2:1.8-56ubuntu2) ...
Selecting previously unselected package lame.
Preparing to unpack .../lame_3.99.5+repack1-9build1_amd64.deb ...
Unpacking lame (3.99.5+repack1-9build1) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up default-jre-headless (2:1.8-56ubuntu2) ...
Setting up default-jre (2:1.8-56ubuntu2) ...
Setting up lame (3.99.5+repack1-9build1) ...
[setup] Installing custom fonts....
sending incremental file list
./
Arvo-Bold.ttf
Arvo-BoldItalic.ttf
Arvo-Italic.ttf
Arvo-Regular.ttf
DroidSerif-Bold.ttf
DroidSerif-BoldItalic.ttf
DroidSerif-Italic.ttf
DroidSerif.ttf
IndieFlower.ttf
Lobster-Regular.ttf
OpenSans-Bold.ttf
OpenSans-BoldItalic.ttf
OpenSans-Italic.ttf
OpenSans-Regular.ttf
PoiretOne-Regular.ttf
Raleway-Bold.ttf
Raleway-Regular.ttf
Roboto-Bold.ttf
Roboto-BoldItalic.ttf
Roboto-Italic.ttf
Roboto-Regular.ttf
RobotoCondensed-Bold.ttf
RobotoCondensed-BoldItalic.ttf
RobotoCondensed-Italic.ttf
RobotoCondensed-Regular.ttf
RobotoSlab-Bold.ttf
RobotoSlab-Regular.ttf

sent 3,656,744 bytes  received 532 bytes  7,314,552.00 bytes/sec
total size is 3,654,132  speedup is 1.00
[setup] Done. 
```

Enter fullscreen mode Exit fullscreen mode

*   [http://www.xmind.net](http://www.xmind.net)