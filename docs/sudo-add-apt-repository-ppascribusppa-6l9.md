# sudo add-apt-repository PPA:scribus/PPA

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppascribusppa-6l9>

```
 ~ sudo add-apt-repository ppa:scribus/ppa 
[sudo] password for psnc: 
You are about to add the following PPA:
 Packages from the Scribus friends

scribus: current 1.4.x branch
scribus-ng: current 1.5.x branch
scribus-trunk: whatever inside the current svn repository, built daily.

scribus-trunk is not updated anymore in 14.04 LTS Trusty Tahr (and 14.10, 15.04 and 15.10) due to a too old Qt, please upgrade your system to a newer Ubuntu release (suggested: 16.04 LTS Xenial Xerus).

SCRIBUS FILES ARE NOT BACKWARDS COMPATIBLE!!
FILES SAVED WITH SCRIBUS-TRUNK OR SCRIBUS-NG CAN'T BE OPENED BY OLDER VERSIONS OF SCRIBUS ANYMORE!!

Back up all your project files before using the svn daily builds as you won't be able to use them with scribus or scribus-ng anymore!

To install the debug symbols you need to add this to your sources.list:
'deb http://ppa.launchpad.net/scribus/ppa/ubuntu xenial main/debug'
 More info: https://launchpad.net/~scribus/+archive/ubuntu/ppa
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.eaPVQAkekI/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
64B6EE15
gpg: requesting key 64B6EE15 from hkp server keyserver.ubuntu.com
gpg: key 64B6EE15: public key "Launchpad PPA for Scribus friends" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get update
Hit:1 http://ppa.launchpad.net/achadwick/mypaint-testing/ubuntu xenial InRelease
Ign:2 http://ftp.fau.de/mint/packages sonya InRelease                                                                                                                      
Hit:3 http://archive.canonical.com/ubuntu xenial InRelease                                                                                                                 
Get:4 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]                                                                                                 
Ign:5 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge InRelease                                                                                          
Hit:6 http://repo.mysql.com/apt/ubuntu xenial InRelease                                                                                                                    
Hit:7 http://ftp.fau.de/mint/packages sonya Release                                                                                                                        
Hit:8 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial InRelease                                                                                                     
Ign:9 http://dl.google.com/linux/chrome/deb stable InRelease                                                                                                               
Get:10 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release [14,9 kB]                                                                                 
Hit:11 http://dl.google.com/linux/chrome/deb stable Release                                                                                                                
Hit:12 http://archive.ubuntu.com/ubuntu xenial InRelease                                                                                                                   
Hit:13 http://ppa.launchpad.net/byobu/ppa/ubuntu xenial InRelease                                                    
Get:14 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release.gpg [173 B]   
Ign:16 http://download.webmin.com/download/repository sarge InRelease                                                                                       
Hit:17 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease                           
Get:18 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                                                                                
Hit:20 http://ppa.launchpad.net/inkscape.dev/stable/ubuntu xenial InRelease                                                                                         
Hit:21 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                                                    
Hit:22 http://ppa.launchpad.net/kdenlive/kdenlive-stable/ubuntu xenial InRelease                                                       
Get:23 http://download.webmin.com/download/repository sarge Release [14,9 kB]                                                          
Ign:14 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release.gpg                                                               
Hit:24 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                                                      
Hit:25 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge/contrib amd64 Packages                                                                            
Hit:26 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial InRelease                                                                                                      
Hit:27 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge/contrib i386 Packages                                                                             
Get:28 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                                                                               
Get:29 http://download.webmin.com/download/repository sarge Release.gpg [173 B]                                            
Hit:30 http://ppa.launchpad.net/libreoffice/ppa/ubuntu xenial InRelease                                                
Hit:31 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial InRelease                       
Hit:32 http://ppa.launchpad.net/nijel/phpmyadmin/ubuntu xenial InRelease                                         
Ign:29 http://download.webmin.com/download/repository sarge Release.gpg                 
Hit:33 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial InRelease                                                         
Hit:34 http://ppa.launchpad.net/ondrej/php/ubuntu xenial InRelease                                                                                        
Get:35 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [652 kB]                                            
Hit:36 http://download.webmin.com/download/repository sarge/contrib amd64 Packages                                                                                  
Hit:37 http://download.webmin.com/download/repository sarge/contrib i386 Packages                                                                                   
Get:38 http://archive.ubuntu.com/ubuntu xenial-updates/main i386 Packages [618 kB]                                                      
Get:39 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [8.076 B]                 
Get:40 http://archive.ubuntu.com/ubuntu xenial-updates/restricted i386 Packages [8.068 B]                     
Get:41 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [544 kB]          
Get:42 http://archive.ubuntu.com/ubuntu xenial-updates/universe i386 Packages [517 kB]                     
Get:43 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [16,2 kB]                  
Get:44 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse i386 Packages [15,3 kB]                     
Hit:45 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial InRelease                          
Hit:46 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial InRelease                  
Get:47 http://ppa.launchpad.net/scribus/ppa/ubuntu xenial InRelease [23,8 kB]                          
Hit:48 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease  
Hit:49 http://ppa.launchpad.net/webupd8team/java/ubuntu xenial InRelease       
Hit:50 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial InRelease      
Get:51 http://ppa.launchpad.net/scribus/ppa/ubuntu xenial/main Sources [1.764 B]
Get:52 http://ppa.launchpad.net/scribus/ppa/ubuntu xenial/main amd64 Packages [4.100 B]
Get:53 http://ppa.launchpad.net/scribus/ppa/ubuntu xenial/main i386 Packages [4.104 B]
Get:54 http://ppa.launchpad.net/scribus/ppa/ubuntu xenial/main Translation-en [1.604 B]
Fetched 2.751 kB in 5s (536 kB/s)                    
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install scribus-ng
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  blt libcoin80v5 libopenscenegraph100v5 libopenthreads20 libpodofo0.9.3 libxine2 libxine2-bin libxine2-ffmpeg libxine2-misc-plugins libxine2-plugins python-tk
  scribus-ng-data tk8.6-blt2.5
Suggested packages:
  blt-demo libopenal0a libsimage-dev gxine xine-ui tix python-tk-dbg icc-profiles scribus-ng-doc scribus-template texlive-latex-recommended
Recommended packages:
  libxine2-doc | libxine-doc icc-profiles-free
The following NEW packages will be installed:
  blt libcoin80v5 libopenscenegraph100v5 libopenthreads20 libpodofo0.9.3 libxine2 libxine2-bin libxine2-ffmpeg libxine2-misc-plugins libxine2-plugins python-tk scribus-ng
  scribus-ng-data tk8.6-blt2.5
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 36,1 MB of archives.
After this operation, 134 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ppa.launchpad.net/scribus/ppa/ubuntu xenial/main amd64 scribus-ng-data all 1.5.3-0ubuntu16.04~ppa0 [16,5 MB]
Get:2 http://archive.ubuntu.com/ubuntu xenial/main amd64 tk8.6-blt2.5 amd64 2.5.3+dfsg-3 [574 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/main amd64 blt amd64 2.5.3+dfsg-3 [4.852 B]
Get:4 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libcoin80v5 amd64 3.1.4~abc9f50+dfsg1-1 [2.396 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libopenthreads20 amd64 3.2.1-7ubuntu4 [12,6 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libxine2-bin amd64 1.2.6-1build5 [1.408 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libxine2-ffmpeg amd64 1.2.6-1build5 [212 kB]                                                                  
Get:8 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libxine2-misc-plugins amd64 1.2.6-1build5 [592 kB]                                                            
Get:9 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libxine2-plugins all 1.2.6-1build5 [3.250 B]                                                                  
Get:10 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libxine2 amd64 1.2.6-1build5 [1.544 B]                                                                       
Get:11 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libopenscenegraph100v5 amd64 3.2.1-7ubuntu4 [5.563 kB]                                                       
Get:12 http://archive.ubuntu.com/ubuntu xenial/main amd64 python-tk amd64 2.7.11-2 [26,2 kB]                                                                               
Get:13 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libpodofo0.9.3 amd64 0.9.3-4 [518 kB]                                                                        
Get:14 http://ppa.launchpad.net/scribus/ppa/ubuntu xenial/main amd64 scribus-ng amd64 1.5.3-0ubuntu16.04~ppa0 [8.332 kB]                                                   
Fetched 36,1 MB in 1min 10s (510 kB/s)                                                                                                                                     
Selecting previously unselected package tk8.6-blt2.5.
(Reading database ... 343090 files and directories currently installed.)
Preparing to unpack .../tk8.6-blt2.5_2.5.3+dfsg-3_amd64.deb ...
Unpacking tk8.6-blt2.5 (2.5.3+dfsg-3) ...
Selecting previously unselected package blt.
Preparing to unpack .../blt_2.5.3+dfsg-3_amd64.deb ...
Unpacking blt (2.5.3+dfsg-3) ...
Selecting previously unselected package libcoin80v5.
Preparing to unpack .../libcoin80v5_3.1.4~abc9f50+dfsg1-1_amd64.deb ...
Unpacking libcoin80v5 (3.1.4~abc9f50+dfsg1-1) ...
Selecting previously unselected package libopenthreads20:amd64.
Preparing to unpack .../libopenthreads20_3.2.1-7ubuntu4_amd64.deb ...
Unpacking libopenthreads20:amd64 (3.2.1-7ubuntu4) ...
Selecting previously unselected package libxine2-bin.
Preparing to unpack .../libxine2-bin_1.2.6-1build5_amd64.deb ...
Unpacking libxine2-bin (1.2.6-1build5) ...
Selecting previously unselected package libxine2-ffmpeg.
Preparing to unpack .../libxine2-ffmpeg_1.2.6-1build5_amd64.deb ...
Unpacking libxine2-ffmpeg (1.2.6-1build5) ...
Selecting previously unselected package libxine2-misc-plugins.
Preparing to unpack .../libxine2-misc-plugins_1.2.6-1build5_amd64.deb ...
Unpacking libxine2-misc-plugins (1.2.6-1build5) ...
Selecting previously unselected package libxine2-plugins.
Preparing to unpack .../libxine2-plugins_1.2.6-1build5_all.deb ...
Unpacking libxine2-plugins (1.2.6-1build5) ...
Selecting previously unselected package libxine2.
Preparing to unpack .../libxine2_1.2.6-1build5_amd64.deb ...
Unpacking libxine2 (1.2.6-1build5) ...
Selecting previously unselected package libopenscenegraph100v5:amd64.
Preparing to unpack .../libopenscenegraph100v5_3.2.1-7ubuntu4_amd64.deb ...
Unpacking libopenscenegraph100v5:amd64 (3.2.1-7ubuntu4) ...
Selecting previously unselected package python-tk.
Preparing to unpack .../python-tk_2.7.11-2_amd64.deb ...
Unpacking python-tk (2.7.11-2) ...
Selecting previously unselected package libpodofo0.9.3.
Preparing to unpack .../libpodofo0.9.3_0.9.3-4_amd64.deb ...
Unpacking libpodofo0.9.3 (0.9.3-4) ...
Selecting previously unselected package scribus-ng-data.
Preparing to unpack .../scribus-ng-data_1.5.3-0ubuntu16.04~ppa0_all.deb ...
Unpacking scribus-ng-data (1.5.3-0ubuntu16.04~ppa0) ...
Selecting previously unselected package scribus-ng.
Preparing to unpack .../scribus-ng_1.5.3-0ubuntu16.04~ppa0_amd64.deb ...
Unpacking scribus-ng (1.5.3-0ubuntu16.04~ppa0) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for shared-mime-info (1.5-2ubuntu0.1) ...
Unknown media type in type 'all/all'
Unknown media type in type 'all/allfiles'
Processing triggers for mime-support (3.59ubuntu1) ...
Processing triggers for desktop-file-utils (0.22+linuxmint1) ...
Setting up tk8.6-blt2.5 (2.5.3+dfsg-3) ...
Setting up blt (2.5.3+dfsg-3) ...
Setting up libcoin80v5 (3.1.4~abc9f50+dfsg1-1) ...
Setting up libopenthreads20:amd64 (3.2.1-7ubuntu4) ...
Setting up libxine2-bin (1.2.6-1build5) ...
Setting up libxine2-ffmpeg (1.2.6-1build5) ...
Setting up libxine2-misc-plugins (1.2.6-1build5) ...
Setting up libxine2-plugins (1.2.6-1build5) ...
Setting up libxine2 (1.2.6-1build5) ...
Setting up libopenscenegraph100v5:amd64 (3.2.1-7ubuntu4) ...
Setting up python-tk (2.7.11-2) ...
Setting up libpodofo0.9.3 (0.9.3-4) ...
Setting up scribus-ng-data (1.5.3-0ubuntu16.04~ppa0) ...
Setting up scribus-ng (1.5.3-0ubuntu16.04~ppa0) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ... 
```

Enter fullscreen mode Exit fullscreen mode