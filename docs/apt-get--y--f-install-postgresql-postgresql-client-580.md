# apt-get -y -f 安装 postgresql

> 原文：<https://dev.to/psnebc/apt-get--y--f-install-postgresql-postgresql-client-580>

### 通过 Webmin 服务器面板安装

```
Installing package(s) with command apt-get -y -f install postgresql postgresql-client ..

Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  postgresql-9.5 postgresql-client-9.5 postgresql-client-common
  postgresql-common
Suggested packages:
  postgresql-doc locales-all postgresql-doc-9.5
Recommended packages:
  postgresql-contrib-9.5 sysstat
The following NEW packages will be installed:
  postgresql postgresql-9.5 postgresql-client postgresql-client-9.5
  postgresql-client-common postgresql-common
0 upgraded, 6 newly installed, 0 to remove and 0 not upgraded.
Need to get 4057 kB of archives.
After this operation, 16.2 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 postgresql-client-common all 173 [28.3 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 postgresql-client-9.5 amd64 9.5.7-0ubuntu0.16.04 [860 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/main amd64 postgresql-common all 173 [153 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 postgresql-9.5 amd64 9.5.7-0ubuntu0.16.04 [3004 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/main amd64 postgresql all 9.5+173 [5336 B]
Get:6 http://archive.ubuntu.com/ubuntu xenial/main amd64 postgresql-client all 9.5+173 [5350 B]
Preconfiguring packages ...
Fetched 4057 kB in 12s (322 kB/s)
Selecting previously unselected package postgresql-client-common.
(Reading database ... 
(Reading database ... 5%
(Reading database ... 10%
(Reading database ... 15%
(Reading database ... 20%
(Reading database ... 25%
(Reading database ... 30%
(Reading database ... 35%
(Reading database ... 40%
(Reading database ... 45%
(Reading database ... 50%
(Reading database ... 55%
(Reading database ... 60%
(Reading database ... 65%
(Reading database ... 70%
(Reading database ... 75%
(Reading database ... 80%
(Reading database ... 85%
(Reading database ... 90%
(Reading database ... 95%
(Reading database ... 100%
(Reading database ... 247399 files and directories currently installed.)
Preparing to unpack .../postgresql-client-common_173_all.deb ...
Unpacking postgresql-client-common (173) ...
Selecting previously unselected package postgresql-client-9.5.
Preparing to unpack .../postgresql-client-9.5_9.5.7-0ubuntu0.16.04_amd64.deb ...
Unpacking postgresql-client-9.5 (9.5.7-0ubuntu0.16.04) ...
Selecting previously unselected package postgresql-common.
Preparing to unpack .../postgresql-common_173_all.deb ...
Adding 'diversion of /usr/bin/pg_config to /usr/bin/pg_config.libpq-dev by postgresql-common'
Unpacking postgresql-common (173) ...
Selecting previously unselected package postgresql-9.5.
Preparing to unpack .../postgresql-9.5_9.5.7-0ubuntu0.16.04_amd64.deb ...
Unpacking postgresql-9.5 (9.5.7-0ubuntu0.16.04) ...
Selecting previously unselected package postgresql.
Preparing to unpack .../postgresql_9.5+173_all.deb ...
Unpacking postgresql (9.5+173) ...
Selecting previously unselected package postgresql-client.
Preparing to unpack .../postgresql-client_9.5+173_all.deb ...
Unpacking postgresql-client (9.5+173) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for systemd (229-4ubuntu17) ...
Processing triggers for ureadahead (0.100.0-19) ...
Setting up postgresql-client-common (173) ...
Setting up postgresql-client-9.5 (9.5.7-0ubuntu0.16.04) ...
update-alternatives: using /usr/share/postgresql/9.5/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
Setting up postgresql-common (173) ...
supported-versions: WARNING! Unknown distribution: linuxmint
ubuntu found in ID_LIKE, treating as Ubuntu
supported-versions: WARNING: Unknown Ubuntu release: 18.1
Adding user postgres to group ssl-cert

Creating config file /etc/postgresql-common/createcluster.conf with new version

Creating config file /etc/logrotate.d/postgresql-common with new version
Building PostgreSQL dictionaries from installed myspell/hunspell packages...
  en_us
Removing obsolete dictionary files:
Setting up postgresql-9.5 (9.5.7-0ubuntu0.16.04) ...
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Unescaped left brace in regex is deprecated, passed through in regex; marked by <-- HERE in m/(?<!\\)\${ <-- HERE ([^}]+)}/ at /usr/sbin/pam_getenv line 78.
Creating new cluster 9.5/main ...
  config /etc/postgresql/9.5/main
  data   /var/lib/postgresql/9.5/main
  locale en_US.UTF-8
  socket /var/run/postgresql
  port   5432
update-alternatives: using /usr/share/postgresql/9.5/man/man1/postmaster.1.gz to provide /usr/share/man/man1/postmaster.1.gz (postmaster.1.gz) in auto mode
Setting up postgresql (9.5+173) ...
Setting up postgresql-client (9.5+173) ...
Processing triggers for systemd (229-4ubuntu17) ...
Processing triggers for ureadahead (0.100.0-19) ...

.. install complete.

Package postgresql-client-common was installed successfully
Package Details
Description     Unknown
Package     postgresql-client-common    Class   P-T
Version     173 Vendor  Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture    all Installed   Unknown
Package postgresql-client-9.5 was installed successfully
Package Details
Description     Unknown
Package     postgresql-client-9.5   Class   P-T
Version     9.5.2-1 Vendor  Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture    amd64   Installed   Unknown
Package postgresql-common was installed successfully
Package Details
Description     Unknown
Package     postgresql-common   Class   P-T
Version     173 Vendor  Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture    all Installed   Unknown
Package postgresql-9.5 was installed successfully
Package Details
Description     Unknown
Package     postgresql-9.5  Class   P-T
Version     9.5.2-1 Vendor  Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture    amd64   Installed   Unknown
Package postgresql was installed successfully
Package Details
Description     Unknown
Package     postgresql  Class   P-T
Version     9.5+173 Vendor  Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture    all Installed   Unknown
Package postgresql-client was installed successfully
Package Details
Description     Unknown
Package     postgresql-client   Class   P-T
Version     9.5+173 Vendor  Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture    all Installed   Unknown 
```

Enter fullscreen mode Exit fullscreen mode