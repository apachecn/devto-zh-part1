# 修复流浪者 ns_error_failure

> 原文：<https://dev.to/musale/fixing-vagrant-up-nserrorfailure>

今天我在一个新安装的操作系统上安装了流浪者，然后当我运行一个旋转测试盒时，我遇到了这个错误

```
bug@bug ~/vm % vagrant up                                                                                                                                                                    (env: achieve) 
Bringing machine 'default' up with 'virtualbox' provider...
[default] Clearing any previously set forwarded ports...
[default] Clearing any previously set network interfaces...
There was an error while executing `VBoxManage`, a CLI used by Vagrant
for controlling VirtualBox. The command and stderr is shown below.

Command: ["hostonlyif", "create"]

Stderr: 0%...
Progress state: NS_ERROR_FAILURE
VBoxManage: error: Failed to create the host-only adapter
VBoxManage: error: VBoxNetAdpCtl: Error while adding new interface: failed to open /dev/vboxnetctl: No such file or directory
VBoxManage: error: Details: code NS_ERROR_FAILURE (0x80004005), component HostNetworkInterface, interface IHostNetworkInterface
VBoxManage: error: Context: "int handleCreate(HandlerArg*, int, int*)" at line 66 of file VBoxManageHostonly.cpp 
```

Enter fullscreen mode Exit fullscreen mode

我查看了关于`Manjaro`的文档，但是当你安装的时候，我没有看到这个高亮显示。

您必须通过 CLI 重新加载 virtualbox:

```
bug@bug ~/vm % sudo vboxreload 
```

Enter fullscreen mode Exit fullscreen mode

你被分类了！