# sudo add-apt-repository PPA:ondrej/PHP

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppaondrejphp-9g6>

```
âžœ  ~ sudo add-apt-repository ppa:ondrej/php
 Co-installable PHP versions: PHP 5.6, PHP 7.x and most requested extensions are included.

You can get more information about the packages at https://deb.sury.org

BUGS&FEATURES: This PPA now has a issue tracker:
https://deb.sury.org/#bug-reporting

CAVEATS:
1\. If you are using php-gearman, you need to add ppa:ondrej/pkg-gearman
2\. If you are using apache2, you are advised to add ppa:ondrej/apache2
3\. If you are using nginx, you are advise to add ppa:ondrej/nginx-mainline
Â Â Â or ppa:ondrej/nginx

PLEASE READ: If you like my work and want to give me a little motivation, please consider donating regularly: https://donate.sury.org/

WARNING: add-apt-repository is broken with non-UTF-8 locales, see 
https://github.com/oerdnj/deb.sury.org/issues/56 for workaround:

# LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php
 More info: https://launchpad.net/~ondrej/+archive/ubuntu/php
Press [ENTER] to continue or ctrl-c to cancel adding it

gpg: keyring `/tmp/tmpf0vaxm31/secring.gpg' created
gpg: keyring `/tmp/tmpf0vaxm31/pubring.gpg' created
gpg: requesting key E5267A6C from hkp server keyserver.ubuntu.com
gpg: /tmp/tmpf0vaxm31/trustdb.gpg: trustdb created
gpg: key E5267A6C: public key "Launchpad PPA for OndÅ™ej SurÃ½" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
OK 
```

Enter fullscreen mode Exit fullscreen mode

```
âžœ  ~ sudo apt-get update  
Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Hit:2 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease                       
Hit:3 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                                                                 
Hit:4 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                                                                 
Get:5 http://ppa.launchpad.net/ondrej/php/ubuntu xenial InRelease [23.9 kB]                                         
Get:6 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 Packages [47.0 kB]    
Get:7 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main i386 Packages [46.7 kB]                            
Get:8 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main Translation-en [26.7 kB]                           
Hit:9 http://us.archive.ubuntu.com/ubuntu xenial InRelease                               
Get:10 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:11 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                                                                                                               
Get:12 http://us.archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [540 kB]                                                                                                   
Fetched 991 kB in 12s (76.8 kB/s)                                                                                                                                                            
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

```
âžœ  ~ sudo add-apt-repository ppa:ondrej/apache2
 This branch follows latest Apache2 packages as maintained by the Debian Apache2 team with couple of compatibility patches on top.

It also includes some widely used Apache 2 modules (if you need some other feel free to send me a request).

BUGS&FEATURES: This PPA now has a issue tracker: https://deb.sury.org/#bug-reporting

PLEASE READ: If you like my work and want to give me a little motivation, please consider donating: https://deb.sury.org/#donate
 More info: https://launchpad.net/~ondrej/+archive/ubuntu/apache2
Press [ENTER] to continue or ctrl-c to cancel adding it

gpg: keyring `/tmp/tmp0i1kw2id/secring.gpg' created
gpg: keyring `/tmp/tmp0i1kw2id/pubring.gpg' created
gpg: requesting key E5267A6C from hkp server keyserver.ubuntu.com
gpg: /tmp/tmp0i1kw2id/trustdb.gpg: trustdb created
gpg: key E5267A6C: public key "Launchpad PPA for OndÅ™ej SurÃ½" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
OK 
```

Enter fullscreen mode Exit fullscreen mode

```
âžœ  ~ sudo apt-get install apache2
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  apache2-bin apache2-data apache2-utils libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap liblua5.1-0 ssl-cert
Suggested packages:
  www-browser apache2-doc apache2-suexec-pristine | apache2-suexec-custom openssl-blacklist
