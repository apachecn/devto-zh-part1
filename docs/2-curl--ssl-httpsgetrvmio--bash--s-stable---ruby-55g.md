# (2)curl-sSL https://get . rvm . io | bash-s stable-ruby

> 原文：<https://dev.to/psnebc/2-curl--ssl-httpsgetrvmio--bash--s-stable---ruby-55g>

### 注意 Bug

*   [https://stack overflow . com/questions/44555760/cant-install-ruby-rvm-on-Ubuntu-16-04-due-to-gpg-bug](https://stackoverflow.com/questions/44555760/cannt-install-ruby-rvm-on-ubuntu-16-04-due-to-gpg-bug)

```
#
# error here
# GPG signature verification failed
#
➜  ~ \curl -sSL https://get.rvm.io | bash -s stable --ruby
Downloading https://github.com/rvm/rvm/archive/1.29.3.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.29.3/1.29.3.tar.gz.asc
gpg: Signature made Sun 10 Sep 2017 10:59:21 PM CEST using RSA key ID BF04FF17
gpg: Can't check signature: No public key
Warning, RVM 1.26.0 introduces signed releases and automated check of signatures when GPG software found. Assuming you trust Michal Papis import the mpapis public key (downloading the signatures).

GPG signature verification failed for '/home/psnc/.rvm/archives/rvm-1.29.3.tgz' - 'https://github.com/rvm/rvm/releases/download/1.29.3/1.29.3.tar.gz.asc'! Try to install GPG v2 and then fetch the public key:

    gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

or if it fails:

    command curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -

the key can be compared with:

    https://rvm.io/mpapis.asc
    https://keybase.io/mpapis

NOTE: GPG version 2.1.17 have a bug which cause failures during fetching keys from remote server. Please downgrade or upgrade to newer version (if available) or use the second method described above. 
```

Enter fullscreen mode Exit fullscreen mode

```
#
# error here
#
➜  ~ gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
gpg: keyserver receive failed: No keyserver available 
```

Enter fullscreen mode Exit fullscreen mode

```
#
# import public key
#
➜  ~ command curl -sSL https://rvm.io/mpapis.asc | gpg2 --import -
gpg: starting migration from earlier GnuPG versions
gpg: porting secret keys from '/home/psnc/.gnupg/secring.gpg' to gpg-agent
gpg: migration succeeded
gpg: /home/psnc/.gnupg/trustdb.gpg: trustdb created
gpg: key D39DC0E3: public key "Michal Papis (RVM signing) <mpapis@gmail.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg: no ultimately trusted keys found 
```

Enter fullscreen mode Exit fullscreen mode

```
 #
# error here
#
➜  ~ rvm --version
zsh: command not found: rvm 
```

Enter fullscreen mode Exit fullscreen mode

```
#
# Installation
#
➜  ~ \curl -sSL https://get.rvm.io | bash -s stable --ruby        
Downloading https://github.com/rvm/rvm/archive/1.29.3.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.29.3/1.29.3.tar.gz.asc
gpg: Signature made Sun 10 Sep 2017 10:59:21 PM CEST using RSA key ID BF04FF17
gpg: Good signature from "Michal Papis (RVM signing) <mpapis@gmail.com>" [unknown]
gpg:                 aka "Michal Papis <michal.papis@toptal.com>" [unknown]
gpg:                 aka "[jpeg image of size 5015]" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 409B 6B17 96C2 7546 2A17  0311 3804 BB82 D39D C0E3
     Subkey fingerprint: 62C9 E5F4 DA30 0D94 AC36  166B E206 C29F BF04 FF17
GPG verified '/home/psnc/.rvm/archives/rvm-1.29.3.tgz'

Installing RVM to /home/psnc/.rvm/
    Adding rvm PATH line to /home/psnc/.profile /home/psnc/.mkshrc /home/psnc/.bashrc /home/psnc/.zshrc.
    Adding rvm loading line to /home/psnc/.profile /home/psnc/.bash_profile /home/psnc/.zlogin.
Installation of RVM in /home/psnc/.rvm/ is almost complete:

  * To start using RVM you need to run `source /home/psnc/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows.
Ruby enVironment Manager 1.29.3 (latest) (c) 2009-2017 Michal Papis, Piotr Kuczynski, Wayne E. Seguin

Searching for binary rubies, this might take some time.
No binary rubies available for: mint/18.2/x86_64/ruby-2.4.1.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Checking requirements for mint.
Installing requirements for mint.
Updating system.....
Installing required packages: g++, libc6-dev, zlib1g-dev, libyaml-dev, libsqlite3-dev, sqlite3, autoconf, libgmp-dev, libgdbm-dev, libncurses5-dev, automake, libtool, libffi-dev, libgmp-dev, libreadline6-dev, libssl-dev................
Requirements installation successful.
Found user configured '-j' flag in 'rvm_make_flags', please note that RVM can detect number of CPU threads and set the '-j' flag automatically if you do not set it.
Installing Ruby from source to: /home/psnc/.rvm/rubies/ruby-2.4.1, this may take a while depending on your cpu(s)...
ruby-2.4.1 - #downloading ruby-2.4.1, this may take a while depending on your connection...
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11.9M  100 11.9M    0     0   698k      0  0:00:17  0:00:17 --:--:--  519k
ruby-2.4.1 - #extracting ruby-2.4.1 to /home/psnc/.rvm/src/ruby-2.4.1....
ruby-2.4.1 - #applying patch /home/psnc/.rvm/patches/ruby/2.4.1/random_c_using_NR_prefix.patch.
ruby-2.4.1 - #configuring..................................................................
ruby-2.4.1 - #post-configuration..
ruby-2.4.1 - #compiling................................................................................
ruby-2.4.1 - #installing...........
ruby-2.4.1 - #making binaries executable..
ruby-2.4.1 - #downloading rubygems-2.6.14
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  751k  100  751k    0     0   550k      0  0:00:01  0:00:01 --:--:--  550k
No checksum for downloaded archive, recording checksum in user configuration.
ruby-2.4.1 - #extracting rubygems-2.6.14....
ruby-2.4.1 - #removing old rubygems.........
ruby-2.4.1 - #installing rubygems-2.6.14...........................
ruby-2.4.1 - #gemset created /home/psnc/.rvm/gems/ruby-2.4.1@global
ruby-2.4.1 - #importing gemset /home/psnc/.rvm/gemsets/global.gems...............................................
ruby-2.4.1 - #generating global wrappers........
ruby-2.4.1 - #gemset created /home/psnc/.rvm/gems/ruby-2.4.1
ruby-2.4.1 - #importing gemsetfile /home/psnc/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.4.1 - #generating default wrappers........
ruby-2.4.1 - #adjusting #shebangs for (gem irb erb ri rdoc testrb rake).
Install of ruby-2.4.1 - #complete 
Ruby was built without documentation, to build it run: rvm docs generate-ri
Creating alias default for ruby-2.4.1...

  * To start using RVM you need to run `source /home/psnc/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows. 
```

Enter fullscreen mode Exit fullscreen mode

```
#
# To start using RVM you need to run `source /home/psnc/.rvm/scripts/rvm`
# in all your open shell windows, in rare cases you need to reopen all shell
# windows.
#
➜  ~ source /home/psnc/.rvm/scripts/rvm 
```

Enter fullscreen mode Exit fullscreen mode

```
#
# rvm version
#
➜  ~ rvm -v
rvm 1.29.3 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io] 
```

Enter fullscreen mode Exit fullscreen mode