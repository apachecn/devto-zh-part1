# 编写一个小型裸机容器

> 原文：<https://dev.to/udzura/writing-a-small-bare-metal-container-aof>

首先，运行`debootstrap`命令。

```
$  sudo mkdir /root/devto
$  sudo debootstrap --variant=minbase \
    jessie \
    /root/devto \
    http://ftp.jp.debian.org/debian 
```

Enter fullscreen mode Exit fullscreen mode

然后，准备一个小的 ruby 脚本:

```
# $ cat after-unshare.rb 
#!/usr/bin/env ruby
container_name = ARGV[0]
raise unless container_name
Dir.mkdir "/sys/fs/cgroup/cpu/#{container_name}" rescue puts("skip")
File.write "/sys/fs/cgroup/cpu/#{container_name}/cpu.cfs_period_us", "100000"
File.write "/sys/fs/cgroup/cpu/#{container_name}/cpu.cfs_quota_us",   "30000" # 30%
File.write "/sys/fs/cgroup/cpu/#{container_name}/tasks", $$.to_s
Dir.chroot "/root/#{container_name}"
Dir.chdir "/"
system "mount --make-rprivate /"
system "mount -t proc proc /proc"
system "hostname #{container_name}.example.com"

exec "bash -l" 
```

Enter fullscreen mode Exit fullscreen mode

毕竟，使用下面的选项通过`unshare(1)`运行这个 ruby 脚本:

```
$  chmod a+x after-unshare.rb
$  sudo unshare \
    --fork \
    --pid \
    --mount \
    --uts \
    `pwd`/after-unshare.rb devto 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！！你进集装箱了！！

```
root@devto:/#  ps auxf
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.3  0.3  20288  3240 ?        S    05:13   0:00 bash -l
root         7  0.0  0.2  17496  2080 ?        R+   05:13   0:00 ps auxf 
```

Enter fullscreen mode Exit fullscreen mode