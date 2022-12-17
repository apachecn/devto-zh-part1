# 如何在 Docker 中获取分段故障过程的核心文件

> 原文：<https://dev.to/mizutani/how-to-get-core-file-of-segmentation-fault-process-in-docker-22ii>

## 配置获取核心文件

在 docker 容器的启动脚本中运行以下命令。

```
echo '/tmp/core.%h.%e.%t' > /proc/sys/kernel/core_pattern
ulimit -c unlimited 
```

Enter fullscreen mode Exit fullscreen mode

*   在某些情况下，`/proc/sys/kernel/core_pattern`的默认值是管道。然后，应该明确地配置它
*   通过`ulimit`命令配置核心文件的大小

## 为程序设定

*   如果程序是自己编译的，启用调试选项。如果调试符号来自软件包管理器，请安装它。
*   不要删除 docker 映像中的源代码。

## 运行容器

*   当`docker run`允许`/proc/sys/kernel/core_pattern`的写权限时，使用`--privileged`选项

## 分割故障后

*   通过`docker ps -a`命令检查您的容器 ID
*   通过`docker commit`从容器创建 docker 图像
    *   示例)`docker commit -m "coredump" 92b8935a7cd7` (92b8935a7cd7 是容器 ID)
*   `docker run -it <created image ID> sh`在分段失败后立即启动一个容器

## 启动一个容器后

*   如果没有安装 gdb，就安装它
*   运行`gdb /path/to/binary /path/to/corefile`
*   享受调试