The following NEW packages will be installed:
  apache2 apache2-bin apache2-data apache2-utils libapr1 libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap liblua5.1-0 ssl-cert
0 upgraded, 10 newly installed, 0 to remove and 8 not upgraded.
Need to get 1,557 kB of archives.
After this operation, 6,432 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libapr1 amd64 1.5.2-3 [86.0 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libaprutil1 amd64 1.5.4-1build1 [77.1 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libaprutil1-dbd-sqlite3 amd64 1.5.4-1build1 [10.6 kB]                                                                            
Get:4 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libaprutil1-ldap amd64 1.5.4-1build1 [8,720 B]                                                                                   
Get:5 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 liblua5.1-0 amd64 5.1.5-8ubuntu1 [102 kB]                                                                                        
Get:6 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apache2-bin amd64 2.4.18-2ubuntu3.5 [925 kB]                                                                             
Get:7 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apache2-utils amd64 2.4.18-2ubuntu3.5 [82.3 kB]                                                                          
Get:8 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apache2-data all 2.4.18-2ubuntu3.5 [162 kB]                                                                              
Get:9 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 apache2 amd64 2.4.18-2ubuntu3.5 [86.7 kB]                                                                                
Get:10 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 ssl-cert all 1.0.37 [16.9 kB]                                                                                                   
Fetched 1,557 kB in 10s (151 kB/s)                                                                                                                                                           
Preconfiguring packages ...
Selecting previously unselected package libapr1:amd64.
(Reading database ... 88128 files and directories currently installed.)
Preparing to unpack .../libapr1_1.5.2-3_amd64.deb ...
Unpacking libapr1:amd64 (1.5.2-3) ...
Selecting previously unselected package libaprutil1:amd64.
Preparing to unpack .../libaprutil1_1.5.4-1build1_amd64.deb ...
Unpacking libaprutil1:amd64 (1.5.4-1build1) ...
Selecting previously unselected package libaprutil1-dbd-sqlite3:amd64.
Preparing to unpack .../libaprutil1-dbd-sqlite3_1.5.4-1build1_amd64.deb ...
Unpacking libaprutil1-dbd-sqlite3:amd64 (1.5.4-1build1) ...
Selecting previously unselected package libaprutil1-ldap:amd64.
Preparing to unpack .../libaprutil1-ldap_1.5.4-1build1_amd64.deb ...
Unpacking libaprutil1-ldap:amd64 (1.5.4-1build1) ...
Selecting previously unselected package liblua5.1-0:amd64.
Preparing to unpack .../liblua5.1-0_5.1.5-8ubuntu1_amd64.deb ...
Unpacking liblua5.1-0:amd64 (5.1.5-8ubuntu1) ...
Selecting previously unselected package apache2-bin.
Preparing to unpack .../apache2-bin_2.4.18-2ubuntu3.5_amd64.deb ...
Unpacking apache2-bin (2.4.18-2ubuntu3.5) ...
Selecting previously unselected package apache2-utils.
Preparing to unpack .../apache2-utils_2.4.18-2ubuntu3.5_amd64.deb ...
Unpacking apache2-utils (2.4.18-2ubuntu3.5) ...
Selecting previously unselected package apache2-data.
Preparing to unpack .../apache2-data_2.4.18-2ubuntu3.5_all.deb ...
Unpacking apache2-data (2.4.18-2ubuntu3.5) ...
Selecting previously unselected package apache2.
Preparing to unpack .../apache2_2.4.18-2ubuntu3.5_amd64.deb ...
Unpacking apache2 (2.4.18-2ubuntu3.5) ...
Selecting previously unselected package ssl-cert.
Preparing to unpack .../ssl-cert_1.0.37_all.deb ...
Unpacking ssl-cert (1.0.37) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for systemd (229-4ubuntu19) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for ufw (0.35-0ubuntu2) ...
Setting up libapr1:amd64 (1.5.2-3) ...
Setting up libaprutil1:amd64 (1.5.4-1build1) ...
Setting up libaprutil1-dbd-sqlite3:amd64 (1.5.4-1build1) ...
Setting up libaprutil1-ldap:amd64 (1.5.4-1build1) ...
Setting up liblua5.1-0:amd64 (5.1.5-8ubuntu1) ...
Setting up apache2-bin (2.4.18-2ubuntu3.5) ...
Setting up apache2-utils (2.4.18-2ubuntu3.5) ...
Setting up apache2-data (2.4.18-2ubuntu3.5) ...
Setting up apache2 (2.4.18-2ubuntu3.5) ...
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
Enabling conf charset.
Enabling conf localized-error-pages.
Enabling conf other-vhosts-access-log.
Enabling conf security.
Enabling conf serve-cgi-bin.
Enabling site 000-default.
Setting up ssl-cert (1.0.37) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for systemd (229-4ubuntu19) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for ufw (0.35-0ubuntu2) ... 
```

Enter fullscreen mode Exit fullscreen mode

```
âžœ  ~ sudo apt-get install php7.1 php5.6 php7.0 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libapache2-mod-php5.6 libapache2-mod-php7.0 libapache2-mod-php7.1 libssl1.1 php-common php5.6-cli php5.6-common php5.6-json php5.6-opcache php5.6-readline php7.0-cli php7.0-common
  php7.0-json php7.0-opcache php7.0-readline php7.1-cli php7.1-common php7.1-json php7.1-opcache php7.1-readline
Suggested packages:
  php-pear
The following NEW packages will be installed:
  libapache2-mod-php5.6 libapache2-mod-php7.0 libapache2-mod-php7.1 libssl1.1 php-common php5.6 php5.6-cli php5.6-common php5.6-json php5.6-opcache php5.6-readline php7.0 php7.0-cli
  php7.0-common php7.0-json php7.0-opcache php7.0-readline php7.1 php7.1-cli php7.1-common php7.1-json php7.1-opcache php7.1-readline
0 upgraded, 23 newly installed, 0 to remove and 8 not upgraded.
Need to get 14.2 MB of archives.
After this operation, 48.0 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 libssl1.1 amd64 1.1.0f-2~ubuntu16.04.1+deb.sury.org+1 [1,333 kB]
Get:2 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php-common all 1:55+ubuntu16.04.1+deb.sury.org+1 [14.5 kB]
Get:3 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php5.6-common amd64 5.6.31-6+ubuntu16.04.1+deb.sury.org+1 [2,743 kB]
Get:4 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php5.6-json amd64 5.6.31-6+ubuntu16.04.1+deb.sury.org+1 [17.1 kB]                                                         
Get:5 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php5.6-opcache amd64 5.6.31-6+ubuntu16.04.1+deb.sury.org+1 [62.2 kB]                                                      
Get:6 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php5.6-readline amd64 5.6.31-6+ubuntu16.04.1+deb.sury.org+1 [13.2 kB]                                                     
Get:7 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php5.6-cli amd64 5.6.31-6+ubuntu16.04.1+deb.sury.org+1 [1,285 kB]                                                         
Get:8 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 libapache2-mod-php5.6 amd64 5.6.31-6+ubuntu16.04.1+deb.sury.org+1 [1,240 kB]                                              
Get:9 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.0-common amd64 7.0.24-1+ubuntu16.04.1+deb.sury.org+1 [879 kB]                                                        
Get:10 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.0-json amd64 7.0.24-1+ubuntu16.04.1+deb.sury.org+1 [16.8 kB]                                                        
Get:11 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.0-opcache amd64 7.0.24-1+ubuntu16.04.1+deb.sury.org+1 [77.0 kB]                                                     
Get:12 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.0-readline amd64 7.0.24-1+ubuntu16.04.1+deb.sury.org+1 [12.8 kB]                                                    
Get:13 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.0-cli amd64 7.0.24-1+ubuntu16.04.1+deb.sury.org+1 [1,288 kB]                                                        
Get:14 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 libapache2-mod-php7.0 amd64 7.0.24-1+ubuntu16.04.1+deb.sury.org+1 [1,229 kB]                                             
Get:15 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.1-common amd64 7.1.10-1+ubuntu16.04.1+deb.sury.org+1 [882 kB]                                                       
Get:16 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.1-json amd64 7.1.10-1+ubuntu16.04.1+deb.sury.org+1 [17.2 kB]                                                        
Get:17 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.1-opcache amd64 7.1.10-1+ubuntu16.04.1+deb.sury.org+1 [142 kB]                                                      
Get:18 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.1-readline amd64 7.1.10-1+ubuntu16.04.1+deb.sury.org+1 [12.8 kB]                                                    
Get:19 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.1-cli amd64 7.1.10-1+ubuntu16.04.1+deb.sury.org+1 [1,327 kB]                                                        
Get:20 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 libapache2-mod-php7.1 amd64 7.1.10-1+ubuntu16.04.1+deb.sury.org+1 [1,268 kB]                                             
Get:21 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php5.6 all 5.6.31-6+ubuntu16.04.1+deb.sury.org+1 [260 kB]                                                                
Get:22 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.0 all 7.0.24-1+ubuntu16.04.1+deb.sury.org+1 [51.9 kB]                                                               
Get:23 http://ppa.launchpad.net/ondrej/php/ubuntu xenial/main amd64 php7.1 all 7.1.10-1+ubuntu16.04.1+deb.sury.org+1 [53.6 kB]                                                               
Fetched 14.2 MB in 51s (276 kB/s)                                                                                                                                                            
Preconfiguring packages ...
Selecting previously unselected package libssl1.1:amd64.
(Reading database ... 88817 files and directories currently installed.)
Preparing to unpack .../libssl1.1_1.1.0f-2~ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking libssl1.1:amd64 (1.1.0f-2~ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php-common.
Preparing to unpack .../php-common_1%3a55+ubuntu16.04.1+deb.sury.org+1_all.deb ...
Unpacking php-common (1:55+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php5.6-common.
Preparing to unpack .../php5.6-common_5.6.31-6+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php5.6-common (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php5.6-json.
Preparing to unpack .../php5.6-json_5.6.31-6+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php5.6-json (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php5.6-opcache.
Preparing to unpack .../php5.6-opcache_5.6.31-6+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php5.6-opcache (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php5.6-readline.
Preparing to unpack .../php5.6-readline_5.6.31-6+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php5.6-readline (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php5.6-cli.
Preparing to unpack .../php5.6-cli_5.6.31-6+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php5.6-cli (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package libapache2-mod-php5.6.
Preparing to unpack .../libapache2-mod-php5.6_5.6.31-6+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking libapache2-mod-php5.6 (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.0-common.
Preparing to unpack .../php7.0-common_7.0.24-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.0-common (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.0-json.
Preparing to unpack .../php7.0-json_7.0.24-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.0-json (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.0-opcache.
Preparing to unpack .../php7.0-opcache_7.0.24-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.0-opcache (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.0-readline.
Preparing to unpack .../php7.0-readline_7.0.24-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.0-readline (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.0-cli.
Preparing to unpack .../php7.0-cli_7.0.24-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.0-cli (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package libapache2-mod-php7.0.
Preparing to unpack .../libapache2-mod-php7.0_7.0.24-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking libapache2-mod-php7.0 (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.1-common.
Preparing to unpack .../php7.1-common_7.1.10-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.1-common (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.1-json.
Preparing to unpack .../php7.1-json_7.1.10-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.1-json (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.1-opcache.
Preparing to unpack .../php7.1-opcache_7.1.10-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.1-opcache (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.1-readline.
Preparing to unpack .../php7.1-readline_7.1.10-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.1-readline (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.1-cli.
Preparing to unpack .../php7.1-cli_7.1.10-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking php7.1-cli (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package libapache2-mod-php7.1.
Preparing to unpack .../libapache2-mod-php7.1_7.1.10-1+ubuntu16.04.1+deb.sury.org+1_amd64.deb ...
Unpacking libapache2-mod-php7.1 (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php5.6.
Preparing to unpack .../php5.6_5.6.31-6+ubuntu16.04.1+deb.sury.org+1_all.deb ...
Unpacking php5.6 (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.0.
Preparing to unpack .../php7.0_7.0.24-1+ubuntu16.04.1+deb.sury.org+1_all.deb ...
Unpacking php7.0 (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Selecting previously unselected package php7.1.
Preparing to unpack .../php7.1_7.1.10-1+ubuntu16.04.1+deb.sury.org+1_all.deb ...
Unpacking php7.1 (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up libssl1.1:amd64 (1.1.0f-2~ubuntu16.04.1+deb.sury.org+1) ...
Setting up php-common (1:55+ubuntu16.04.1+deb.sury.org+1) ...
Setting up php5.6-common (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/5.6/mods-available/calendar.ini with new version

Creating config file /etc/php/5.6/mods-available/ctype.ini with new version

Creating config file /etc/php/5.6/mods-available/exif.ini with new version

Creating config file /etc/php/5.6/mods-available/fileinfo.ini with new version

Creating config file /etc/php/5.6/mods-available/ftp.ini with new version

Creating config file /etc/php/5.6/mods-available/gettext.ini with new version

Creating config file /etc/php/5.6/mods-available/iconv.ini with new version

Creating config file /etc/php/5.6/mods-available/pdo.ini with new version

Creating config file /etc/php/5.6/mods-available/phar.ini with new version

Creating config file /etc/php/5.6/mods-available/posix.ini with new version

Creating config file /etc/php/5.6/mods-available/shmop.ini with new version

Creating config file /etc/php/5.6/mods-available/sockets.ini with new version

Creating config file /etc/php/5.6/mods-available/sysvmsg.ini with new version

Creating config file /etc/php/5.6/mods-available/sysvsem.ini with new version

Creating config file /etc/php/5.6/mods-available/sysvshm.ini with new version

Creating config file /etc/php/5.6/mods-available/tokenizer.ini with new version
Setting up php5.6-json (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/5.6/mods-available/json.ini with new version
Setting up php5.6-opcache (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/5.6/mods-available/opcache.ini with new version
Setting up php5.6-readline (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/5.6/mods-available/readline.ini with new version
Setting up php5.6-cli (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
update-alternatives: using /usr/bin/php5.6 to provide /usr/bin/php (php) in auto mode
update-alternatives: using /usr/bin/phar5.6 to provide /usr/bin/phar (phar) in auto mode
update-alternatives: using /usr/bin/phar.phar5.6 to provide /usr/bin/phar.phar (phar.phar) in auto mode

Creating config file /etc/php/5.6/cli/php.ini with new version
Setting up libapache2-mod-php5.6 (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/5.6/apache2/php.ini with new version
Module mpm_event disabled.
Enabling module mpm_prefork.
apache2_switch_mpm Switch to prefork
apache2_invoke: Enable module php5.6
Setting up php7.0-common (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.0/mods-available/calendar.ini with new version

Creating config file /etc/php/7.0/mods-available/ctype.ini with new version

Creating config file /etc/php/7.0/mods-available/exif.ini with new version

Creating config file /etc/php/7.0/mods-available/fileinfo.ini with new version

Creating config file /etc/php/7.0/mods-available/ftp.ini with new version

Creating config file /etc/php/7.0/mods-available/gettext.ini with new version

Creating config file /etc/php/7.0/mods-available/iconv.ini with new version

Creating config file /etc/php/7.0/mods-available/pdo.ini with new version

Creating config file /etc/php/7.0/mods-available/phar.ini with new version

Creating config file /etc/php/7.0/mods-available/posix.ini with new version

Creating config file /etc/php/7.0/mods-available/shmop.ini with new version

Creating config file /etc/php/7.0/mods-available/sockets.ini with new version

Creating config file /etc/php/7.0/mods-available/sysvmsg.ini with new version

Creating config file /etc/php/7.0/mods-available/sysvsem.ini with new version

Creating config file /etc/php/7.0/mods-available/sysvshm.ini with new version

Creating config file /etc/php/7.0/mods-available/tokenizer.ini with new version
Setting up php7.0-json (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.0/mods-available/json.ini with new version
Setting up php7.0-opcache (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.0/mods-available/opcache.ini with new version
Setting up php7.0-readline (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.0/mods-available/readline.ini with new version
Setting up php7.0-cli (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
update-alternatives: using /usr/bin/php7.0 to provide /usr/bin/php (php) in auto mode
update-alternatives: using /usr/bin/phar7.0 to provide /usr/bin/phar (phar) in auto mode
update-alternatives: using /usr/bin/phar.phar7.0 to provide /usr/bin/phar.phar (phar.phar) in auto mode

Creating config file /etc/php/7.0/cli/php.ini with new version
Setting up libapache2-mod-php7.0 (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.0/apache2/php.ini with new version
libapache2-mod-php7.0: php5.6 module already enabled, not enabling PHP 7.0
Setting up php7.1-common (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.1/mods-available/calendar.ini with new version

Creating config file /etc/php/7.1/mods-available/ctype.ini with new version

Creating config file /etc/php/7.1/mods-available/exif.ini with new version

Creating config file /etc/php/7.1/mods-available/fileinfo.ini with new version

Creating config file /etc/php/7.1/mods-available/ftp.ini with new version

Creating config file /etc/php/7.1/mods-available/gettext.ini with new version

Creating config file /etc/php/7.1/mods-available/iconv.ini with new version

Creating config file /etc/php/7.1/mods-available/pdo.ini with new version

Creating config file /etc/php/7.1/mods-available/phar.ini with new version

Creating config file /etc/php/7.1/mods-available/posix.ini with new version

Creating config file /etc/php/7.1/mods-available/shmop.ini with new version

Creating config file /etc/php/7.1/mods-available/sockets.ini with new version

Creating config file /etc/php/7.1/mods-available/sysvmsg.ini with new version

Creating config file /etc/php/7.1/mods-available/sysvsem.ini with new version

Creating config file /etc/php/7.1/mods-available/sysvshm.ini with new version

Creating config file /etc/php/7.1/mods-available/tokenizer.ini with new version
Setting up php7.1-json (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.1/mods-available/json.ini with new version
Setting up php7.1-opcache (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.1/mods-available/opcache.ini with new version
Setting up php7.1-readline (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.1/mods-available/readline.ini with new version
Setting up php7.1-cli (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
update-alternatives: using /usr/bin/php7.1 to provide /usr/bin/php (php) in auto mode
update-alternatives: using /usr/bin/phar7.1 to provide /usr/bin/phar (phar) in auto mode
update-alternatives: using /usr/bin/phar.phar7.1 to provide /usr/bin/phar.phar (phar.phar) in auto mode

Creating config file /etc/php/7.1/cli/php.ini with new version
Setting up libapache2-mod-php7.1 (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...

Creating config file /etc/php/7.1/apache2/php.ini with new version
libapache2-mod-php7.1: php5.6 module already enabled, not enabling PHP 7.1
Setting up php5.6 (5.6.31-6+ubuntu16.04.1+deb.sury.org+1) ...
Setting up php7.0 (7.0.24-1+ubuntu16.04.1+deb.sury.org+1) ...
Setting up php7.1 (7.1.10-1+ubuntu16.04.1+deb.sury.org+1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ... 
```

Enter fullscreen mode Exit fullscreen mode