# sudo add-apt-repository PPA:inkscape . dev/stable

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppainkscapedevstable-b6c>

```
➜  ~  sudo add-apt-repository ppa:inkscape.dev/stable
[sudo] password for psnc: 
You are about to add the following PPA:
 The Inkscape Stable PPA is intended to provide the current supported release of Inkscape.
 More info: https://launchpad.net/~inkscape.dev/+archive/ubuntu/stable
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.Y9JjkMStKR/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
B9A06DE3
gpg: requesting key B9A06DE3 from hkp server keyserver.ubuntu.com
gpg: key B9A06DE3: public key "Launchpad PPA for Inkscape Developers" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get update
Get:1 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Ign:2 http://ftp.fau.de/mint/packages sonya InRelease              
Get:3 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:4 http://archive.canonical.com/ubuntu xenial InRelease [11,5 kB]
Ign:5 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge InRelease
Get:6 http://ftp.fau.de/mint/packages sonya Release [24,1 kB]      
Get:7 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release [14,9 kB]
Get:8 http://repo.mysql.com/apt/ubuntu xenial InRelease [19,1 kB]  
Ign:9 http://dl.google.com/linux/chrome/deb stable InRelease       
Get:10 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release.gpg [173 B]
Get:12 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Ign:13 http://download.webmin.com/download/repository sarge InRelease
Ign:10 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release.gpg
Get:14 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge/contrib amd64 Packages [1.383 B]
Get:15 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
Get:16 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge/contrib i386 Packages [1.383 B]
Get:17 http://download.webmin.com/download/repository sarge Release [14,9 kB]
Get:18 http://dl.google.com/linux/chrome/deb stable Release [1.189 B]
Get:20 http://download.webmin.com/download/repository sarge Release.gpg [173 B]
Ign:20 http://download.webmin.com/download/repository sarge Release.gpg
Get:21 http://download.webmin.com/download/repository sarge/contrib amd64 Packages [1.383 B]
Get:22 http://download.webmin.com/download/repository sarge/contrib i386 Packages [1.383 B]
Get:23 http://ppa.launchpad.net/achadwick/mypaint-testing/ubuntu xenial InRelease [17,6 kB]
Get:24 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial InRelease [18,0 kB]
Get:25 http://ppa.launchpad.net/byobu/ppa/ubuntu xenial InRelease [17,5 kB]
Get:26 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease [17,5 kB]
Get:27 http://ppa.launchpad.net/inkscape.dev/stable/ubuntu xenial InRelease [17,5 kB]
Get:28 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease [18,0 kB]
Get:29 http://ppa.launchpad.net/kdenlive/kdenlive-stable/ubuntu xenial InRelease [18,1 kB]
Get:30 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease [24,3 kB]
Get:31 http://ppa.launchpad.net/kritalime/ppa/ubuntu xenial InRelease [24,3 kB]
Get:32 http://ppa.launchpad.net/libreoffice/ppa/ubuntu xenial InRelease [23,8 kB]
Get:33 http://ppa.launchpad.net/maarten-baert/simplescreenrecorder/ubuntu xenial InRelease [17,6 kB]
Get:34 http://ppa.launchpad.net/nijel/phpmyadmin/ubuntu xenial InRelease [17,5 kB]
Get:35 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial InRelease [23,8 kB]
Get:36 http://ppa.launchpad.net/ondrej/php/ubuntu xenial InRelease [23,9 kB]
Get:37 http://ppa.launchpad.net/otto-kesselgulasch/gimp-edge/ubuntu xenial InRelease [17,5 kB]
Get:38 http://ppa.launchpad.net/pmjdebruijn/darktable-release/ubuntu xenial InRelease [17,6 kB]
Get:39 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease [23,8 kB]
Get:40 http://ppa.launchpad.net/webupd8team/java/ubuntu xenial InRelease [17,5 kB]
Get:41 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial InRelease [17,5 kB]
Get:42 http://ppa.launchpad.net/inkscape.dev/stable/ubuntu xenial/main Sources [988 B]
Get:43 http://ppa.launchpad.net/inkscape.dev/stable/ubuntu xenial/main amd64 Packages [1.020 B]
Get:44 http://ppa.launchpad.net/inkscape.dev/stable/ubuntu xenial/main i386 Packages [1.016 B]
Get:45 http://ppa.launchpad.net/inkscape.dev/stable/ubuntu xenial/main Translation-en [692 B]
Fetched 1.022 kB in 6s (154 kB/s)                                  
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install inkscape
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libgtkspell0 libmagick++-6.q16-5v5 libpotrace0
Suggested packages:
  dia | dia-gnome libgnomevfs2-extra libsvg-perl libxml-xql-perl
  transfig pstoedit
Recommended packages:
  libwmf-bin perlmagick python-scour python-uniconvertor
The following NEW packages will be installed:
  inkscape libgtkspell0 libmagick++-6.q16-5v5 libpotrace0
0 upgraded, 4 newly installed, 0 to remove and 1 not upgraded.
Need to get 17,1 MB of archives.
After this operation, 133 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgtkspell0 amd64 2.0.16-1.1ubuntu1 [11,1 kB]
Get:2 http://ppa.launchpad.net/inkscape.dev/stable/ubuntu xenial/main amd64 inkscape amd64 0.92.2+68~ubuntu16.04.1 [17,0 MB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libmagick++-6.q16-5v5 amd64 8:6.8.9.9-7ubuntu5.9 [136 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/main amd64 libpotrace0 amd64 1.13-2 [17,7 kB]
Fetched 17,1 MB in 24s (695 kB/s)                                  
Selecting previously unselected package libgtkspell0.
(Reading database ... 341799 files and directories currently installed.)
Preparing to unpack .../libgtkspell0_2.0.16-1.1ubuntu1_amd64.deb ...
Unpacking libgtkspell0 (2.0.16-1.1ubuntu1) ...
Selecting previously unselected package libmagick++-6.q16-5v5:amd64.
Preparing to unpack .../libmagick++-6.q16-5v5_8%3a6.8.9.9-7ubuntu5.9_amd64.deb ...
Unpacking libmagick++-6.q16-5v5:amd64 (8:6.8.9.9-7ubuntu5.9) ...
Selecting previously unselected package libpotrace0.
Preparing to unpack .../libpotrace0_1.13-2_amd64.deb ...
Unpacking libpotrace0 (1.13-2) ...
Selecting previously unselected package inkscape.
Preparing to unpack .../inkscape_0.92.2+68~ubuntu16.04.1_amd64.deb ...
Unpacking inkscape (0.92.2+68~ubuntu16.04.1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for desktop-file-utils (0.22+linuxmint1) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up libgtkspell0 (2.0.16-1.1ubuntu1) ...
Setting up libmagick++-6.q16-5v5:amd64 (8:6.8.9.9-7ubuntu5.9) ...
Setting up libpotrace0 (1.13-2) ...
Setting up inkscape (0.92.2+68~ubuntu16.04.1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ... 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install libwmf-bin perlmagick python-scour python-uniconvertor
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libimage-magick-perl libimage-magick-q16-perl
Suggested packages:
  imagemagick-doc python-rsvg python-uniconvertor-dbg
The following NEW packages will be installed:
  libimage-magick-perl libimage-magick-q16-perl libwmf-bin
  perlmagick python-scour python-uniconvertor
0 upgraded, 6 newly installed, 0 to remove and 1 not upgraded.
Need to get 470 kB of archives.
After this operation, 2.519 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libimage-magick-q16-perl amd64 8:6.8.9.9-7ubuntu5.9 [109 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libimage-magick-perl all 8:6.8.9.9-7ubuntu5.9 [62,3 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/main amd64 libwmf-bin amd64 0.2.8.4-10.5ubuntu1 [17,1 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 perlmagick all 8:6.8.9.9-7ubuntu5.9 [13,2 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/main amd64 python-scour all 0.32-1 [42,7 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial/universe amd64 python-uniconvertor amd64 1.1.5-2 [226 kB]
Fetched 470 kB in 0s (906 kB/s)          
Selecting previously unselected package libimage-magick-q16-perl.
(Reading database ... 342884 files and directories currently installed.)
Preparing to unpack .../libimage-magick-q16-perl_8%3a6.8.9.9-7ubuntu5.9_amd64.deb ...
Unpacking libimage-magick-q16-perl (8:6.8.9.9-7ubuntu5.9) ...
Selecting previously unselected package libimage-magick-perl.
Preparing to unpack .../libimage-magick-perl_8%3a6.8.9.9-7ubuntu5.9_all.deb ...
Unpacking libimage-magick-perl (8:6.8.9.9-7ubuntu5.9) ...
Selecting previously unselected package libwmf-bin.
Preparing to unpack .../libwmf-bin_0.2.8.4-10.5ubuntu1_amd64.deb ...
Unpacking libwmf-bin (0.2.8.4-10.5ubuntu1) ...
Selecting previously unselected package perlmagick.
Preparing to unpack .../perlmagick_8%3a6.8.9.9-7ubuntu5.9_all.deb ...
Unpacking perlmagick (8:6.8.9.9-7ubuntu5.9) ...
Selecting previously unselected package python-scour.
Preparing to unpack .../python-scour_0.32-1_all.deb ...
Unpacking python-scour (0.32-1) ...
Selecting previously unselected package python-uniconvertor.
Preparing to unpack .../python-uniconvertor_1.1.5-2_amd64.deb ...
Unpacking python-uniconvertor (1.1.5-2) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up libimage-magick-q16-perl (8:6.8.9.9-7ubuntu5.9) ...
Setting up libimage-magick-perl (8:6.8.9.9-7ubuntu5.9) ...
Setting up libwmf-bin (0.2.8.4-10.5ubuntu1) ...
Setting up perlmagick (8:6.8.9.9-7ubuntu5.9) ...
Setting up python-scour (0.32-1) ...
Setting up python-uniconvertor (1.1.5-2) ... 
```

Enter fullscreen mode Exit fullscreen mode