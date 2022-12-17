# 安装 phpmyadmin

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-phpmyadmin-213>

```
➜ ~ sudo add-apt-repository ppa:nijel/phpmyadmin 
```

Enter fullscreen mode Exit fullscreen mode

```
➜ ~ sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

```
➜ ~ sudo apt-get upgrade 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install phpmyadmin                                                                                                                                                          
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Suggested packages:
  www-browser
The following NEW packages will be installed:
  phpmyadmin
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/3,929 kB of archives.
After this operation, 24.9 MB of additional disk space will be used.
Preconfiguring packages ...
Determining localhost credentials from /etc/mysql/debian.cnf: succeeded.
Selecting previously unselected package phpmyadmin.
(Reading database ... 123799 files and directories currently installed.)
Preparing to unpack .../phpmyadmin_4%3a4.6.6-1+deb.cihar.com~xenial.2_all.deb ...
Unpacking phpmyadmin (4:4.6.6-1+deb.cihar.com~xenial.2) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up phpmyadmin (4:4.6.6-1+deb.cihar.com~xenial.2) ...
Determining localhost credentials from /etc/mysql/debian.cnf: succeeded.
dbconfig-common: writing config to /etc/dbconfig-common/phpmyadmin.conf
granting access to database phpmyadmin for phpmyadmin@localhost: success.
verifying access for phpmyadmin@localhost: success.
dbconfig-common: dumping mysql database phpmyadmin to /var/tmp/phpmyadmin.phpmyadmin.2017-10-15-08.56.mysql.6gKwP7.
database does not exist.
dbconfig-common: dropping old mysql database phpmyadmin.
dropping database phpmyadmin: database does not exist.
creating database phpmyadmin: success.
verifying database phpmyadmin exists: success.
populating database via sql...  done.
dbconfig-common: flushing administrative password
apache2_invoke: Enable configuration phpmyadmin 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo service apache2 restart 
```

Enter fullscreen mode Exit fullscreen mode

➜[http://IP/phpmyadmin](http://ip/phpmyadmin)T2】➜[http://192 . 168 . 33 . 10/phpmyadmin](http://192.168.33.10/phpmyadmin)