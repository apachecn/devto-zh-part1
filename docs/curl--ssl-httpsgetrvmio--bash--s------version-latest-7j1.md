# curl-sSL https://get . rvm . io | bash-s--最新版本

> 原文：<https://dev.to/psnebc/curl--ssl-httpsgetrvmio--bash--s------version-latest-7j1>

```
 Die 17 Okt - 23:57  ~/Vagrant/psnc 
 @patrick  vagrant ssh
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Sun Oct 15 20:18:39 2017 from 10.0.2.2
➜  ~ curl -sSL https://get.rvm.io | bash -s -- --version latest
Downloading https://github.com/rvm/rvm/archive/1.29.3.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.29.3/1.29.3.tar.gz.asc
gpg: directory `/home/vagrant/.gnupg' created
gpg: new configuration file `/home/vagrant/.gnupg/gpg.conf' created
gpg: WARNING: options in `/home/vagrant/.gnupg/gpg.conf' are not yet active during this run
gpg: keyring `/home/vagrant/.gnupg/pubring.gpg' created
gpg: Signature made Sun 10 Sep 2017 08:59:21 PM UTC using RSA key ID BF04FF17
gpg: Can't check signature: public key not found
Warning, RVM 1.26.0 introduces signed releases and automated check of signatures when GPG software found. Assuming you trust Michal Papis import the mpapis public key (downloading the signatures).

GPG signature verification failed for '/home/vagrant/.rvm/archives/rvm-1.29.3.tgz' - 'https://github.com/rvm/rvm/releases/download/1.29.3/1.29.3.tar.gz.asc'! Try to install GPG v2 and then fetch the public key:

    gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3

or if it fails:

    command curl -sSL https://rvm.io/mpapis.asc | gpg --import -

the key can be compared with:

    https://rvm.io/mpapis.asc
    https://keybase.io/mpapis

NOTE: GPG version 2.1.17 have a bug which cause failures during fetching keys from remote server. Please downgrade or upgrade to newer version (if available) or use the second method described above. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ curl -sSL https://rvm.io/mpapis.asc | gpg --import -
gpg: keyring `/home/vagrant/.gnupg/secring.gpg' created
gpg: /home/vagrant/.gnupg/trustdb.gpg: trustdb created
gpg: key D39DC0E3: public key "Michal Papis (RVM signing) <mpapis@gmail.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
gpg: no ultimately trusted keys found
➜  ~ curl -sSL https://get.rvm.io | bash -s -- --version latest
Downloading https://github.com/rvm/rvm/archive/1.29.3.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.29.3/1.29.3.tar.gz.asc
gpg: Signature made Sun 10 Sep 2017 08:59:21 PM UTC using RSA key ID BF04FF17
gpg: Good signature from "Michal Papis (RVM signing) <mpapis@gmail.com>"
gpg:                 aka "Michal Papis <michal.papis@toptal.com>"
gpg:                 aka "[jpeg image of size 5015]"
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 409B 6B17 96C2 7546 2A17  0311 3804 BB82 D39D C0E3
     Subkey fingerprint: 62C9 E5F4 DA30 0D94 AC36  166B E206 C29F BF04 FF17
GPG verified '/home/vagrant/.rvm/archives/rvm-1.29.3.tgz'

Installing RVM to /home/vagrant/.rvm/
    Adding rvm PATH line to /home/vagrant/.profile /home/vagrant/.mkshrc /home/vagrant/.bashrc /home/vagrant/.zshrc.
    Adding rvm loading line to /home/vagrant/.profile /home/vagrant/.bash_profile /home/vagrant/.zlogin.
Installation of RVM in /home/vagrant/.rvm/ is almost complete:

  * To start using RVM you need to run `source /home/vagrant/.rvm/scripts/rvm`
    in all your open shell windows, in rare cases you need to reopen all shell windows. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ source /home/vagrant/.rvm/scripts/rvm 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ rvm -v
rvm 1.29.3 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io] 
```

Enter fullscreen mode Exit fullscreen mode