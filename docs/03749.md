# sudo apt-add-repository PPA:ansi ble/ansi ble

> 原文：<https://dev.to/psnebc/sudo-apt-add-repository-ppaansibleansible-e22>

```
➜  ~ sudo apt-add-repository ppa:ansible/ansible
You are about to add the following PPA:
 Ansible is a radically simple IT automation platform that makes your applications and systems easier to deploy. Avoid writing scripts or custom code to deploy and update your applications— automate in a language that approaches plain English, using SSH, with no agents to install on remote systems.

http://ansible.com/
 More info: https://launchpad.net/~ansible/+archive/ubuntu/ansible
Press Enter to continue or Ctrl+C to cancel

Executing: /tmp/tmp.Ot4iunfnWQ/gpg.1.sh --keyserver
hkp://keyserver.ubuntu.com:80
--recv-keys
7BB9C367
gpg: requesting key 7BB9C367 from hkp server keyserver.ubuntu.com
gpg: key 7BB9C367: public key "Launchpad PPA for Ansible, Inc." imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1) 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get update
Get:1 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial InRelease [18,0 kB]
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Hit:3 http://archive.ubuntu.com/ubuntu xenial InRelease            
Hit:4 http://archive.canonical.com/ubuntu xenial InRelease         
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Ign:6 http://ftp.fau.de/mint/packages sonya InRelease              
Hit:7 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease
Hit:8 http://ftp.fau.de/mint/packages sonya Release                
Get:9 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
Hit:10 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease
Hit:11 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease
Hit:12 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease
Get:13 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial/main Sources [608 B]
Get:15 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial/main amd64 Packages [560 B]
Get:16 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial/main i386 Packages [560 B]
Get:17 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial/main Translation-en [340 B]
Fetched 327 kB in 0s (433 kB/s)                                    
Reading package lists... Done 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ sudo apt-get install ansible
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  python-jinja2 python-setuptools python-yaml sshpass
Suggested packages:
  python-jinja2-doc python-setuptools-doc
The following NEW packages will be installed:
  ansible python-jinja2 python-setuptools python-yaml sshpass
0 upgraded, 5 newly installed, 0 to remove and 1 not upgraded.
Need to get 3.075 kB of archives.
After this operation, 24,0 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 python-jinja2 all 2.8-1 [109 kB]
Get:2 http://ppa.launchpad.net/ansible/ansible/ubuntu xenial/main amd64 ansible all 2.4.0.0-1ppa~xenial [2.682 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/main amd64 python-yaml amd64 3.11-3build1 [105 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/main amd64 python-setuptools all 20.7.0-1 [169 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/universe amd64 sshpass amd64 1.05-1 [10,5 kB]
Fetched 3.075 kB in 3s (995 kB/s)                                  
Selecting previously unselected package python-jinja2.
(Reading database ... 252155 files and directories currently installed.)
Preparing to unpack .../python-jinja2_2.8-1_all.deb ...
Unpacking python-jinja2 (2.8-1) ...
Selecting previously unselected package python-yaml.
Preparing to unpack .../python-yaml_3.11-3build1_amd64.deb ...
Unpacking python-yaml (3.11-3build1) ...
Selecting previously unselected package python-setuptools.
Preparing to unpack .../python-setuptools_20.7.0-1_all.deb ...
Unpacking python-setuptools (20.7.0-1) ...
Selecting previously unselected package sshpass.
Preparing to unpack .../sshpass_1.05-1_amd64.deb ...
Unpacking sshpass (1.05-1) ...
Selecting previously unselected package ansible.
Preparing to unpack .../ansible_2.4.0.0-1ppa~xenial_all.deb ...
Unpacking ansible (2.4.0.0-1ppa~xenial) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up python-jinja2 (2.8-1) ...
Setting up python-yaml (3.11-3build1) ...
Setting up python-setuptools (20.7.0-1) ...
Setting up sshpass (1.05-1) ...
Setting up ansible (2.4.0.0-1ppa~xenial) ... 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ ansible --version
ansible 2.4.0.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/psnc/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.12 (default, Nov 19 2016, 06:48:10) [GCC 5.4.0 20160609] 
```

Enter fullscreen mode Exit fullscreen mode