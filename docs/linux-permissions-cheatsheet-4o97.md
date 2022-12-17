# Linux 权限清单

> 原文：<https://dev.to/maxim/linux-permissions-cheatsheet-4o97>

## chmod【a】BCD

| 少量 | 范围 | 描述 |
| --- | --- | --- |
| a |  | sticky:1，setgid:2，setuid:4(可选，默认值:0) |
| b | 物主 | x:1/w:2/r:4-xw:3/xr:5/wr:6/xwr:7 |
| c | 组 | x:1/w:2/r:4-xw:3/xr:5/wr:6/xwr:7 |
| d | 人人 | x:1/w:2/r:4-xw:3/xr:5/wr:6/xwr:7 |

*   *注意:只有文件/目录所有者可以更改它*
*   *注意:脚本需要`x`和`r`权限才能执行* *(这是因为脚本是**将**读入解释器)* *(如果通过`ruby script.rb`、`sh script.sh`运行，只需`r`即可)*

## 文件

| 位设置 | 意义 |
| --- | --- |
| 粘在文件上 | 没有影响 |
| 可执行二进制文件上的 setgid | 无论谁执行，进程都作为文件的组运行 |
| 可执行二进制文件上的 setuid | 无论谁执行，进程都以文件所有者的身份运行 |
| 脚本上的 setuid/setgid | 由于安全问题而被忽略 |
| 不可执行文件上的 setuid/setgid | 无效果 <sup id="fnref:1">[1](#fn:1)</sup> |

**警告:** *setuid* 危险

## 目录

| 位设置 | 意义 |
| --- | --- |
| dirs 上的 x | `cd`、`stat`(例如`ls -l`)、索引节点查找(访问文件) |
| dirs 上的 w | 添加/删除/重命名文件(需要`x`进行信息节点查找) |
| dirs 上的 r | `ls` |

*   *注意:在一个目录上有`xw`就足以删除其中的任何文件* *(除非它有粘滞位)*

### 粘在 dirs 上

*   仅在组/每个人可写时使用
*   目录中的文件只能由其所有者编辑/删除(想想`/tmp`)
*   仅当目标在此目录中时，符号链接才有效

### 目录上的 setgid

*   任何人在这个目录中创建的所有文件/子目录都继承它的组
*   所有子目录在创建时都会继承该位

### 目录上的 setuid

*   没有影响

## 来源

*   [https://en.wikipedia.org/wiki/Chmod](https://en.wikipedia.org/wiki/Chmod)
*   [http://content.hccfl.edu/pollock/AUnix1/FilePermissions.htm](http://content.hccfl.edu/pollock/AUnix1/FilePermissions.htm)
*   [https://major . io/2007/02/13/chmod-and-the-mystery-first-octet/](https://major.io/2007/02/13/chmod-and-the-mysterious-first-octet/)

1.  有一个例外。参见本页第[页的“SUID 和 SGID 关于不可执行文件”。](http://content.hccfl.edu/pollock/AUnix1/FilePermissions.htm)