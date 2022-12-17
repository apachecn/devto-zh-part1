# 流浪包和流浪箱添加

> 原文：<https://dev.to/psnebc/vagrant-package-and-vagrant-box-add-8of>

➜把流浪者盒子包在文件夹里~/流浪者

```
 Son 15 Okt - 12:22  ~/Vagrant 
@patrick  vagrant package
==> default: Clearing any previously set forwarded ports...
==> default: Exporting VM...
==> default: Compressing package to: /home/patrick/Vagrant/package.box 
```

Enter fullscreen mode Exit fullscreen mode

➜将我箱子里的打包箱重命名为“流浪-打包箱-psnc-01 . box”
➜列出所有流浪箱

```
 Son 15 Okt - 12:39  ~/Vagrant 
@patrick  vagrant box list
geerlingguy/ubuntu1604 (virtualbox, 1.1.5)
geerlingguy/ubuntu1604 (virtualbox, 1.1.6) 
```

Enter fullscreen mode Exit fullscreen mode

➜从~/游民文件夹
添加新打包的游民箱

```
Son 15 Okt - 12:41  ~/Vagrant 
@patrick  vagrant box add psnc vagrant-packaged-box-psnc-01.box       
==> box: Box file was not detected as metadata. Adding it directly...
==> box: Adding box 'psnc' (v0) for provider: 
    box: Unpacking necessary files from: file:///home/patrick/Vagrant/vagrant-packaged-box-psnc-01.box
==> box: Successfully added box 'psnc' (v0) for 'virtualbox'! 
```

Enter fullscreen mode Exit fullscreen mode

➜流浪箱列表显示新添加的流浪箱“psnc”

```
Son 15 Okt - 12:42  ~/Vagrant 
@patrick  vagrant box list                                     
geerlingguy/ubuntu1604 (virtualbox, 1.1.5)
geerlingguy/ubuntu1604 (virtualbox, 1.1.6)
psnc                   (virtualbox, 0) 
```

Enter fullscreen mode Exit fullscreen mode

➜在里面新建了一个文件夹~/流浪者

```
Son 15 Okt - 12:42  ~/Vagrant 
@patrick  mkdir psnc 
```

Enter fullscreen mode Exit fullscreen mode

➜更改目录(光盘)

```
Son 15 Okt - 12:42  ~/Vagrant 
@patrick  cd psnc 
```

Enter fullscreen mode Exit fullscreen mode

➜创建一个新的流浪者文件

```
Son 15 Okt - 12:43  ~/Vagrant/psnc 
@patrick  vagrant init
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant. 
```

Enter fullscreen mode Exit fullscreen mode

➜复制一个以前构建的流浪者文件到~/流浪者/psnc/
➜把“流浪者文件”里面的流浪者框改成“psnc”
➜config . VM . box = " psnc "

➜启动了流浪箱

```
Son 15 Okt - 12:43  ~/Vagrant/psnc 
@patrick  vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'psnc'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: psnc_default_1508064313820_91403
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
```

Enter fullscreen mode Exit fullscreen mode

➜登录

```
Son 15 Okt - 12:45  ~/Vagrant/psnc 
@patrick  vagrant ssh
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.4.0-97-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Sun Oct 15 10:17:26 2017 from 10.0.2.2 
```

Enter fullscreen mode Exit fullscreen mode

➜出口

```
➜  ~ exit Connection to 127.0.0.1 closed. 
```

Enter fullscreen mode Exit fullscreen mode

➜关闭流浪盒

```
➜ Son 15 Okt - 12:48  ~/Vagrant/psnc 
@patrick  vagrant halt
==> default: Attempting graceful shutdown of VM...
==> default: [vagrant-hostsupdater] Removing hosts 
```

Enter fullscreen mode Exit fullscreen mode

```
 Son 15 Okt - 12:49  ~/Vagrant/psnc 
 @patrick  vagrant help Usage: vagrant [options] <command> [<args>]

    -v, --version                    Print the version and exit.
    -h, --help                       Print this help.

Common commands:
     box             manages boxes: installation, removal, etc.
     connect         connect to a remotely shared Vagrant environment
     destroy         stops and deletes all traces of the vagrant machine
     exec executes commands in virtual machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help shows the help for a subcommand
     hostmanager     plugin: vagrant-hostmanager: manages the /etc/hosts file within a multi-machine environment
     hostsupdater    
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login           log in to HashiCorp's Atlas
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     share           share your Vagrant environment with anyone in the world
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     vbguest         plugin: vagrant-vbguest: install VirtualBox Guest Additions to the machine
     version         prints current and latest Vagrant version

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`. 
```

Enter fullscreen mode Exit fullscreen mode