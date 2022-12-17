# sudo add-apt-repository PPA:krit alime/PPA

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppakritalimeppa-3aj>

### 添加 ppa

```
➜  ~ sudo add-apt-repository ppa:kritalime/ppa
[sudo] password for patrick: 
You are about to add the following PPA:
 ----------------------------------------------------------------------------
Krita Lime PPA                                              http://krita.org
---------------------------------------------------------------------------------

Welcome to Krita Lime PPA!

This PPA delivers the freshest Krita packages for Ubuntu, directly picked
by the developers from the source tree.

If you have Ubuntu 16.10 or newer, please use Krita from this repository, this is an officially recommended way alongside AppImage builds.

To install Krita just type:

sudo add-apt-repository ppa:kritalime/ppa
sudo apt-get update
sudo apt-get install krita

If you also want to install translations:

sudo apt-get install krita-l10n

Note: older versions of Ubuntu (including 16.04) are not supported by this repository due to outdated version of Qt library provided. Please use AppImage builds instead [0]

[0] - https://krita.org/en/download/krita-desktop/
 More info: https://launchpad.net/~kritalime/+archive/ubuntu/ppa
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.Q41kJGiaNH/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
01CEE17F
gpg: requesting key 01CEE17F from hkp server keyserver.ubuntu.com
gpg: key 01CEE17F: public key "Launchpad PPA for Krita Lime (*experimental*)" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

### 更新

```
➜  ~ sudo apt-get update
Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Hit:2 http://archive.ubuntu.com/ubuntu xenial InRelease                                             
Get:3 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease [18,0 kB]                      
Ign:4 http://ftp-stud.hs-esslingen.de/pub/Mirrors/packages.linuxmint.com serena InRelease           
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                            
Hit:6 http://ftp-stud.hs-esslingen.de/pub/Mirrors/packages.linuxmint.com serena Release             
Hit:7 http://archive.canonical.com/ubuntu xenial InRelease                                          
Get:9 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial InRelease [24,3 kB]                      
Get:10 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                
Hit:11 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease                       
Get:12 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [356 kB]               
Hit:13 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial InRelease                           
Get:14 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main Sources [2.956 B]                  
Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [632 kB]                 
Get:16 http://security.ubuntu.com/ubuntu xenial-security/main i386 Packages [332 kB]                
Get:17 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main amd64 Packages [4.844 B]           
Get:18 http://security.ubuntu.com/ubuntu xenial-security/main Translation-en [158 kB]               
Get:19 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial/main i386 Packages [4.828 B]            
Get:20 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial/main Sources [608 B]                    
Get:21 http://security.ubuntu.com/ubuntu xenial-security/restricted amd64 Packages [7.352 B]        
Get:22 http://security.ubuntu.com/ubuntu xenial-security/restricted i386 Packages [7.364 B]         
Get:23 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [170 kB]           
Get:24 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial/main amd64 Packages [496 B]             
Get:25 http://archive.ubuntu.com/ubuntu xenial-updates/main i386 Packages [604 kB]                  
Get:26 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial/main i386 Packages [496 B]              
Get:27 http://security.ubuntu.com/ubuntu xenial-security/universe i386 Packages [147 kB]            
Get:28 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial/main Translation-en [352 B]             
Get:29 http://security.ubuntu.com/ubuntu xenial-security/universe Translation-en [89,8 kB]          
Get:30 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [2.760 B]      
Get:31 http://security.ubuntu.com/ubuntu xenial-security/multiverse i386 Packages [2.916 B]        
Get:32 http://archive.ubuntu.com/ubuntu xenial-updates/main Translation-en [265 kB]                
Get:33 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [7.972 B]
Get:34 http://archive.ubuntu.com/ubuntu xenial-updates/restricted i386 Packages [7.988 B]
Get:35 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [537 kB]
Get:36 http://archive.ubuntu.com/ubuntu xenial-updates/universe i386 Packages [513 kB]
Get:37 http://archive.ubuntu.com/ubuntu xenial-updates/universe Translation-en [218 kB]
Get:38 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [15,3 kB]
Get:39 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse i386 Packages [14,5 kB]
Get:40 http://archive.ubuntu.com/ubuntu xenial-backports/main amd64 Packages [4.836 B]
Get:41 http://archive.ubuntu.com/ubuntu xenial-backports/main i386 Packages [4.840 B]
Get:42 http://archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [5.896 B]
Get:43 http://archive.ubuntu.com/ubuntu xenial-backports/universe i386 Packages [5.896 B]
Get:44 http://archive.ubuntu.com/ubuntu xenial-backports/universe Translation-en [3.060 B]
Fetched 4.475 kB in 5s (842 kB/s)                                
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

