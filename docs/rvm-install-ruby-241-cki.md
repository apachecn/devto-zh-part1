# rvm 安装 ruby-2.4.1

> 原文：<https://dev.to/psnebc/rvm-install-ruby-241-cki>

## 进行回顾

### 没有保证

```
vagrant@vagrant:~$ curl -L get.rvm.io | bash -s stable
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   194  100   194    0     0    694      0 --:--:-- --:--:-- --:--:--   695
100 24090  100 24090    0     0  29156      0 --:--:-- --:--:-- --:--:--  832k
awk: symbol lookup error: /usr/local/lib/libreadline.so.6: undefined symbol: UP
vagrant@vagrant:~$ gpg2 --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
The program 'gpg2' is currently not installed. You can install it by typing:
sudo apt install gnupg2 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ sudo apt-get install gnupg2
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  dirmngr gnupg-agent libassuan0 libksba8 libnpth0 pinentry-curses
Suggested packages:
  gnupg-doc parcimonie xloadimage pinentry-doc
The following NEW packages will be installed:
  dirmngr gnupg-agent gnupg2 libassuan0 libksba8 libnpth0 pinentry-curses
0 upgraded, 7 newly installed, 0 to remove and 2 not upgraded.
Need to get 1,394 kB of archives.
After this operation, 4,111 kB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libassuan0 amd64 2.4.2-2 [34.6 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 pinentry-curses amd64 0.9.7-3 [31.2 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 libnpth0 amd64 1.2-3 [7,998 B]
Get:4 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 gnupg-agent amd64 2.1.11-6ubuntu2 [239 kB]
Get:5 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 libksba8 amd64 1.3.3-1ubuntu0.16.04.1 [90.2 kB]
Get:6 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 gnupg2 amd64 2.1.11-6ubuntu2 [756 kB]
Get:7 http://us.archive.ubuntu.com/ubuntu xenial/main amd64 dirmngr amd64 2.1.11-6ubuntu2 [235 kB]
Fetched 1,394 kB in 1s (928 kB/s)
Selecting previously unselected package libassuan0:amd64.
(Reading database ... 153852 files and directories currently installed.)
Preparing to unpack .../libassuan0_2.4.2-2_amd64.deb ...
Unpacking libassuan0:amd64 (2.4.2-2) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Setting up libassuan0:amd64 (2.4.2-2) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Selecting previously unselected package pinentry-curses.
(Reading database ... 153857 files and directories currently installed.)
Preparing to unpack .../pinentry-curses_0.9.7-3_amd64.deb ...
Unpacking pinentry-curses (0.9.7-3) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up pinentry-curses (0.9.7-3) ...
Selecting previously unselected package libnpth0:amd64.
(Reading database ... 153865 files and directories currently installed.)
Preparing to unpack .../libnpth0_1.2-3_amd64.deb ...
Unpacking libnpth0:amd64 (1.2-3) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Setting up libnpth0:amd64 (1.2-3) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Selecting previously unselected package gnupg-agent.
(Reading database ... 153870 files and directories currently installed.)
Preparing to unpack .../gnupg-agent_2.1.11-6ubuntu2_amd64.deb ...
Unpacking gnupg-agent (2.1.11-6ubuntu2) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up gnupg-agent (2.1.11-6ubuntu2) ...
Selecting previously unselected package libksba8:amd64.
(Reading database ... 153891 files and directories currently installed.)
Preparing to unpack .../libksba8_1.3.3-1ubuntu0.16.04.1_amd64.deb ...
Unpacking libksba8:amd64 (1.3.3-1ubuntu0.16.04.1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Setting up libksba8:amd64 (1.3.3-1ubuntu0.16.04.1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Selecting previously unselected package gnupg2.
(Reading database ... 153899 files and directories currently installed.)
Preparing to unpack .../gnupg2_2.1.11-6ubuntu2_amd64.deb ...
Unpacking gnupg2 (2.1.11-6ubuntu2) ...
Processing triggers for install-info (6.1.0.dfsg.1-5) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up gnupg2 (2.1.11-6ubuntu2) ...
Selecting previously unselected package dirmngr.
(Reading database ... 153962 files and directories currently installed.)
Preparing to unpack .../dirmngr_2.1.11-6ubuntu2_amd64.deb ...
Unpacking dirmngr (2.1.11-6ubuntu2) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up dirmngr (2.1.11-6ubuntu2) ... 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ curl -L get.rvm.io | bash -s stable
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   194  100   194    0     0    260      0 --:--:-- --:--:-- --:--:--   260
100 24090  100 24090    0     0  13095      0  0:00:01  0:00:01 --:--:-- 40419
Downloading https://github.com/rvm/rvm/archive/1.29.3.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.29.3/1.29.3.tar.gz.asc
gpg: Signature made Sun 10 Sep 2017 08:59:21 PM UTC using RSA key ID BF04FF17
gpg: Good signature from "Michal Papis (RVM signing) <mpapis@gmail.com>" [unknown]
gpg:                 aka "Michal Papis <michal.papis@toptal.com>" [unknown]
gpg:                 aka "[jpeg image of size 5015]" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 409B 6B17 96C2 7546 2A17  0311 3804 BB82 D39D C0E3
     Subkey fingerprint: 62C9 E5F4 DA30 0D94 AC36  166B E206 C29F BF04 FF17
GPG verified '/home/vagrant/.rvm/archives/rvm-1.29.3.tgz'

Upgrading the RVM installation in /home/vagrant/.rvm/                                                                       
    RVM PATH line found in /home/vagrant/.mkshrc /home/vagrant/.profile /home/vagrant/.bashrc /home/vagrant/.zshrc.
    RVM sourcing line found in /home/vagrant/.profile /home/vagrant/.bash_profile /home/vagrant/.zlogin.
    Installing rvm gem in 1 gemsets.
    Installing gem-wrappers gem in 1 gemsets.
    Regenerating gem wrappers in 1 rubies.
Upgrade of RVM in /home/vagrant/.rvm/ is complete.

Upgrade Notes:

  * No new notes to display. 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ gpg2 --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
gpg: key D39DC0E3: "Michal Papis (RVM signing) <mpapis@gmail.com>" 22 new signatures
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:         new signatures: 22 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ rvm install 2.4.1
Searching for binary rubies, this might take some time.
Found remote file https://rvm_io.global.ssl.fastly.net/binaries/ubuntu/16.04/x86_64/ruby-2.4.1.tar.bz2
Checking requirements for ubuntu.
Requirements installation successful.
ruby-2.4.1 - #configure
ruby-2.4.1 - #download
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 16.4M  100 16.4M    0     0  1778k      0  0:00:09  0:00:09 --:--:-- 2335k
ruby-2.4.1 - #validate archive
ruby-2.4.1 - #extract
ruby-2.4.1 - #validate binary
ruby-2.4.1 - #setup
ruby-2.4.1 - #gemset created /home/vagrant/.rvm/gems/ruby-2.4.1@global
ruby-2.4.1 - #importing gemset /home/vagrant/.rvm/gemsets/global.gems...................................
ruby-2.4.1 - #generating global wrappers........
ruby-2.4.1 - #gemset created /home/vagrant/.rvm/gems/ruby-2.4.1
ruby-2.4.1 - #importing gemsetfile /home/vagrant/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.4.1 - #generating default wrappers........ 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ rvm use 2.4.1
Using /home/vagrant/.rvm/gems/ruby-2.4.1 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ rvm | head -n 1
Ruby enVironment Manager 1.29.3 (latest) (c) 2009-2017 Michal Papis, Piotr Kuczynski, Wayne E. Seguin 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ rvm list known
# MRI Rubies
[ruby-]1.8.6[-p420]
[ruby-]1.8.7[-head] # security released on head
[ruby-]1.9.1[-p431]
[ruby-]1.9.2[-p330]
[ruby-]1.9.3[-p551]
[ruby-]2.0.0[-p648]
[ruby-]2.1[.10]
[ruby-]2.2[.7]
[ruby-]2.3[.4]
[ruby-]2.4[.1]
ruby-head

# for forks use: rvm install ruby-head-<name> --url https://github.com/github/ruby.git --branch 2.2

# JRuby
jruby-1.6[.8]
jruby-1.7[.27]
jruby[-9.1.13.0]
jruby-head

# Rubinius
rbx-1[.4.3]
rbx-2.3[.0]
rbx-2.4[.1]
rbx-2[.5.8]
rbx-3[.84]
rbx-head

# Opal
opal

# Minimalistic ruby implementation - ISO 30170:2012
mruby-1.0.0
mruby-1.1.0
mruby-1.2.0
mruby-1[.3.0]
mruby[-head]

# Ruby Enterprise Edition
ree-1.8.6
/home/vagrant/.rvm/config/known 
```

Enter fullscreen mode Exit fullscreen mode

```
vagrant@vagrant:~$ ruby -v                                                                                                                                                                    
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-linux] 
```

Enter fullscreen mode Exit fullscreen mode

```
 ~ /bin/zsh --login
➜  ~ ruby -v          
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-linux] 
```

Enter fullscreen mode Exit fullscreen mode