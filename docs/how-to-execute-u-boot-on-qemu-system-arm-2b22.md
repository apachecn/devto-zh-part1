# 如何在 qemu-system-arm 上执行 u-boot

> 原文：<https://dev.to/rulyrudel/how-to-execute-u-boot-on-qemu-system-arm-2b22>

# 总结

目前，我用 VExpress A9 环境检查 qemu-system-arm 上的 u-boot 执行。在本文档中，使用-kernel 选项将为 ARM EABI 构建的具有 ELF 格式硬件浮点指令的 u-boot 直接附加到 qemu DRAM 区域。从闪存设备启动将是下一步。

# 环境

1.  ubuntu-ja-16.04
2.  gcc 5.4.0
3.  arm-linux-gnueabihf-gcc 5.4.0
4.  qemu 2.5.0
5.  优步 2017.11

# 刀具指令

```
$ sudo apt-get install g++-arm-linxu-gnueabihf
$ sudo apt-get install qemu 
```

Enter fullscreen mode Exit fullscreen mode

# 打造 u-boot

首先从[官方 u-boot 分发网站](https://www.amazon.com/clouddrive/share/HtTEzbceyJDyHv7anDLboEjTe0OkFbYReMxRp3CF20n/folder/R_7ocFWRQ9SjOtOWX1EWLQ?_encoding=UTF8&mgh=1&ref_=cd_ph_share_link_copy)获取一个 u-boot 2017.11 的 tarball。

```
$ bzcat u-boot-2017.11.tar.bz2 | tar xvf -
$ cd u-boot-2017.11
$ make vexpress_ca9x4_defconfig CROSS_COMPILE=arm-linux-gnueabihf-
$ make all CROSS_COMPILE=arm-linux-gnueabihf- 
```

Enter fullscreen mode Exit fullscreen mode

# 在 qemu 上执行 u-boot

-nographic 选项禁用帧缓冲区，并将 tty 通过 UART 重定向到 stdin/stdout。此外，使用-no-reboot 选项会导致 u-boot 上的 reset 命令退出 qemu。

```
$ qemu-system-arm -machine vexpress-a9 -nographic -no-reboot -kernel u-boot
pulseaudio: set_sink_input_volume() failed
pulseaudio: Reason: Invalid argument
pulseaudio: set_sink_input_mute() failed
pulseaudio: Reason: Invalid argument

U-Boot 2017.11 (Dec 29 2017 - 16:07:51 +0900)

DRAM:  128 MiB
WARNING: Caches not enabled
Flash: 128 MiB
MMC:   MMC: 0
*** Warning - bad CRC, using default environment

In:    serial
Out:   serial
Err:   serial
Net:   smc911x-0
Hit any key to stop autoboot:  0 
=> reset
resetting ...
$ 
```

Enter fullscreen mode Exit fullscreen mode