### 安装

```
➜  ~ sudo apt-get install krita
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  krita-data libgsl2 libopencolorio1v5 libtinyxml2.6.2v5 libyaml-cpp0.3v5
Suggested packages:
  krita-l10n gsl-ref-psdoc | gsl-doc-pdf | gsl-doc-info | gsl-ref-html
The following NEW packages will be installed:
  krita krita-data libgsl2 libopencolorio1v5 libtinyxml2.6.2v5 libyaml-cpp0.3v5
0 upgraded, 6 newly installed, 0 to remove and 70 not upgraded.
Need to get 30,6 MB of archives.
After this operation, 101 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgsl2 amd64 2.1+dfsg-2 [840 kB]
Get:2 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial/main amd64 krita-data all 1:3.2.1+dfsg-1~ubuntu16.04~ppa1 [17,4 MB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/main amd64 libtinyxml2.6.2v5 amd64 2.6.2-3 [29,7 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libyaml-cpp0.3v5 amd64 0.3.0-1.2 [144 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libopencolorio1v5 amd64 1.0.9~dfsg0-4 [310 kB]
Get:6 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial/main amd64 krita amd64 1:3.2.1+dfsg-1~ubuntu16.04~ppa1 [11,9 MB]
Fetched 30,6 MB in 1min 39s (307 kB/s)                                                              
Selecting previously unselected package krita-data.
(Reading database ... 318011 files and directories currently installed.)
Preparing to unpack .../krita-data_1%3a3.2.1+dfsg-1~ubuntu16.04~ppa1_all.deb ...
Unpacking krita-data (1:3.2.1+dfsg-1~ubuntu16.04~ppa1) ...
Selecting previously unselected package libgsl2:amd64.
Preparing to unpack .../libgsl2_2.1+dfsg-2_amd64.deb ...
Unpacking libgsl2:amd64 (2.1+dfsg-2) ...
Selecting previously unselected package libtinyxml2.6.2v5:amd64.
Preparing to unpack .../libtinyxml2.6.2v5_2.6.2-3_amd64.deb ...
Unpacking libtinyxml2.6.2v5:amd64 (2.6.2-3) ...
Selecting previously unselected package libyaml-cpp0.3v5:amd64.
Preparing to unpack .../libyaml-cpp0.3v5_0.3.0-1.2_amd64.deb ...
Unpacking libyaml-cpp0.3v5:amd64 (0.3.0-1.2) ...
Selecting previously unselected package libopencolorio1v5.
Preparing to unpack .../libopencolorio1v5_1.0.9~dfsg0-4_amd64.deb ...
Unpacking libopencolorio1v5 (1.0.9~dfsg0-4) ...
Selecting previously unselected package krita.
Preparing to unpack .../krita_1%3a3.2.1+dfsg-1~ubuntu16.04~ppa1_amd64.deb ...
Unpacking krita (1:3.2.1+dfsg-1~ubuntu16.04~ppa1) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for desktop-file-utils (0.22-1ubuntu5.1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Setting up krita-data (1:3.2.1+dfsg-1~ubuntu16.04~ppa1) ...
Setting up libgsl2:amd64 (2.1+dfsg-2) ...
Setting up libtinyxml2.6.2v5:amd64 (2.6.2-3) ...
Setting up libyaml-cpp0.3v5:amd64 (0.3.0-1.2) ...
Setting up libopencolorio1v5 (1.0.9~dfsg0-4) ...
Setting up krita (1:3.2.1+dfsg-1~ubuntu16.04~ppa1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode