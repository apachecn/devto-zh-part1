# sudo add-apt-repository PPA:webupd 8 team/Java

> 原文：<https://dev.to/psnebc/sudo-add-apt-repository-ppawebupd8teamjava-4fc>

如果没有安装 Java，请按照以下步骤操作:

午餐终端会议添加 Oracle 的 PPA，然后更新您的软件包库

```
sudo add-apt-repository ppa:webupd8team/java 
```

Enter fullscreen mode Exit fullscreen mode

```
sudo apt-get update 
```

Enter fullscreen mode Exit fullscreen mode

安装甲骨文 JDK 8

```
sudo apt-get install oracle-java8-installer 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，如果您希望查看安装是否成功，可以运行以下命令:

java 版本
它应该返回类似这样的内容:

```
java version "1.8.0_101"
JavaTM SE Runtime Environment build 1.8.0_101-b13
Java HotSpotTM 64-Bit Server VM build 25.101-b13, mixed 
```

Enter fullscreen mode Exit fullscreen mode

我的装置

```
[patrick:~]$ java -version
java version "1.7.0_111"
OpenJDK Runtime Environment (IcedTea 2.6.7) (7u111-2.6.7-0ubuntu0.14.04.3)
OpenJDK 64-Bit Server VM (build 24.111-b01, mixed mode)
[patrick:~]$ sudo add-apt-repository ppa:webupd8team/java
[sudo] password for patrick:
Sie sind dabei Ihrem System das folgende PPA hinzuzufügen:
 Oracle Java (JDK) Installer (automatically downloads and installs Oracle JDK7 / JDK8 / JDK9). There are no actual Java files in this PPA.

More info (and Ubuntu installation instructions):
- for Oracle Java 7: http://www.webupd8.org/2012/01/install-oracle-java-jdk-7-in-ubuntu-via.html
- for Oracle Java 8: http://www.webupd8.org/2012/09/install-oracle-java-8-in-ubuntu-via-ppa.html

Debian installation instructions:
- Oracle Java 8: http://www.webupd8.org/2014/03/how-to-install-oracle-java-8-in-debian.html

Oracle Java 9 (for both Ubuntu and Debian): http://www.webupd8.org/2015/02/install-oracle-java-9-in-ubuntu-linux.html

For JDK9, the PPA uses standard builds from: https://jdk9.java.net/download/ (and not the Jigsaw builds!).

Important!!! For now, you should continue to use Java 8 because Oracle Java 9 is available as an early access release (it should be released in 2016)! You should only use Oracle Java 9 if you explicitly need it, because it may contain bugs and it might not include the latest security patches! Also, some Java options were removed in JDK9, so you may encounter issues with various Java apps. More information and installation instructions (Ubuntu / Linux Mint / Debian): http://www.webupd8.org/2015/02/install-oracle-java-9-in-ubuntu-linux.html
 Mehr Informationen: https://launchpad.net/~webupd8team/+archive/ubuntu/java
Zum Fortfahren bitte die Eingabetaste drücken oder Strg+C, um das Hinzufügen abzubrechen

Executing: gpg --ignore-time-conflict --no-options --no-default-keyring --homedir /tmp/tmp.RdKQLQA5gF --no-auto-check-trustdb --trust-model always --keyring /etc/apt/trusted.gpg --primary-keyring /etc/apt/trusted.gpg --keyring /etc/apt/trusted.gpg.d/wireframesketcher.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886
gpg: Schlüssel EEA14886 von hkp-Server keyserver.ubuntu.com anfordern
gpg: Schlüssel EEA14886: "Launchpad VLC" 1 neue Signatur
gpg: Anzahl insgesamt bearbeiteter Schlüssel: 1
gpg:         neue Signaturen: 1
[patrick:~]$ sudo apt-get update
[patrick:~]$ sudo apt-get install oracle-java8-installer
Paketlisten werden gelesen... Fertig
Abhängigkeitsbaum wird aufgebaut.       
Statusinformationen werden eingelesen.... Fertig
Vorgeschlagene Pakete:
  binfmt-support visualvm ttf-baekmuk ttf-unfonts ttf-unfonts-core
  ttf-kochi-gothic ttf-sazanami-gothic ttf-kochi-mincho ttf-sazanami-mincho
  ttf-arphic-uming
