# wget Lucene/Solr/7 . 0 . 1/Solr-7 . 0 . 1 . tgz

> 原文:# t0]https://dev . to/psnec/wget-Lucene sar 701 Solr-701 tgz-c9d

### 安装

* * *

```
Mon 16 Okt - 19:11 
@patrick  cd /tmp 
```

Enter fullscreen mode Exit fullscreen mode

```
Mon 16 Okt - 19:12  /tmp 
@patrick  wget http://www-eu.apache.org/dist/lucene/solr/7.0.1/solr-7.0.1.tgz
--2017-10-16 19:14:28--  http://www-eu.apache.org/dist/lucene/solr/7.0.1/solr-7.0.1.tgz
Auflösen des Hostnamens »www-eu.apache.org (www-eu.apache.org)« … 195.154.151.36, 2001:bc8:2142:300::
Verbindungsaufbau zu www-eu.apache.org (www-eu.apache.org)|195.154.151.36|:80 … verbunden.
HTTP-Anforderung gesendet, auf Antwort wird gewartet … 200 OK
Länge: 149663435 (143M) [application/x-gzip]
Wird in »»solr-7.0.1.tgz«« gespeichert.

solr-7.0.1.tgz                                  100%[=====================================================================================================>] 142,73M   508KB/s    in 12m 14s 

2017-10-16 19:26:43 (199 KB/s) - »»solr-7.0.1.tgz«« gespeichert [149663435/149663435] 
```

Enter fullscreen mode Exit fullscreen mode

```
Mon 16 Okt - 19:26  /tmp 
@patrick  tar xzf solr-7.0.1.tgz solr-7.0.1/bin/install_solr_service.sh --strip-components=2 
```

Enter fullscreen mode Exit fullscreen mode

```
Mon 16 Okt - 19:27  /tmp 
@patrick  sudo ./install_solr_service.sh solr-7.0.1.tgz
[sudo] password for patrick: 
id: solr: Einen solchen Benutzer gibt es nicht
Creating new user: solr
Lege Systembenutzer »solr« (UID 133) an ...
Lege neue Gruppe »solr« (GID 141) an ...
Lege neuen Benutzer »solr« (UID 133) mit Gruppe »solr« an ...
Erstelle Home-Verzeichnis »/var/solr« ...

Extracting solr-7.0.1.tgz to /opt

Installing symlink /opt/solr -> /opt/solr-7.0.1 ...

Installing /etc/init.d/solr script ...

Installing /etc/default/solr.in.sh ...

 Adding system startup for /etc/init.d/solr ...
   /etc/rc0.d/K20solr -> ../init.d/solr
   /etc/rc1.d/K20solr -> ../init.d/solr
   /etc/rc6.d/K20solr -> ../init.d/solr
   /etc/rc2.d/S20solr -> ../init.d/solr
   /etc/rc3.d/S20solr -> ../init.d/solr
   /etc/rc4.d/S20solr -> ../init.d/solr
   /etc/rc5.d/S20solr -> ../init.d/solr
Service solr installed.
Customize Solr startup configuration in /etc/default/solr.in.sh
Waiting up to 180 seconds to see Solr running on port 8983 [\]  
Started Solr server on port 8983 (pid=5546). Happy searching!

Found 1 Solr nodes: 

Solr process 5546 running on port 8983
{
  "solr_home":"/var/solr/data",
  "version":"7.0.1 8d6c3889aa543954424d8ac1dbb3f03bf207140b - sarowe - 2017-10-02 14:39:33",
  "startTime":"2017-10-16T17:27:44.953Z",
  "uptime":"0 days, 0 hours, 0 minutes, 10 seconds",
  "memory":"22.8 MB (%4.6) of 490.7 MB"} 
```

Enter fullscreen mode Exit fullscreen mode

```
 Mon 16 Okt - 19:39  /tmp 
@patrick  sudo su - solr -c "/opt/solr/bin/solr create -c drupal -n data_driven_schema_configs"

Copying configuration to new core instance directory:
/var/solr/data/drupal

Creating new core 'drupal' using command:
http://localhost:8983/solr/admin/cores?action=CREATE&name=drupal&instanceDir=drupal

{
  "responseHeader":{
    "status":0,
    "QTime":1676},
  "core":"drupal"} 
```

Enter fullscreen mode Exit fullscreen mode

### 图片

*   [https://thepractical dev . S3 . Amazon AWS . com/I/9 a 55 TJ 5 az 2 enhdjqphgk . png](https://thepracticaldev.s3.amazonaws.com/i/9a55tj5az2enhdjqphgk.png)

### 链接

*   [http://www-eu.apache.org/dist/lucene/solr/7.0.1/](http://www-eu.apache.org/dist/lucene/solr/7.0.1/)