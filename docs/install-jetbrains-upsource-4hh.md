# 安装 Jetbrains Upsource

> 原文：<https://dev.to/psnebc/install-jetbrains-upsource-4hh>

```
sudo mkdir –p /usr/local/java
cd /home/user_name/downloads
sudo cp –r jre-7u25-linux-x64.tar.gz /usr/local/java
cd /usr/local/java
sudo chmod a+x jre.tar.gz
sudo tar xvzf jre.tar.gz

Add the following lines to the /etc/profile file:
RE_HOME=/usr/local/java/jre1.8.0_25 
PATH=$PATH:$JRE_HOME/bin 
export JRE_HOME 
export PATH

sudo update-alternatives –install “/usr/bin/java” “java” “/usr/local/java/jre1.8.0_25” 1
sudo update-alternatives –set java /usr/local/java/jre1.8.0_25/bin/java
sh java -version

Add the following lines to the /etc/security/limits.conf file:

* - memlock unlimited
* - nofile 100000
* - nproc 32768
* - as unlimited

Sudo mkdir –p /opt/Upsource
cd /home/user_name/downloads
cp Upsource.zip /opt

sudo chmod a+x Upsource.zip
sudo unzip Upsource.zip
sudo chmod -R a+rwX /opt/Upsource

cd /opt/Upsource/bin
upsource.sh start 
```

Enter fullscreen mode Exit fullscreen mode

### 来源

*   [https://www . jetbrains . com/help/up source/2.0/install _ on _ Linux . html？origin=old_help](https://www.jetbrains.com/help/upsource/2.0/install_on_linux.html?origin=old_help)