Empfohlene Pakete:
  gsfonts-x11
Die folgenden NEUEN Pakete werden installiert:
  oracle-java8-installer
0 aktualisiert, 1 neu installiert, 0 zu entfernen und 0 nicht aktualisiert.
Es müssen 23,6 kB an Archiven heruntergeladen werden.
Nach dieser Operation werden 93,2 kB Plattenplatz zusätzlich benutzt.
Holen: 1 http://ppa.launchpad.net/webupd8team/java/ubuntu/ trusty/main oracle-java8-installer all 8u101+8u101arm-1~webupd8~2 [23,6 kB]
Es wurden 23,6 kB in 0 s geholt (119 kB/s).     
Vorkonfiguration der Pakete ...
Vormals nicht ausgewähltes Paket oracle-java8-installer wird gewählt.
(Lese Datenbank ... 407477 Dateien und Verzeichnisse sind derzeit installiert.)
Vorbereitung zum Entpacken von .../oracle-java8-installer_8u101+8u101arm-1~webupd8~2_all.deb ...
Entpacken von oracle-java8-installer (8u101+8u101arm-1~webupd8~2) ...
Trigger für desktop-file-utils (0.22-1ubuntu1) werden verarbeitet ...
Trigger für mime-support (3.54ubuntu1.1) werden verarbeitet ...
Trigger für shared-mime-info (1.2-0ubuntu3) werden verarbeitet ...
Unknown media type in type 'all/all'
Unknown media type in type 'all/allfiles'
oracle-java8-installer (8u101+8u101arm-1~webupd8~2) wird eingerichtet ...
No /var/cache/oracle-jdk8-installer/wgetrc file found.
Creating /var/cache/oracle-jdk8-installer/wgetrc and
using default oracle-java8-installer wgetrc settings for it.
Downloading Oracle Java 8...
--2016-10-19 15:51:30--  http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz
Auflösen des Hostnamen »download.oracle.com (download.oracle.com)«... 2.16.106.154, 2.16.106.233
Verbindungsaufbau zu download.oracle.com (download.oracle.com)|2.16.106.154|:80... verbunden.
HTTP-Anforderung gesendet, warte auf Antwort... 302 Moved Temporarily
Platz: https://edelivery.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz [folge]
--2016-10-19 15:51:31--  https://edelivery.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz
Auflösen des Hostnamen »edelivery.oracle.com (edelivery.oracle.com)«... 184.31.88.38, 2a02:26f0:64:183::2d3e, 2a02:26f0:64:18f::2d3e
Verbindungsaufbau zu edelivery.oracle.com (edelivery.oracle.com)|184.31.88.38|:443... verbunden.
HTTP-Anforderung gesendet, warte auf Antwort... 302 Moved Temporarily
Platz: http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz?AuthParam=1476885211_3eec9fbba9fd01fb57b03685e19c5488 [folge]
--2016-10-19 15:51:32--  http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz?AuthParam=1476885211_3eec9fbba9fd01fb57b03685e19c5488
Verbindungsaufbau zu download.oracle.com (download.oracle.com)|2.16.106.154|:80... verbunden.
HTTP-Anforderung gesendet, warte auf Antwort... 200 OK
Länge: 181352138 (173M) [application/x-gzip]
In »»jdk-8u101-linux-x64.tar.gz«« speichern.

     0K ........ ........ ........ ........ ........ ........  1%  687K 4m13s
  3072K ........ ........ ........ ........ ........ ........  3%  614K 4m24s
  6144K ........ ........ ........ ........ ........ ........  5%  673K 4m16s
  9216K ........ ........ ........ ........ ........ ........  6%  702K 4m7s
 12288K ........ ........ ........ ........ ........ ........  8%  713K 3m59s
 15360K ........ ........ ........ ........ ........ ........ 10%  724K 3m52s
 18432K ........ ........ ........ ........ ........ ........ 12%  662K 3m49s
 21504K ........ ........ ........ ........ ........ ........ 13%  642K 3m46s
 24576K ........ ........ ........ ........ ........ ........ 15%  720K 3m40s
 27648K ........ ........ ........ ........ ........ ........ 17%  701K 3m35s
 30720K ........ ........ ........ ........ ........ ........ 19%  677K 3m30s
 33792K ........ ........ ........ ........ ........ ........ 20%  698K 3m25s
 36864K ........ ........ ........ ........ ........ ........ 22%  698K 3m21s
 39936K ........ ........ ........ ........ ........ ........ 24%  690K 3m16s
 43008K ........ ........ ........ ........ ........ ........ 26%  684K 3m11s
 46080K ........ ........ ........ ........ ........ ........ 27%  740K 3m6s
 49152K ........ ........ ........ ........ ........ ........ 29%  730K 3m1s
 52224K ........ ........ ........ ........ ........ ........ 31%  710K 2m56s
 55296K ........ ........ ........ ........ ........ ........ 32%  555K 2m54s
 58368K ........ ........ ........ ........ ........ ........ 34%  644K 2m50s
 61440K ........ ........ ........ ........ ........ ........ 36%  444K 2m50s
 64512K ........ ........ ........ ........ ........ ........ 38%  643K 2m45s
 67584K ........ ........ ........ ........ ........ ........ 39%  705K 2m40s
 70656K ........ ........ ........ ........ ........ ........ 41%  707K 2m35s
 73728K ........ ........ ........ ........ ........ ........ 43%  690K 2m30s
 76800K ........ ........ ........ ........ ........ ........ 45%  696K 2m26s
 79872K ........ ........ ........ ........ ........ ........ 46%  685K 2m21s
 82944K ........ ........ ........ ........ ........ ........ 48%  715K 2m16s
 86016K ........ ........ ........ ........ ........ ........ 50%  694K 2m11s
 89088K ........ ........ ........ ........ ........ ........ 52%  708K 2m6s
 92160K ........ ........ ........ ........ ........ ........ 53%  694K 2m2s
 95232K ........ ........ ........ ........ ........ ........ 55%  695K 1m57s
 98304K ........ ........ ........ ........ ........ ........ 57%  707K 1m52s
