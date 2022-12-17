# sudo add-apt 储存区域 PPA:Otto-kesh/gimp-edge

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppaotto-kesselgulaschgimp-edge-df2>

### 添加 ppa

```
➜  ~ sudo add-apt-repository ppa:otto-kesselgulasch/gimp-edge  
[sudo] password for patrick: 
You are about to add the following PPA:
 Attention!

This is a PPA only for development purposes!!!

There'll be no new packages for obsolete Ubuntu versions anymore and will not accept new uploads:
https://wiki.ubuntu.com/Releases
Sorry but that's the way live goes.

Are you running Trusty? If so be aware I'm not be able build gegl packages since some weeks.
The reason is: gegl from git needs glib with a version which is too new for trusty. Glib is a very important part of Ubuntu 14.04 so I'cant guarantee the integrity of Ubuntu Trusty - 14.04.
I hope in the next weeks I can build a Snap version.

These packages were built of the development branch of the GIMP. They may be very unstable, and as such should not be used in production. 

To add this PPA open a terminal and type

sudo add-apt-repository ppa:otto-kesselgulasch/gimp-edge
sudo apt-get update && sudo apt-get upgrade

or

sudo apt-get update && sudo apt-get install gimp gimp-gmic e.g.

To revert to stable open a terminal and type
sudo ppa-purge ppa:otto-kesselgulasch/gimp-edge

Before you install ppa-purge
sudo apt-get install ppa-purge

 More info: https://launchpad.net/~otto-kesselgulasch/+archive/ubuntu/gimp-edge
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.qhqRDD2GIn/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
614C4B38
gpg: requesting key 614C4B38 from hkp server keyserver.ubuntu.com
gpg: key 614C4B38: public key "Launchpad otto06217" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

### 更新

```
➜  ~ sudo apt-get update
Ign:1 http://ftp-stud.hs-esslingen.de/pub/Mirrors/packages.linuxmint.com serena InRelease           
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]                          
Hit:3 http://archive.ubuntu.com/ubuntu xenial InRelease                                             
Hit:4 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                
Hit:5 http://archive.canonical.com/ubuntu xenial InRelease                                          
Hit:6 http://ftp-stud.hs-esslingen.de/pub/Mirrors/packages.linuxmint.com serena Release             
Get:7 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                            
Hit:8 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                
Get:9 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                          
Hit:10 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial InRelease              
Get:11 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial InRelease [17,5 kB]      
Hit:13 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease
Hit:14 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial InRelease    
Get:15 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main Sources [6.852 B]
Get:16 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 Packages [11,6 kB]
Get:17 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main i386 Packages [11,6 kB]
Get:18 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main Translation-en [8.244 B]
Fetched 362 kB in 3s (104 kB/s)                     
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

### 安装 Gimp

