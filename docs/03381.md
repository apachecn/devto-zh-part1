# sudo add-apt-repository PPA:pmjdebruijn/dark table-release

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppapmjdebruijndarktable-release-3nm>

```
➜  ~ sudo add-apt-repository ppa:pmjdebruijn/darktable-release
[sudo] password for psnc: 
You are about to add the following PPA:
 "Fairly Stable" releases for Darktable

Do not install a more recent version of Lensfun using another PPA as it will likely cause issues due the API changes. The lensfun package for Xenial on this PPA already has new lenses patched in.

Xenial (16.04 LTS) has some extra library updates available, as some relevant libraries shipped by Canonical for Xenial are fairly dated.

Trusty (14.04 LTS) has some extra library updates available, as the relevant libraries shipped by Canonical for Trusty are fairly dated. These extras will however no longer be updated, they will remain as-is.

Trusty will no longer be receiving updates after 2.0.7.

Precise will no longer be receiving updates after 1.4.2.

New Ubuntu versions will be supported within a week or two after their release. Please DO NOT contact me about that. Be patient.

Please do NOT contact me about providing updates for anything. I will update things when I see fit to do so. I do NOT do requests!

DO NOT INSTALL THESE BINARY PACKAGES IN DEBIAN.
 More info: https://launchpad.net/~pmjdebruijn/+archive/ubuntu/darktable-release
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.76aArIMOuQ/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
C07EE05F
gpg: requesting key C07EE05F from hkp server keyserver.ubuntu.com
gpg: key C07EE05F: public key "Launchpad PPA for Pascal de Bruijn" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get update 
Ign:1 http://ftp.fau.de/mint/packages sonya InRelease
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]   
Ign:3 http://dl.google.com/linux/chrome/deb stable InRelease                                                                   
Hit:4 http://ftp.fau.de/mint/packages sonya Release                                                                            
Hit:5 http://repo.mysql.com/apt/ubuntu xenial InRelease                                                   
Hit:6 http://dl.google.com/linux/chrome/deb stable Release                                                
Hit:9 http://archive.canonical.com/ubuntu xenial InRelease                                                                                                                 
Hit:10 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial InRelease                                                                                                    
Hit:11 http://archive.ubuntu.com/ubuntu xenial InRelease                                                             
Ign:12 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge InRelease                                        
Get:13 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                                                                        
Get:14 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release [14,9 kB]                                                      
Ign:15 http://download.webmin.com/download/repository sarge InRelease                                                                      
Hit:16 http://ppa.launchpad.net/byobu/ppa/ubuntu xenial InRelease                                                
Get:17 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release.gpg [173 B]                                                                               
Ign:17 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release.gpg                                                                            
Hit:18 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease                                                                         
Hit:19 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge/contrib amd64 Packages                                                              
Get:20 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                                                                                                
Hit:21 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                                                                                      
Hit:22 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge/contrib i386 Packages                                                                             
Hit:23 http://ppa.launchpad.net/kdenlive/kdenlive-stable/ubuntu xenial InRelease                                                                                           
Get:24 http://download.webmin.com/download/repository sarge Release [14,9 kB]                                                                                              
Hit:25 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                                                                                      
Get:26 http://download.webmin.com/download/repository sarge Release.gpg [173 B]                                                                                            
Ign:26 http://download.webmin.com/download/repository sarge Release.gpg                                                                                                    
Hit:27 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial InRelease                                                                                                      
Hit:28 http://download.webmin.com/download/repository sarge/contrib amd64 Packages                                                                                         
Hit:29 http://ppa.launchpad.net/libreoffice/ppa/ubuntu xenial InRelease                                                                                                    
Hit:30 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial InRelease                                                                                 
Hit:31 http://ppa.launchpad.net/nijel/phpmyadmin/ubuntu xenial InRelease                                                                                                   
Hit:32 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial InRelease                                                                                                     
Hit:33 http://ppa.launchpad.net/ondrej/php/ubuntu xenial InRelease                                                                                                         
Hit:34 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial InRelease                                                                                       
Get:35 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial InRelease [17,6 kB]                                                                            
Hit:36 http://download.webmin.com/download/repository sarge/contrib i386 Packages                                                                                          
Hit:37 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease                                                                                              
Hit:38 http://ppa.launchpad.net/webupd8team/java/ubuntu xenial InRelease                                                                                                   
Hit:39 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial InRelease                                                                                                  
Get:40 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial/main Sources [2.008 B]                                                                         
Get:41 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial/main amd64 Packages [2.612 B]                                                                  
Get:42 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial/main i386 Packages [2.628 B]                                                                   
Get:43 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial/main Translation-en [2.164 B]                                                                  
Fetched 364 kB in 15s (23,7 kB/s)                                                                                                                                          
Reading package lists... Done
W: GPG error: http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY D97A3AE911F63C51
W: The repository 'http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release' is not signed.
N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
N: See apt-secure(8) manpage for repository creation and user configuration details.
W: GPG error: http://download.webmin.com/download/repository sarge Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY D97A3AE911F63C51
W: The repository 'http://download.webmin.com/download/repository sarge Release' is not signed.
N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
N: See apt-secure(8) manpage for repository creation and user configuration details. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install darktable
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libcolord-gtk1 libflickcurl0 libgraphicsmagick-q16-3 libosmgpsmap-1.0-1 libpugixml1v5
Suggested packages:
  graphicsmagick-dbg
Recommended packages:
  darktable-dbg
The following NEW packages will be installed:
  darktable libcolord-gtk1 libflickcurl0 libgraphicsmagick-q16-3 libosmgpsmap-1.0-1 libpugixml1v5
0 upgraded, 6 newly installed, 0 to remove and 3 not upgraded.
Need to get 4.576 kB of archives.
After this operation, 19,8 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libflickcurl0 amd64 1.25-3 [134 kB]
Get:2 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 libgraphicsmagick-q16-3 amd64 1.3.25-8~ubuntu16.04.1+deb.sury.org+2 [1.122 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libcolord-gtk1 amd64 0.1.26-1 [16,6 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libosmgpsmap-1.0-1 amd64 1.1.0-1 [39,2 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libpugixml1v5 amd64 1.7-2 [82,9 kB]
Get:6 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial/main amd64 darktable amd64 1:2.2.5-0pmjdebruijn1~xenial [3.181 kB]
Fetched 4.576 kB in 19s (236 kB/s)                                                                                                                                         
Selecting previously unselected package libflickcurl0:amd64.
(Reading database ... 336626 files and directories currently installed.)
Preparing to unpack .../libflickcurl0_1.25-3_amd64.deb ...
Unpacking libflickcurl0:amd64 (1.25-3) ...
Selecting previously unselected package libcolord-gtk1:amd64.
Preparing to unpack .../libcolord-gtk1_0.1.26-1_amd64.deb ...
Unpacking libcolord-gtk1:amd64 (0.1.26-1) ...
Selecting previously unselected package libgraphicsmagick-q16-3.
Preparing to unpack .../libgraphicsmagick-q16-3_1.3.25-8~ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking libgraphicsmagick-q16-3 (1.3.25-8~ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package libosmgpsmap-1.0-1:amd64.
Preparing to unpack .../libosmgpsmap-1.0-1_1.1.0-1_amd64.deb ...
Unpacking libosmgpsmap-1.0-1:amd64 (1.1.0-1) ...
Selecting previously unselected package libpugixml1v5:amd64.
Preparing to unpack .../libpugixml1v5_1.7-2_amd64.deb ...
Unpacking libpugixml1v5:amd64 (1.7-2) ...
Selecting previously unselected package darktable.
Preparing to unpack .../darktable_1%3a2.2.5-0pmjdebruijn1~xenial_amd64.deb ...
Unpacking darktable (1:2.2.5-0pmjdebruijn1~xenial) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for desktop-file-utils (0.22+linuxmint1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Setting up libflickcurl0:amd64 (1.25-3) ...
Setting up libcolord-gtk1:amd64 (0.1.26-1) ...
Setting up libgraphicsmagick-q16-3 (1.3.25-8~ubuntu16.04.1+deb.sury.org+2) ...
Setting up libosmgpsmap-1.0-1:amd64 (1.1.0-1) ...
Setting up libpugixml1v5:amd64 (1.7-2) ...
Setting up darktable (1:2.2.5-0pmjdebruijn1~xenial) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode