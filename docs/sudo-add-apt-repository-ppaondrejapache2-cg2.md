# sudo add-apt-repository PPA:ondrej/Apache 2

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppaondrejapache2-cg2>

```
➜  ~ sudo add-apt-repository ppa:ondrej/apache2
[sudo] password for webdev: 
You are about to add the following PPA:
 This branch follows latest Apache2 packages as maintained by the Debian Apache2 team with couple of compatibility patches on top.

It also includes some widely used Apache 2 modules (if you need some other feel free to send me a request).

BUGS&FEATURES: This PPA now has a issue tracker: https://deb.sury.org/#bug-reporting

PLEASE READ: If you like my work and want to give me a little motivation, please consider donating: https://deb.sury.org/#donate
 More info: https://launchpad.net/~ondrej/+archive/ubuntu/apache2
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.nWSJlEb0rh/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
E5267A6C
gpg: requesting key E5267A6C from hkp server keyserver.ubuntu.com
gpg: key E5267A6C: public key "Launchpad PPA for Ondřej Surý" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get update
Hit:1 http://archive.ubuntu.com/ubuntu xenial InRelease                                                                                                                   
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]                                                                
Hit:3 http://archive.canonical.com/ubuntu xenial InRelease                                                                                           
Ign:4 http://mint-mirror.gwendallebihan.net/packages sonya InRelease                                                                                 
Hit:5 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease                                                            
Get:6 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                                                                                   
Hit:7 http://mint-mirror.gwendallebihan.net/packages sonya Release                                                                                                    
Hit:9 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                                                                                  
Hit:10 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                                                          
Get:11 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                        
Get:12 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial InRelease [23,8 kB]                              
Hit:13 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease                                                                                                                                                                                                                                           
Hit:14 http://ppa.launchpad.net/webupd8team/java/ubuntu xenial InRelease                                                                                                   
Get:15 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main Sources [6.656 B]                                                                                        
Get:16 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 Packages [13,0 kB]                                                                                 
Get:17 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main i386 Packages [13,1 kB]                                                                                  
Get:18 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main Translation-en [7.084 B]                                                                                 
Fetched 370 kB in 2min 14s (2.750 B/s)                                                                                                                                     
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install apache2
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  apache2-bin apache2-data apache2-utils libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libnghttp2-14 libssl1.1
Suggested packages:
  apache2-doc apache2-suexec-pristine | apache2-suexec-custom
The following NEW packages will be installed:
  apache2 apache2-bin apache2-data apache2-utils libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libnghttp2-14 libssl1.1
0 upgraded, 10 newly installed, 0 to remove and 24 not upgraded.
Need to get 3.215 kB of archives.
After this operation, 10,2 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 libssl1.1 amd64 1.1.0f-2~ubuntu16.04.1+deb.sury.org+4 [1.333 kB]
Get:2 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 libapr1 amd64 1.6.2-1+ubuntu16.04.1+deb.sury.org+2 [96,3 kB]                                       
Get:3 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 libaprutil1 amd64 1.6.0-2+ubuntu16.04.1+deb.sury.org+2 [89,6 kB]                                   
Get:4 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 libaprutil1-dbd-sqlite3 amd64 1.6.0-2+ubuntu16.04.1+deb.sury.org+2 [16,9 kB]                       
Get:5 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 libaprutil1-ldap amd64 1.6.0-2+ubuntu16.04.1+deb.sury.org+2 [15,0 kB]                              
Get:6 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 libnghttp2-14 amd64 1.25.0-1+ubuntu16.04.1+deb.sury.org+1 [80,0 kB]                                
Get:7 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 apache2-bin amd64 2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2 [1.119 kB]                               
Get:8 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 apache2-utils amd64 2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2 [143 kB]                               
Get:9 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 apache2-data all 2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2 [163 kB]                                  
Get:10 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial/main amd64 apache2 amd64 2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2 [159 kB]                                    
Fetched 3.215 kB in 19s (162 kB/s)                                                                                                                                        
Preconfiguring packages ...
Selecting previously unselected package libssl1.1:amd64.
(Reading database ... 246675 files and directories currently installed.)
Preparing to unpack .../libssl1.1_1.1.0f-2~ubuntu16.04.1+deb.sury.org+4_amd64.deb ...
Unpacking libssl1.1:amd64 (1.1.0f-2~ubuntu16.04.1+deb.sury.org+4) ...
Selecting previously unselected package libapr1:amd64.
Preparing to unpack .../libapr1_1.6.2-1+ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking libapr1:amd64 (1.6.2-1+ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package libaprutil1:amd64.
Preparing to unpack .../libaprutil1_1.6.0-2+ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking libaprutil1:amd64 (1.6.0-2+ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package libaprutil1-dbd-sqlite3:amd64.
Preparing to unpack .../libaprutil1-dbd-sqlite3_1.6.0-2+ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking libaprutil1-dbd-sqlite3:amd64 (1.6.0-2+ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package libaprutil1-ldap:amd64.
Preparing to unpack .../libaprutil1-ldap_1.6.0-2+ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking libaprutil1-ldap:amd64 (1.6.0-2+ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package libnghttp2-14:amd64.
Preparing to unpack .../libnghttp2-14_1.25.0-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking libnghttp2-14:amd64 (1.25.0-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package apache2-bin.
Preparing to unpack .../apache2-bin_2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking apache2-bin (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package apache2-utils.
Preparing to unpack .../apache2-utils_2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking apache2-utils (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package apache2-data.
Preparing to unpack .../apache2-data_2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2_all.deb ...
Unpacking apache2-data (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Selecting previously unselected package apache2.
Preparing to unpack .../apache2_2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2_amd64.deb ...
Unpacking apache2 (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for systemd (229-4ubuntu20) ...
Processing triggers for ureadahead (0.100.0-19) ...
Setting up libssl1.1:amd64 (1.1.0f-2~ubuntu16.04.1+deb.sury.org+4) ...
Setting up libapr1:amd64 (1.6.2-1+ubuntu16.04.1+deb.sury.org+2) ...
Setting up libaprutil1:amd64 (1.6.0-2+ubuntu16.04.1+deb.sury.org+2) ...
Setting up libaprutil1-dbd-sqlite3:amd64 (1.6.0-2+ubuntu16.04.1+deb.sury.org+2) ...
Setting up libaprutil1-ldap:amd64 (1.6.0-2+ubuntu16.04.1+deb.sury.org+2) ...
Setting up libnghttp2-14:amd64 (1.25.0-1+ubuntu16.04.1+deb.sury.org+1) ...
Setting up apache2-bin (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Setting up apache2-utils (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Setting up apache2-data (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Setting up apache2 (2.4.29-0.1+ubuntu16.04.1+deb.sury.org+2) ...
Enabling module mpm_event.
Enabling module authz_core.
Enabling module authz_host.
Enabling module authn_core.
Enabling module auth_basic.
Enabling module access_compat.
Enabling module authn_file.
Enabling module authz_user.
Enabling module alias.
Enabling module dir.
Enabling module autoindex.
Enabling module env.
Enabling module mime.
Enabling module negotiation.
Enabling module setenvif.
Enabling module filter.
Enabling module deflate.
Enabling module status.
Enabling module reqtimeout.
Enabling conf charset.
Enabling conf localized-error-pages.
Enabling conf other-vhosts-access-log.
Enabling conf security.
Enabling conf serve-cgi-bin.
Enabling site 000-default.
insserv: warning: current start runlevel(s) (empty) of script `apache-htcacheclean' overrides LSB defaults (2 3 4 5).
insserv: warning: current stop runlevel(s) (0 1 2 3 4 5 6) of script `apache-htcacheclean' overrides LSB defaults (0 1 6).
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for systemd (229-4ubuntu20) ...
Processing triggers for ureadahead (0.100.0-19) ... 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ apache2 -v
Server version: Apache/2.4.29 (Ubuntu)
Server built:   2017-10-22T13:35:47 
```

Enter fullscreen mode Exit fullscreen mode