```
➜  ~ sudo apt-get install gimp
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  gimp-data libbabl-0.1-0 libgegl-0.3-0 libgexiv2-2 libgimp2.0 liblcms2-2 liblcms2-2:i386
  libmypaint libwebp6 libwebpdemux2 libwebpmux3
Suggested packages:
  gimp-data-extras
The following NEW packages will be installed:
  libgexiv2-2 libmypaint libwebp6 libwebpdemux2 libwebpmux3
The following packages will be upgraded:
  gimp gimp-data libbabl-0.1-0 libgegl-0.3-0 libgimp2.0 liblcms2-2 liblcms2-2:i386
7 upgraded, 5 newly installed, 0 to remove and 11 not upgraded.
Need to get 15,5 MB of archives.
After this operation, 5.615 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgexiv2-2 amd64 0.10.3-2 [41,7 kB]
Get:2 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main i386 liblcms2-2 i386 2.8-ubuntu16.04.1~ppa [151 kB]
Get:3 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 liblcms2-2 amd64 2.8-ubuntu16.04.1~ppa [140 kB]
Get:4 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 libbabl-0.1-0 amd64 0.1.35~3-u16.04.1~ppa~541300d [230 kB]
Get:5 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 libwebp6 amd64 0.6.0-3x0~ppa [184 kB]
Get:6 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 libgegl-0.3-0 amd64 1:0.3.21~13-u16.04.2~ppa~b3647fc [779 kB]
Get:7 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 gimp amd64 2.9.7~13-u16.04.1~ppa~d4d8dbb [3.666 kB]
Get:8 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 libgimp2.0 amd64 2.9.7~13-u16.04.1~ppa~d4d8dbb [778 kB]
Get:9 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 gimp-data all 2.9.7~13-u16.04.1~ppa~d4d8dbb [9.473 kB]
Get:10 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 libmypaint amd64 1.4.0~7-u16.04.1~ppa~c196a86 [33,9 kB]
Get:11 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 libwebpdemux2 amd64 0.6.0-3x0~ppa [11,9 kB]
Get:12 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial/main amd64 libwebpmux3 amd64 0.6.0-3x0~ppa [22,4 kB]
Fetched 15,5 MB in 12s (1.267 kB/s)                                                                 
(Reading database ... 353969 files and directories currently installed.)
Preparing to unpack .../liblcms2-2_2.8-ubuntu16.04.1~ppa_amd64.deb ...
De-configuring liblcms2-2:i386 (2.6-3ubuntu2) ...
Unpacking liblcms2-2:amd64 (2.8-ubuntu16.04.1~ppa) over (2.6-3ubuntu2) ...
Preparing to unpack .../liblcms2-2_2.8-ubuntu16.04.1~ppa_i386.deb ...
Unpacking liblcms2-2:i386 (2.8-ubuntu16.04.1~ppa) over (2.6-3ubuntu2) ...
Preparing to unpack .../libbabl-0.1-0_0.1.35~3-u16.04.1~ppa~541300d_amd64.deb ...
Unpacking libbabl-0.1-0:amd64 (0.1.35~3-u16.04.1~ppa~541300d) over (0.1.16-1) ...
Selecting previously unselected package libwebp6:amd64.
Preparing to unpack .../libwebp6_0.6.0-3x0~ppa_amd64.deb ...
Unpacking libwebp6:amd64 (0.6.0-3x0~ppa) ...
Preparing to unpack .../libgegl-0.3-0_1%3a0.3.21~13-u16.04.2~ppa~b3647fc_amd64.deb ...
Unpacking libgegl-0.3-0:amd64 (1:0.3.21~13-u16.04.2~ppa~b3647fc) over (0.3.4-1ubuntu2) ...
Selecting previously unselected package libgexiv2-2:amd64.
Preparing to unpack .../libgexiv2-2_0.10.3-2_amd64.deb ...
Unpacking libgexiv2-2:amd64 (0.10.3-2) ...
Preparing to unpack .../gimp_2.9.7~13-u16.04.1~ppa~d4d8dbb_amd64.deb ...
Unpacking gimp (2.9.7~13-u16.04.1~ppa~d4d8dbb) over (2.8.16-1ubuntu1.1) ...
Preparing to unpack .../libgimp2.0_2.9.7~13-u16.04.1~ppa~d4d8dbb_amd64.deb ...
Unpacking libgimp2.0 (2.9.7~13-u16.04.1~ppa~d4d8dbb) over (2.8.16-1ubuntu1.1) ...
Preparing to unpack .../gimp-data_2.9.7~13-u16.04.1~ppa~d4d8dbb_all.deb ...
Unpacking gimp-data (2.9.7~13-u16.04.1~ppa~d4d8dbb) over (2.8.16-1ubuntu1.1) ...
Selecting previously unselected package libmypaint.
Preparing to unpack .../libmypaint_1.4.0~7-u16.04.1~ppa~c196a86_amd64.deb ...
Unpacking libmypaint (1.4.0~7-u16.04.1~ppa~c196a86) ...
Selecting previously unselected package libwebpdemux2:amd64.
Preparing to unpack .../libwebpdemux2_0.6.0-3x0~ppa_amd64.deb ...
Unpacking libwebpdemux2:amd64 (0.6.0-3x0~ppa) ...
Selecting previously unselected package libwebpmux3:amd64.
Preparing to unpack .../libwebpmux3_0.6.0-3x0~ppa_amd64.deb ...
Unpacking libwebpmux3:amd64 (0.6.0-3x0~ppa) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for desktop-file-utils (0.22-1ubuntu5.1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for doc-base (0.10.7) ...
Processing 1 added doc-base file...
Error in `/usr/share/doc-base/libmypaint', line 20: all `Format' sections are invalid.
Note: `install-docs --verbose --check file_name' may give more details about the above error.
Registering documents with scrollkeeper...
Setting up liblcms2-2:i386 (2.8-ubuntu16.04.1~ppa) ...
Setting up liblcms2-2:amd64 (2.8-ubuntu16.04.1~ppa) ...
Setting up libbabl-0.1-0:amd64 (0.1.35~3-u16.04.1~ppa~541300d) ...
Setting up libwebp6:amd64 (0.6.0-3x0~ppa) ...
Setting up libgegl-0.3-0:amd64 (1:0.3.21~13-u16.04.2~ppa~b3647fc) ...
Setting up libgexiv2-2:amd64 (0.10.3-2) ...
Setting up libgimp2.0 (2.9.7~13-u16.04.1~ppa~d4d8dbb) ...
Setting up gimp-data (2.9.7~13-u16.04.1~ppa~d4d8dbb) ...
Installing new version of config file /etc/gimp/2.0/controllerrc ...
Installing new version of config file /etc/gimp/2.0/gimprc ...
Installing new version of config file /etc/gimp/2.0/sessionrc ...
Installing new version of config file /etc/gimp/2.0/templaterc ...
Setting up libmypaint (1.4.0~7-u16.04.1~ppa~c196a86) ...
Setting up libwebpdemux2:amd64 (0.6.0-3x0~ppa) ...
Setting up libwebpmux3:amd64 (0.6.0-3x0~ppa) ...
Setting up gimp (2.9.7~13-u16.04.1~ppa~d4d8dbb) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode

### 图片

*   [https://thepractical dev . S3 . Amazon AWS . com/I/fhye 79 hdo 5 qw 985 wo 4 rk . png](https://thepracticaldev.s3.amazonaws.com/i/fhye79hdo5qw985wo4rk.png)