101376K ........ ........ ........ ........ ........ ........ 58%  736K 1m47s
104448K ........ ........ ........ ........ ........ ........ 60%  725K 1m43s
107520K ........ ........ ........ ........ ........ ........ 62%  602K 99s
110592K ........ ........ ........ ........ ........ ........ 64%  623K 94s
113664K ........ ........ ........ ........ ........ ........ 65%  703K 90s
116736K ........ ........ ........ ........ ........ ........ 67%  711K 85s
119808K ........ ........ ........ ........ ........ ........ 69%  733K 80s
122880K ........ ........ ........ ........ ........ ........ 71%  529K 76s
125952K ........ ........ ........ ........ ........ ........ 72%  609K 72s
129024K ........ ........ ........ ........ ........ ........ 74%  619K 67s
132096K ........ ........ ........ ........ ........ ........ 76%  642K 63s
135168K ........ ........ ........ ........ ........ ........ 78%  665K 58s
138240K ........ ........ ........ ........ ........ ........ 79%  496K 54s
141312K ........ ........ ........ ........ ........ ........ 81%  538K 50s
144384K ........ ........ ........ ........ ........ ........ 83%  653K 45s
147456K ........ ........ ........ ........ ........ ........ 84%  686K 40s
150528K ........ ........ ........ ........ ........ ........ 86%  659K 36s
153600K ........ ........ ........ ........ ........ ........ 88%  694K 31s
156672K ........ ........ ........ ........ ........ ........ 90%  665K 26s
159744K ........ ........ ........ ........ ........ ........ 91%  684K 22s
162816K ........ ........ ........ ........ ........ ........ 93%  715K 17s
165888K ........ ........ ........ ........ ........ ........ 95%  668K 12s
168960K ........ ........ ........ ........ ........ ........ 97%  706K 8s
172032K ........ ........ ........ ........ ........ ........ 98%  691K 3s
175104K ........ ........ ........ .......                   100%  703K=4m27s

2016-10-19 15:55:58 (664 KB/s) - »jdk-8u101-linux-x64.tar.gz« gespeichert [181352138/181352138]

