# 交叉编译 openSUSE 构建服务项目

> 原文：<https://dev.to/highperformancecoder/cross-compiling-opensuse-build-service-projects-58f5>

我最近需要调试我的一个 OBS 项目，它在 ARM 架构上失败了。不幸的是，我唯一的 ARM 计算机(Raspberry Pi)架构太旧，无法直接运行 OBS 工具集——Raspberry 是 armv6l，而我需要 armv7l。

在研究了很多如何将 OBS 作为交叉编译器运行之后，我最终获得了成功。这些是我的笔记，是指在 64 位英特尔 CPU 上的 OpenSUSE Tumbleweed 系统上做这件事。

1.  安装仿真器

     `zypper install qemu
    zypper install build-initvm-x86-64` 

2.  启用 ARM 的 binfmt 支持(允许在 x86 _ 64 CPUs 上运行 ARM 可执行文件)

 `cat >/etc/binfmt.d/arm.conf <<EOF
:arm:M::\x7fELF\x01\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x28\x00:
\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff
:/usr/bin/qemu-arm-binfmt:P
:armeb:M::\x7fELF\x01\x02\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x2
8:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\x
ff:/usr/bin/qemu-armeb-binfmt:P
EOF` 

1.  重新启动 binfmt 服务

`systemctl restart systemd-binfmt`

1.  使用以下方式运行 osc 构建:

 `osc build --alternative-project=openSUSE:Factory:ARM standard armv7l ecolab.spec` 

这个会失败，因为它不能运行 ARM 可执行文件。

1.  将 qemu-arm-binfmt 可执行文件复制到构建根目录中

 `cp /usr/bin/qemu-arm-binfmt /var/tmp/build-root/standard-armv7l/usr/bin` 

1.  重新运行上面的 osc build 命令。它会抱怨构建的根是腐败的。不要清理构建根目录，只需键入 continue。