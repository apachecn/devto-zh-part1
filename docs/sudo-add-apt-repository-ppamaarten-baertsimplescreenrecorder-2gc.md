# sudo add-apt-repository PPA:Maarten-baert/simplescreenrecorder

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppamaarten-baertsimplescreenrecorder-2gc>

```
 #
# Add PPA
#

➜  ~ sudo add-apt-repository ppa:maarten-baert/simplescreenrecorder
[sudo] password for psnc: 
You are about to add the following PPA:
 SimpleScreenRecorder is a feature-rich screen recorder that supports X11 and OpenGL. It has a Qt-based graphical user interface. It can record the entire screen or part of it, or record OpenGL applications directly. The recording can be paused and resumed at any time. Many different file formats and codecs are supported.

More information can be found here:
http://www.maartenbaert.be/simplescreenrecorder/
 More info: https://launchpad.net/~maarten-baert/+archive/ubuntu/simplescreenrecorder
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.lnGYAR6Evq/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
283EC8CD
gpg: requesting key 283EC8CD from hkp server keyserver.ubuntu.com
gpg: key 283EC8CD: public key "Launchpad PPA for Maarten Baert" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

```
 #
# Update Sources
#

➜  ~ sudo apt-get update                                           
Ign:1 http://ftp.fau.de/mint/packages sonya InRelease
Hit:2 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial InRelease                                                 
Get:3 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]                                                                                                 
Hit:4 http://ftp.fau.de/mint/packages sonya Release                                                                                                                        
Hit:5 http://archive.canonical.com/ubuntu xenial InRelease                                                                                                                 
Hit:6 http://archive.ubuntu.com/ubuntu xenial InRelease                                                                                                                    
Get:7 http://ppa.launchpad.net/byobu/ppa/ubuntu xenial InRelease [17,5 kB]                                                                                         
Get:8 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                                                                                
Ign:10 http://dl.google.com/linux/chrome/deb stable InRelease                                                                                         
Hit:11 http://dl.google.com/linux/chrome/deb stable Release                                                                                                           
Get:12 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                                                         
Hit:14 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease                
Hit:15 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                            
Hit:16 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease          
Get:17 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial InRelease [17,6 kB]
Hit:18 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease  
Get:19 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial/main Sources [784 B]
Get:20 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial/main amd64 Packages [904 B]
Get:21 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial/main i386 Packages [904 B]
Get:22 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial/main Translation-en [460 B]
Fetched 345 kB in 1s (185 kB/s)                      
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

```
 #
# Installation
#

➜  ~ sudo apt-get install simplescreenrecorder
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Recommended packages:
  simplescreenrecorder-lib
The following NEW packages will be installed:
  simplescreenrecorder
0 upgraded, 1 newly installed, 0 to remove and 6 not upgraded.
Need to get 1.224 kB of archives.
After this operation, 3.060 kB of additional disk space will be used.
Get:1 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial/main amd64 simplescreenrecorder amd64 0.3.8+1~ppa1~xenial1 [1.224 kB]
Fetched 1.224 kB in 4s (290 kB/s)                 
Selecting previously unselected package simplescreenrecorder.
(Reading database ... 255527 files and directories currently installed.)
Preparing to unpack .../simplescreenrecorder_0.3.8+1~ppa1~xenial1_amd64.deb ...
Unpacking simplescreenrecorder (0.3.8+1~ppa1~xenial1) ...
Processing triggers for desktop-file-utils (0.22+linuxmint1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up simplescreenrecorder (0.3.8+1~ppa1~xenial1) ... 
```

Enter fullscreen mode Exit fullscreen mode