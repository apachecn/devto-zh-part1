# sudo apt-get install -y php7.1 软件包...

> 原文：<https://dev.to/psnebc/sudo-apt-get-install--y-php71-packages--89>

```
sudo apt-get install -y php7.1 libapache2-mod-php7.1 php7.1 php7.1-common php7.1-gd php7.1-mysql php7.1-mcrypt php7.1-curl php7.1-intl php7.1-xsl php7.1-mbstring php7.1-zip php7.1-bcmath php7.1-iconv php7.1-soap 
```

Enter fullscreen mode Exit fullscreen mode

### 在 Linux Mint 17.3 上输出 LTS 罗莎

```
Son 22 Okt - 18:00
@patrick î‚° sudo apt-get install -y php7.1 libapache2-mod-php7.1 php7.1 php7.1-common php7.1-gd php7.1-mysql php7.1-mcrypt php7.1-curl php7.1-intl php7.1-xsl php7.1-mbstring php7.1-zip php7.1-bcmath php7.1-iconv php7.1-soap
Paketlisten werden gelesen... Fertig
AbhÃ¤ngigkeitsbaum wird aufgebaut.       
Statusinformationen werden eingelesen.... Fertig
Hinweis: Â»php7.1-commonÂ« wird an Stelle von Â»php7.1-iconvÂ« gewÃ¤hlt.
libapache2-mod-php7.1 ist schon die neueste Version.
libapache2-mod-php7.1 wurde als manuell installiert festgelegt.
php7.1-bcmath ist schon die neueste Version.
php7.1-common ist schon die neueste Version.
php7.1-curl ist schon die neueste Version.
php7.1-intl ist schon die neueste Version.
php7.1-mbstring ist schon die neueste Version.
php7.1-mysql ist schon die neueste Version.
php7.1 ist schon die neueste Version.
Die folgenden NEUEN Pakete werden installiert:
  php7.1-gd php7.1-mcrypt php7.1-soap php7.1-xsl php7.1-zip
0 aktualisiert, 5 neu installiert, 0 zu entfernen und 6 nicht aktualisiert.
Es mÃ¼ssen 229 kB an Archiven heruntergeladen werden.
Nach dieser Operation werden 925 kB Plattenplatz zusÃ¤tzlich benutzt.
Holen: 1 http://ppa.launchpad.net/ondrej/php/ubuntu/ trusty/main php7.1-gd amd64 7.1.10-1+ubuntu14.04.1+deb.sury.org+1 [27,4 kB]
Holen: 2 http://ppa.launchpad.net/ondrej/php/ubuntu/ trusty/main php7.1-mcrypt amd64 7.1.10-1+ubuntu14.04.1+deb.sury.org+1 [14,6 kB]
Holen: 3 http://ppa.launchpad.net/ondrej/php/ubuntu/ trusty/main php7.1-soap amd64 7.1.10-1+ubuntu14.04.1+deb.sury.org+1 [114 kB]
Holen: 4 http://ppa.launchpad.net/ondrej/php/ubuntu/ trusty/main php7.1-xsl all 7.1.10-1+ubuntu14.04.1+deb.sury.org+1 [53,3 kB]
Holen: 5 http://ppa.launchpad.net/ondrej/php/ubuntu/ trusty/main php7.1-zip amd64 7.1.10-1+ubuntu14.04.1+deb.sury.org+1 [19,8 kB]
Es wurden 229 kB in 1 s geholt (200 kB/s).
Vormals nicht ausgewÃ¤hltes Paket php7.1-gd wird gewÃ¤hlt.
(Lese Datenbank ... 607174 Dateien und Verzeichnisse sind derzeit installiert.)
Vorbereitung zum Entpacken von .../php7.1-gd_7.1.10-1+ubuntu14.04.1+deb.sury.org+1_amd64.deb ...
Entpacken von php7.1-gd (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) ...
Vormals nicht ausgewÃ¤hltes Paket php7.1-mcrypt wird gewÃ¤hlt.
Vorbereitung zum Entpacken von .../php7.1-mcrypt_7.1.10-1+ubuntu14.04.1+deb.sury.org+1_amd64.deb ...
Entpacken von php7.1-mcrypt (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) ...
Vormals nicht ausgewÃ¤hltes Paket php7.1-soap wird gewÃ¤hlt.
Vorbereitung zum Entpacken von .../php7.1-soap_7.1.10-1+ubuntu14.04.1+deb.sury.org+1_amd64.deb ...
Entpacken von php7.1-soap (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) ...
Vormals nicht ausgewÃ¤hltes Paket php7.1-xsl wird gewÃ¤hlt.
Vorbereitung zum Entpacken von .../php7.1-xsl_7.1.10-1+ubuntu14.04.1+deb.sury.org+1_all.deb ...
Entpacken von php7.1-xsl (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) ...
Vormals nicht ausgewÃ¤hltes Paket php7.1-zip wird gewÃ¤hlt.
Vorbereitung zum Entpacken von .../php7.1-zip_7.1.10-1+ubuntu14.04.1+deb.sury.org+1_amd64.deb ...
Entpacken von php7.1-zip (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) ...
Trigger fÃ¼r libapache2-mod-php7.1 (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) werden verarbeitet ...
php7.1-gd (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) wird eingerichtet ...

Creating config file /etc/php/7.1/mods-available/gd.ini with new version
php7.1-mcrypt (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) wird eingerichtet ...

Creating config file /etc/php/7.1/mods-available/mcrypt.ini with new version
php7.1-soap (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) wird eingerichtet ...

Creating config file /etc/php/7.1/mods-available/soap.ini with new version
php7.1-xsl (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) wird eingerichtet ...
php7.1-zip (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) wird eingerichtet ...

Creating config file /etc/php/7.1/mods-available/zip.ini with new version
Trigger fÃ¼r libapache2-mod-php7.1 (7.1.10-1+ubuntu14.04.1+deb.sury.org+1) werden verarbeitet ... 
```

Enter fullscreen mode Exit fullscreen mode

```
Son 22 Okt - 18:06
@patrick î‚° php -me
[PHP Modules]
bcmath
calendar
Core
ctype
curl
date dom
exif
fileinfo
filter
ftp
gd
gettext
hash iconv
imagick
intl
json
libxml
mbstring
mcrypt
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
Phar
posix
readline
Reflection
session
shmop
SimpleXML
soap
sockets
SPL
standard
sysvmsg
sysvsem
sysvshm
tokenizer
wddx
xml
xmlreader
xmlwriter
xsl
Zend OPcache
zip
zlib

[Zend Modules]
Zend OPcache 
```

Enter fullscreen mode Exit fullscreen mode