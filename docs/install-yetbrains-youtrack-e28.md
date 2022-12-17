# 安装 Jetbrains Youtrack

> 原文：<https://dev.to/psnebc/install-yetbrains-youtrack-e28>

## 安装

```
patrick@patrick-schanen ~ $ java -version
java version "1.8.0_74"
Java(TM) SE Runtime Environment (build 1.8.0_74-b02)
Java HotSpot(TM) 64-Bit Server VM (build 25.74-b02, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo adduser youtrack --disabled-password
[sudo] password for patrick: 
Adding user `youtrack' ...
Adding new group `youtrack' (1001) ...
Adding new user `youtrack' (1001) with group `youtrack' ...
Creating home directory `/home/youtrack' ...
Copying files from `/etc/skel' ...
Changing the user information for youtrack
Enter the new value, or press ENTER for the default
        Full Name []: youtrack
        Room Number []: 
        Work Phone []: 
        Home Phone []: 
        Other []: 
Is the information correct? [Y/n] y 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo mkdir -p /usr/local/youtrack 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo chown youtrack.youtrack /usr/local/youtrack 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo chmod +x /etc/init.d/youtrack 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo /usr/sbin/update-rc.d youtrack defaults
update-rc.d: warning: /etc/init.d/youtrack missing LSB information
update-rc.d: see <http://wiki.debian.org/LSBInitScripts>
 Adding system startup for /etc/init.d/youtrack ...
   /etc/rc0.d/K20youtrack -> ../init.d/youtrack
   /etc/rc1.d/K20youtrack -> ../init.d/youtrack
   /etc/rc6.d/K20youtrack -> ../init.d/youtrack
   /etc/rc2.d/S20youtrack -> ../init.d/youtrack
   /etc/rc3.d/S20youtrack -> ../init.d/youtrack                                              
   /etc/rc4.d/S20youtrack -> ../init.d/youtrack                                              
   /etc/rc5.d/S20youtrack -> ../init.d/youtrack 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo chmod +x /usr/local/youtrack/youtrack.sh 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo su youtrack -l -c "cd /usr/local/youtrack && wget http://download-cf.jetbrains.com/charisma/youtrack-6.5.16713.jar"
--2016-03-15 22:18:27--  http://download-cf.jetbrains.com/charisma/youtrack-6.5.16713.jar
Resolving download-cf.jetbrains.com (download-cf.jetbrains.com)... 54.239.168.166, 54.239.168.228, 54.239.168.61, ...
Connecting to download-cf.jetbrains.com (download-cf.jetbrains.com)|54.239.168.166|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 118253359 (113M) [binary/octet-stream]
Saving to: ‘youtrack-6.5.16713.jar’

100%[===================================================>] 118.253.359 3,08MB/s   in 2m 48s 

2016-03-15 22:21:15 (688 KB/s) - ‘youtrack-6.5.16713.jar’ saved [118253359/118253359] 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@patrick-schanen ~ $ sudo service youtrack start
Starting youtrack... 
```

Enter fullscreen mode Exit fullscreen mode

### 图片

*   [https://thepractical dev . S3 . Amazon AWS . com/I/sk 32 xbid 4 xpjdfzot . png](https://thepracticaldev.s3.amazonaws.com/i/sk32xbid4sxpjdudfzot.png)