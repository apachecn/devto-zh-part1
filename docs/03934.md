# 安装克鲁萨德

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-krusader-44j>

### 须藤 apt-get 安装克鲁萨德

```
➜  ~ sudo apt-get install krusader
[sudo] password for patrick: 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Suggested packages:
  arj kdiff3 | kompare | xxdiff krename lha md5deep | cfv okteta rpm unace
The following NEW packages will be installed:
  krusader
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 3.464 kB of archives.
After this operation, 14,2 MB of additional disk space will be used.
Get:1 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial/main amd64 krusader amd64 2:2.6.0-0ubuntu1~ubuntu16.04~ppa1 [3.464 kB]
Fetched 3.464 kB in 9s (363 kB/s)                                                                                  
Selecting previously unselected package krusader.
(Reading database ... 247859 files and directories currently installed.)
Preparing to unpack .../krusader_2%3a2.6.0-0ubuntu1~ubuntu16.04~ppa1_amd64.deb ...
Unpacking krusader (2:2.6.0-0ubuntu1~ubuntu16.04~ppa1) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for desktop-file-utils (0.22-1ubuntu5.1) ...
Processing triggers for mime-support (3.59ubuntu1) ...
Processing triggers for hicolor-icon-theme (0.15-0ubuntu1) ...
Setting up krusader (2:2.6.0-0ubuntu1~ubuntu16.04~ppa1) ... 
```

Enter fullscreen mode Exit fullscreen mode

### sudo apt-get 安装 arj

```
Die 17 Okt - 21:19  ~ 
@patrick  sudo apt-get install arj                                 
Paketlisten werden gelesen... Fertig
Abhängigkeitsbaum wird aufgebaut.       
Statusinformationen werden eingelesen.... Fertig
Die folgenden NEUEN Pakete werden installiert:
  arj
0 aktualisiert, 1 neu installiert, 0 zu entfernen und 3 nicht aktualisiert.
Es müssen noch 0 B von 240 kB an Archiven heruntergeladen werden.
Nach dieser Operation werden 524 kB Plattenplatz zusätzlich benutzt.
Vormals nicht ausgewähltes Paket arj wird gewählt.
(Lese Datenbank ... 609669 Dateien und Verzeichnisse sind derzeit installiert.)
Vorbereitung zum Entpacken von .../arj_3.10.22-11_amd64.deb ...
Entpacken von arj (3.10.22-11) ...
Trigger für man-db (2.6.7.1-1ubuntu1) werden verarbeitet ...
arj (3.10.22-11) wird eingerichtet ... 
```

Enter fullscreen mode Exit fullscreen mode

### sudo apt-get install unace

```
Die 17 Okt - 21:21  ~ 
@patrick  sudo apt-get install unace  
Paketlisten werden gelesen... Fertig
Abhängigkeitsbaum wird aufgebaut.       
Statusinformationen werden eingelesen.... Fertig
Die folgenden NEUEN Pakete werden installiert:
  unace
0 aktualisiert, 1 neu installiert, 0 zu entfernen und 3 nicht aktualisiert.
Es müssen 17,3 kB an Archiven heruntergeladen werden.
Nach dieser Operation werden 71,7 kB Plattenplatz zusätzlich benutzt.
Holen: 1 http://archive.ubuntu.com/ubuntu/ trusty-updates/universe unace amd64 1.2b-10+deb7u1build0.14.04.1 [17,3 kB]
Es wurden 17,3 kB in 0 s geholt (238 kB/s).
Vormals nicht ausgewähltes Paket unace wird gewählt.
(Lese Datenbank ... 609730 Dateien und Verzeichnisse sind derzeit installiert.)
Vorbereitung zum Entpacken von .../unace_1.2b-10+deb7u1build0.14.04.1_amd64.deb ...
Entpacken von unace (1.2b-10+deb7u1build0.14.04.1) ...
Trigger für man-db (2.6.7.1-1ubuntu1) werden verarbeitet ...
unace (1.2b-10+deb7u1build0.14.04.1) wird eingerichtet ... 
```

Enter fullscreen mode Exit fullscreen mode

### sudo apt-get 安装 kompare

```
sudo apt-get install kompare
Paketlisten werden gelesen... Fertig
Abhängigkeitsbaum wird aufgebaut.       
Statusinformationen werden eingelesen.... Fertig
Die folgenden zusätzlichen Pakete werden installiert:
  libkomparediff2-4
Die folgenden NEUEN Pakete werden installiert:
  kompare libkomparediff2-4
0 aktualisiert, 2 neu installiert, 0 zu entfernen und 3 nicht aktualisiert.
Es müssen 437 kB an Archiven heruntergeladen werden.
Nach dieser Operation werden 1.177 kB Plattenplatz zusätzlich benutzt.
Möchten Sie fortfahren? [J/n] y
Holen: 1 http://ftp.fau.de/mint/packages/ rosa/import libkomparediff2-4 amd64 4:4.14.2-0ubuntu1~ubuntu14.04~ppa1 [56,9 kB]
Holen: 2 http://ftp.fau.de/mint/packages/ rosa/import kompare amd64 4:4.14.1-0ubuntu1~ubuntu14.04~ppa1 [380 kB]
Es wurden 437 kB in 0 s geholt (1.754 kB/s).
Vormals nicht ausgewähltes Paket libkomparediff2-4 wird gewählt.
(Lese Datenbank ... 609687 Dateien und Verzeichnisse sind derzeit installiert.)
Vorbereitung zum Entpacken von .../libkomparediff2-4_4%3a4.14.2-0ubuntu1~ubuntu14.04~ppa1_amd64.deb ...
Entpacken von libkomparediff2-4 (4:4.14.2-0ubuntu1~ubuntu14.04~ppa1) ...
Vormals nicht ausgewähltes Paket kompare wird gewählt.
Vorbereitung zum Entpacken von .../kompare_4%3a4.14.1-0ubuntu1~ubuntu14.04~ppa1_amd64.deb ...
Entpacken von kompare (4:4.14.1-0ubuntu1~ubuntu14.04~ppa1) ...
Trigger für desktop-file-utils (0.22-1ubuntu1.1) werden verarbeitet ...
Trigger für mime-support (3.54ubuntu1.1) werden verarbeitet ...
Trigger für man-db (2.6.7.1-1ubuntu1) werden verarbeitet ...
Trigger für hicolor-icon-theme (0.13-1) werden verarbeitet ...
libkomparediff2-4 (4:4.14.2-0ubuntu1~ubuntu14.04~ppa1) wird eingerichtet ...
kompare (4:4.14.1-0ubuntu1~ubuntu14.04~ppa1) wird eingerichtet ...
Trigger für libc-bin (2.19-0ubuntu6.13) werden verarbeitet ... 
```

Enter fullscreen mode Exit fullscreen mode

### 图片

*   [https://thepractical dev . S3 . Amazon AWS . com/I/p 0 spwzh 8 sa 8732 m2 RSI . png](https://thepracticaldev.s3.amazonaws.com/i/p0spwzh8sa8732im2rsi.png)