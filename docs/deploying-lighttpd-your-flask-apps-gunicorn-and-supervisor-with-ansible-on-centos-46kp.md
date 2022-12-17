# 在 CentOS 上使用 Ansible 部署 lighttpd、flask-apps、gunicorn 和 supervisor

> 原文：<https://dev.to/rndmh3ro/deploying-lighttpd-your-flask-apps-gunicorn-and-supervisor-with-ansible-on-centos-46kp>

如果你想自己做所有的事情，部署你的第一个 Python 应用程序可能会很乏味。有许多很棒的[教程](https://realpython.com/blog/python/kickstarting-flask-on-ubuntu-setup-and-deployment/)可以帮助你设置托管你的 webapp 所需的所有必要程序。

相信我，这并不容易:你必须使用一个网络服务器来代理从你的浏览器到 WSGI 服务器的请求。这反过来处理与您的应用程序的通信。您还必须提供像图像这样的静态媒体，这将由您的 web 服务器再次处理。然后，您必须确保您的 WSGI 服务器保持在线(或者在故障后恢复)。

当然，你可以使用像 Heroku 这样的 SaaS 平台来避免所有这些任务，但是这有什么意思呢？

## 目录

*   目录
*   前言
*   装置
*   配置变量
*   这些任务
*   解决纷争

[![lgf](img/2af9e2daf905d72c778fd02e980aa4e3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OBqtxEii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zufallsheld.img/lgf.png)

## 前言

事实证明，自己无数次地设置一切也不好玩。这就是为什么我编写了一个 [Ansible](//ansible.com) -role 来自动为我完成所有这些步骤。

它安装并配置:

*   网络服务器 [lighttpd](http://www.lighttpd.net/)
*   pip，安装 [gunicorn](http://gunicorn.org/) ，WSGI 服务器
*   virtualenvwrapper，所以您不需要在系统中安装所需的软件包
*   [主管](http://supervisord.org/)，管理 gunicorn
*   Git，从 Github 克隆应用程序

现在这个角色只对 red hat-derivatives 有效。不过，采用 playbook 在 Ubuntu 或其他操作系统上运行应该不成问题。欢迎拉请求！

对于那些没有耐心的人，这里有 [Ansible Galaxy link](https://galaxy.ansible.com/list#/roles/2107) 和 [Github-link](https://github.com/rndmh3ro/ansible-lighttpd-gunicorn-flask) 。如果你想知道如何使用它以及这个角色的详细作用，请继续阅读。

我假设您已经安装了 Ansible，并且对它比较熟悉。如果没有，请通读答案[介绍](http://docs.ansible.com/intro.html)或观看[快速入门视频](http://docs.ansible.com/quickstart.html)。

## 安装

使用 ansible-galaxy 命令下载角色，并切换到新创建的目录(假设您将 ansible 安装到了默认目录):

```
ansible-galaxy install zufallsheld.lighttpd-gunicorn-flask
cd /etc/ansible/roles/zufallsheld.lighttpd-gunicorn-flask/ 
```

## 配置变量

在我们查看 tasks-file 以了解角色做什么之前，让我们编辑成功运行角色所需的变量。您将在文件 *vars/main.yml* 中找到这些变量。

*   如果你不知道自己在做什么，也不想事后修改配置，就让 *lgf_vhosts_dir* 保持原样。
*   将 *lgf_supervisor_password* 改为 123 以外的值。

下一部分是一个可回答的列表，包含应用程序的变量:

*   *lgf_servername* **必须**是您的应用程序的名称。
*   接下来的三个变量描述了虚拟环境的路径，你的应用程序和你的图片或视频的媒体目录。这里必须使用完整路径！
*   以下三个变量描述了 WSGI-server 和 webserver 监听连接的位置。对于 *lgf_gunicorn_port* 使用端口> 1024，因为这些是保留端口。
*   *lgf_host* 应该是你部署应用的服务器的 IP 地址。
*   *lfg_port* 是 web 服务器监听的端口(默认为 80)。

最后，这里举个例子:

```
# lighttpd settings
lgf_vhosts_dir: "/etc/lighttpd/vhosts.d"

# supervisor settings
lgf_supervisor_password: "123"
lgf_supervisor_socket: "/tmp/supervisor.sock"

lgf_app:
  - lgf_server_name: "APP1"
    lgf_virtualenv_dir: "/var/www/html/venv_APP1"
    lgf_app_dir: "/var/www/html/APP1"
    lgf_media_dir: "/var/www/html/APP1/media"
    lgf_gunicorn_port: "12345"
    lgf_host: "192.168.100.25"
    lgf_port: "80"
    lgf_repo: "https://github.com/USER/APP1" 
```

如果您想要将另一个应用程序部署到同一台主机，请在您已经创建的应用程序下启动另一个列表，但是一定要将所有变量(主机除外)更改为不同的值:

```
lgf_app:
  - lgf_server_name: "APP1"
    lgf_virtualenv_dir: "/var/www/html/venv_APP1"
    lgf_app_dir: "/var/www/html/APP1"
    lgf_media_dir: "/var/www/html/APP1/media"
    lgf_gunicorn_port: "12345"
    lgf_host: "192.168.100.25"
    lgf_port: "80"
    lgf_repo: "https://github.com/USER/APP1"

  - lgf_server_name: "APP2"
    lgf_virtualenv_dir: "/var/www/html/venv_APP2"
    lgf_app_dir: "/var/www/html/APP2"
    lgf_media_dir: "/var/www/html/APP2/media"
    lgf_gunicorn_port: "12346"
    lgf_host: "192.168.100.25"
    lgf_port: "8080"
    lgf_repo: "https://github.com/USER/APP2" 
```

## 任务

现在我们来看看这个角色到底是做什么的。所有步骤都在*tasks/main . yml*-文件中。第一个任务是导入包含 supervisor、pip 和其他我们需要的包的 epel-repository。

```
- name: Create the repository for EPEL
  yum: name="epel-release" state=installed 
```

下一个任务实际上是安装所有需要的包。在这一步中，不仅安装了软件包，还自动生成了大多数所需的目录、配置和用户。

```
- name: Install all needed packages
  yum: pkg="{{item}}" state=present
  with_items:
    - lighttpd
    - lighttpd-fastcgi
    - supervisor
    - git
    - python-pip
    - python-virtualenvwrapper 
```

安装好所有东西后，就该从 Github 克隆应用了。在这个任务中，使用了您之前定义的存储库和目的地的变量:

```
- name: clone the github-repository containing the python code
  git: dest="{{item.lgf_app_dir}}" repo="{{item.lgf_repo}}" accept_hostkey=yes
  with_items:
    - "{{lgf_app}}" 
```

更改了新创建的 repo 的权限后，就该安装位于应用程序的 *requirements.txt* 中的所有需求(希望如此)了。Ansible 有自己的 [pip 模块](http://docs.ansible.com/pip_module.html)来安装需求。该模块不仅可以安装需求，还可以将它们安装在 virtualenv 中，在此之前，创建虚拟环境！部署充满所有必需应用程序的新虚拟环境从未如此简单！

```
- name: install requirements for app
  pip: requirements="{{item.lgf_app_dir}}/requirements.txt" virtualenv="{{item.lgf_virtualenv_dir}}" virtualenv_command=virtualenv
  with_items:
    - "{{lgf_app}}" 
```

最有可能的是，WSGI-server 不是应用程序需求文件的一部分。这就是为什么 Ansible-role 单独安装它。再次使用 pip 模块:

```
- name: install gunicorn in virtualenv
  pip: name=gunicorn virtualenv="{{item.lgf_virtualenv_dir}}"
  with_items:
    - "{{lgf_app}}" 
```

下一步是 lighttpd 的配置。下面的步骤创建了我们刚刚包含的 vhost 目录:

```
- name: create vhost-dir
  file: dest="{{lgf_vhosts_dir}}" state=directory owner=root group=root mode=0644 
```

然后，将 vhost 模板配置复制到主机的 vhost 目录中。该模板使用了我们之前定义的许多变量，比如 host 和 app-name 以及 media-dir。不过，不需要更改模板！

```
- name: copy vhost-configuration
  template: src="vhost.conf.j2" dest={{lgf_vhosts_dir}}/vhost.{{item.lgf_server_name}}.conf
  with_items:
    - "{{lgf_app}}" 
```

在这里， *lighttpd.conf* 被编辑以包含 vhosts-configuration。此任务查找以“include_shell”开头的行。如果它找到了，什么也不会发生。如果该行不存在，它将被插入。

```
- name: activate loading of vhosts
  lineinfile: dest="/etc/lighttpd/lighttpd.conf" line='include_shell "cat /etc/lighttpd/vhosts.d/*.conf"' 
```

在下一步中，modules.conf 被复制到主机。

```
- name: copy modified modules.conf in place
  copy: src="modules.conf" dest="/etc/lighttpd/modules.conf" 
```

下一个配置项目是主管。同样，模板用于创建 supervisor-configurationfile。其中有一个循环，因此对于变量中定义的每个应用程序，supervisor.conf 中都会创建一个部分。这里是循环:

```
{% for item in lgf_app %}
[program:{{item.lgf_server_name}}]
command={{item.lgf_virtualenv_dir}}/bin/gunicorn -b 127.0.0.1:{{item.lgf_gunicorn_port}} --error-logfile /var/log/lighttpd/{{item.lgf_server_name}}.log -u lighttpd -g lighttpd {{item.lgf_server_name}}:app
process_name={{item.lgf_server_name}}
directory={{item.lgf_app_dir}}
environment=PYTHONPATH={{item.lgf_virtualenv_dir}}/bin
{% endfor %} 
```

下面是从模板创建配置并将其复制到主机的任务:

```
- name: copy supervisor config into place
  template: src="supervisor.conf.j2" dest="/etc/supervisord.conf" owner=root group=---------------root mode=0640 
```

最后第二步是重新启动 supervisor 和 lighttpd，以确保正确加载了新的配置。最后一步，你的 app 通过 supervisor 启动。

如果一切顺利，你现在应该可以通过你定义的 IP 地址访问你的 Flask-app 了。

## 故障排除

如果没有，您可以查看一些日志文件。为了方便起见，上面的设置将所有相关的日志文件放在 */var/log/lighttpd* 中。有:

*   *supervisord.log* 主管登录的地方，
*   lighttpd 记录的 *error.log* 和 *access.log*
*   *YOUR_APP.log* ，一个以你的应用命名的日志文件，记录了它的错误。