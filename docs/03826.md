# sudo apt-get 安装程序

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-htop-8o2>

```
 Fre 20 Okt - 19:27  ~ 
 @patrick  cd Vagrant/psnc 

 Fre 20 Okt - 19:27  ~/Vagrant/psnc 
 @patrick  vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Clearing any previously set forwarded ports...
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
    default: /vagrant => /home/patrick/Vagrant/psnc
==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> default: flag to force provisioning. Provisioners marked to run always will still run.

 Fre 20 Okt - 19:28  ~/Vagrant/psnc 
 @patrick  vagrant ssh
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Thu Oct 19 21:23:51 2017 from 10.0.2.2
➜  ~ sudo apt-get install htop
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  htop
0 upgraded, 1 newly installed, 0 to remove and 5 not upgraded.
Need to get 76.4 kB of archives.
After this operation, 215 kB of additional disk space will be used.
Get:1 http://us.archive.ubuntu.com/ubuntu xenial-updates/universe amd64 htop amd64 2.0.1-1ubuntu1 [76.4 kB]
Fetched 76.4 kB in 0s (107 kB/s)
Selecting previously unselected package htop.
(Reading database ... 155846 files and directories currently installed.)
Preparing to unpack .../htop_2.0.1-1ubuntu1_amd64.deb ...
Unpacking htop (2.0.1-1ubuntu1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up htop (2.0.1-1ubuntu1) ...
➜  ~ 
```

Enter fullscreen mode Exit fullscreen mode