# sudo apt-get 安装 asciinema

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-asciinema-136>

```
 Son 15 Okt - 14:29  ~/Vagrant 
 @patrick  vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Checking if box 'geerlingguy/ubuntu1604' is up to date...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
==> default: Machine booted and ready!
[default] GuestAdditions 5.1.28 running --- OK.
==> default: Checking for guest additions in VM...
==> default: [vagrant-hostsupdater] Checking for host entries
==> default: Configuring and enabling network interfaces...
==> default: Mounting shared folders...
    default: /vagrant => /home/patrick/Vagrant
==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> default: flag to force provisioning. Provisioners marked to run always will still run. 
```

Enter fullscreen mode Exit fullscreen mode

```
 Son 15 Okt - 14:29  ~/Vagrant 
 @patrick  vagrant ssh
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Sun Oct 15 10:17:26 2017 from 10.0.2.2 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-add-repository ppa:zanchey/asciinema

 More info: https://launchpad.net/~zanchey/+archive/ubuntu/asciinema
Press [ENTER] to continue or ctrl-c to cancel adding it

gpg: keyring `/tmp/tmp_mk7etn4/secring.gpg' created
gpg: keyring `/tmp/tmp_mk7etn4/pubring.gpg' created
gpg: requesting key 0F833EAD from hkp server keyserver.ubuntu.com
gpg: /tmp/tmp_mk7etn4/trustdb.gpg: trustdb created
gpg: key 0F833EAD: public key "Launchpad PPA for David Adam" imported
gpg: no ultimately trusted keys found
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
OK
➜  ~ sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade
Hit:1 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]                                                                                                                   
Ign:3 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge InRelease                                                                                                            
Hit:4 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                                                                                                         
Hit:5 http://webmin.mirror.somersettechsolutions.co.uk/repository sarge Release                                                                                                              
Hit:6 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                                                                                                         
Hit:7 http://us.archive.ubuntu.com/ubuntu xenial InRelease                                                                
Hit:9 http://ppa.launchpad.net/nijel/phpmyadmin/ubuntu xenial InRelease                                                   
Ign:10 http://download.webmin.com/download/repository sarge InRelease                                                    
Get:11 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                                                                             
Hit:12 http://ppa.launchpad.net/ondrej/apache2/ubuntu xenial InRelease                                                                                   
Hit:13 http://ppa.launchpad.net/ondrej/php/ubuntu xenial InRelease                                                                                                   
Hit:14 http://ppa.launchpad.net/webupd8team/java/ubuntu xenial InRelease                                                                                            
Hit:15 http://download.webmin.com/download/repository sarge Release                                                                    
Get:16 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial InRelease [17.5 kB]                                                    
Get:17 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]         
Get:19 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial/main amd64 Packages [448 B]
Get:20 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial/main i386 Packages [448 B]            
Get:21 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial/main Translation-en [200 B]
Fetched 325 kB in 1s (245 kB/s)                      
Reading package lists... Done
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
➜  ~  sudo apt-get install asciinema                                         
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  asciinema
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 14.2 kB of archives.
After this operation, 97.3 kB of additional disk space will be used.
Get:1 http://ppa.launchpad.net/zanchey/asciinema/ubuntu xenial/main amd64 asciinema all 1.4.0-1 [14.2 kB]
Fetched 14.2 kB in 0s (84.5 kB/s)
Selecting previously unselected package asciinema.
(Reading database ... 151919 files and directories currently installed.)
Preparing to unpack .../asciinema_1.4.0-1_all.deb ...
Unpacking asciinema (1.4.0-1) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up asciinema (1.4.0-1) ... 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ asciinema    
usage: asciinema [-h] [--version] {rec,play,upload,auth} ...

Record and share your terminal sessions, the right way.

positional arguments:
  {rec,play,upload,auth}
    rec                 Record terminal session
    play                Replay terminal session
    upload              Upload locally saved terminal session to asciinema.org
    auth                Manage recordings on asciinema.org account

optional arguments:
  -h, --help            show this help message and exit
  --version             show program's version number and exit

example usage:
  Record terminal and upload it to asciinema.org:
    asciinema rec
  Record terminal to local file:
    asciinema rec demo.json
  Record terminal and upload it to asciinema.org, specifying title:
    asciinema rec -t "My git tutorial"
  Record terminal to local file, "trimming" longer pauses to max 2.5 sec:
    asciinema rec -w 2.5 demo.json
  Replay terminal recording from local file:
    asciinema play demo.json
  Replay terminal recording hosted on asciinema.org:
    asciinema play https://asciinema.org/a/difqlgx86ym6emrmd8u62yqu8

For help on a specific command run:
  asciinema <command> -h 
```

Enter fullscreen mode Exit fullscreen mode

举例

```
➜  ~ asciinema rec -t "Vagrant Box"
~ Asciicast recording started.
~ Hit Ctrl-D or type "exit" to finish.
➜  ~ emacs25
➜  ~ vim
➜  ~ vifm
➜  ~ mc 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ exit
~ Asciicast recording finished.
~ Press <Enter> to upload, <Ctrl-C> to cancel.

https://asciinema.org/a/lyjcYVShTE3rv8R3wQUgeKiDo 
```

Enter fullscreen mode Exit fullscreen mode