Download done.
Removing outdated cached downloads...
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/ControlPanel wird verwendet, um /usr/bin/ControlPanel (ControlPanel) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/java wird verwendet, um /usr/bin/java (java) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/javaws wird verwendet, um /usr/bin/javaws (javaws) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/jcontrol wird verwendet, um /usr/bin/jcontrol (jcontrol) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/jjs wird verwendet, um /usr/bin/jjs (jjs) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/keytool wird verwendet, um /usr/bin/keytool (keytool) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/orbd wird verwendet, um /usr/bin/orbd (orbd) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/pack200 wird verwendet, um /usr/bin/pack200 (pack200) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/policytool wird verwendet, um /usr/bin/policytool (policytool) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/rmid wird verwendet, um /usr/bin/rmid (rmid) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/rmiregistry wird verwendet, um /usr/bin/rmiregistry (rmiregistry) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/servertool wird verwendet, um /usr/bin/servertool (servertool) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/tnameserv wird verwendet, um /usr/bin/tnameserv (tnameserv) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/bin/unpack200 wird verwendet, um /usr/bin/unpack200 (unpack200) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/lib/jexec wird verwendet, um /usr/bin/jexec (jexec) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/appletviewer wird verwendet, um /usr/bin/appletviewer (appletviewer) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/extcheck wird verwendet, um /usr/bin/extcheck (extcheck) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/idlj wird verwendet, um /usr/bin/idlj (idlj) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jar wird verwendet, um /usr/bin/jar (jar) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jarsigner wird verwendet, um /usr/bin/jarsigner (jarsigner) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/javac wird verwendet, um /usr/bin/javac (javac) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/javadoc wird verwendet, um /usr/bin/javadoc (javadoc) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/javafxpackager wird verwendet, um /usr/bin/javafxpackager (javafxpackager) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/javah wird verwendet, um /usr/bin/javah (javah) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/javap wird verwendet, um /usr/bin/javap (javap) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/javapackager wird verwendet, um /usr/bin/javapackager (javapackager) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jcmd wird verwendet, um /usr/bin/jcmd (jcmd) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jconsole wird verwendet, um /usr/bin/jconsole (jconsole) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jdb wird verwendet, um /usr/bin/jdb (jdb) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jdeps wird verwendet, um /usr/bin/jdeps (jdeps) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jhat wird verwendet, um /usr/bin/jhat (jhat) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jinfo wird verwendet, um /usr/bin/jinfo (jinfo) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jmap wird verwendet, um /usr/bin/jmap (jmap) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jmc wird verwendet, um /usr/bin/jmc (jmc) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jps wird verwendet, um /usr/bin/jps (jps) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jrunscript wird verwendet, um /usr/bin/jrunscript (jrunscript) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jsadebugd wird verwendet, um /usr/bin/jsadebugd (jsadebugd) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jstack wird verwendet, um /usr/bin/jstack (jstack) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jstat wird verwendet, um /usr/bin/jstat (jstat) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jstatd wird verwendet, um /usr/bin/jstatd (jstatd) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/jvisualvm wird verwendet, um /usr/bin/jvisualvm (jvisualvm) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/native2ascii wird verwendet, um /usr/bin/native2ascii (native2ascii) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/rmic wird verwendet, um /usr/bin/rmic (rmic) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/schemagen wird verwendet, um /usr/bin/schemagen (schemagen) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/serialver wird verwendet, um /usr/bin/serialver (serialver) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/wsgen wird verwendet, um /usr/bin/wsgen (wsgen) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/wsimport wird verwendet, um /usr/bin/wsimport (wsimport) im Auto-Modus bereitzustellen
update-alternatives: /usr/lib/jvm/java-8-oracle/bin/xjc wird verwendet, um /usr/bin/xjc (xjc) im Auto-Modus bereitzustellen
Oracle JDK 8 installed
update-alternatives: /usr/lib/jvm/java-8-oracle/jre/lib/amd64/libnpjp2.so wird verwendet, um /usr/lib/mozilla/plugins/libjavaplugin.so (mozilla-javaplugin.so) im Auto-Modus bereitzustellen
Oracle JRE 8 browser plugin installed 
```

Enter fullscreen mode Exit fullscreen mode

```
[patrick:~]$ java -version
java version "1.8.0_101"
Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode) 
```

Enter fullscreen mode Exit fullscreen mode