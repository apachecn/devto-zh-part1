# wget gitea-1 . 2 . 0-rc3-Linux-amd64

> 原文：<https://dev.to/psnebc/wget-gitea-120-rc3-linux-amd64-a8f>

```
➜  ~ wget -O gitea https://dl.gitea.io/gitea/1.2.0-rc3/gitea-1.2.0-rc3-linux-amd64
--2017-10-15 14:11:46--  https://dl.gitea.io/gitea/1.2.0-rc3/gitea-1.2.0-rc3-linux-amd64
Resolving dl.gitea.io (dl.gitea.io)... 104.27.143.155, 104.27.142.155, 2400:cb00:2048:1::681b:8f9b, ...
Connecting to dl.gitea.io (dl.gitea.io)|104.27.143.155|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 51521136 (49M) [application/octet-stream]
Saving to: ‘gitea’

gitea                                           100%[=====================================================================================================>]  49.13M  1.24MB/s    in 42s     

2017-10-15 14:12:29 (1.18 MB/s) - ‘gitea’ saved [51521136/51521136] 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ chmod +x gitea 
```

Enter fullscreen mode Exit fullscreen mode

*   启动 Gitea(首次启动)

```
➜  ~ ./gitea web
2017/10/15 14:13:24 [W] Custom config '/home/vagrant/custom/conf/app.ini' not found, ignore this if you're running first time
2017/10/15 14:13:24 [T] Custom path: /home/vagrant/custom
2017/10/15 14:13:24 [T] Log path: /home/vagrant/log
2017/10/15 14:13:24 [I] Gitea v1.2.0+rc3 built with: bindata, sqlite
2017/10/15 14:13:24 [I] Log Mode: Console(Trace)
2017/10/15 14:13:24 [I] XORM Log Mode: Console(Trace)
2017/10/15 14:13:24 [I] Cache Service Enabled
2017/10/15 14:13:24 [I] Session Service Enabled
2017/10/15 14:13:24 [I] SQLite3 Supported
2017/10/15 14:13:24 Serving [::]:3000 with pid 1877
2017/10/15 14:13:24 [I] Run Mode: Development
2017/10/15 14:13:24 [I] Listen: http://0.0.0.0:3000
[Macaron] 2017-10-15 14:13:44: Started GET / for 192.168.33.1
[Macaron] 2017-10-15 14:13:44: Completed / 302 Found in 348.432µs
2017/10/15 14:13:44 [D] Session ID: 860b458ea7d1ebc8
2017/10/15 14:13:44 [D] CSRF Token: KQJghxArlFXPaEHdxTojZstchVM6MTUwODA3NjgyNDUxMDMwNTEyMg==
[Macaron] 2017-10-15 14:13:44: Started GET /robots.txt for 192.168.33.1
2017/10/15 14:13:44 [D] Session ID: f4c0a3cd4d85f9f1
[Macaron] 2017-10-15 14:13:44: Started GET /install for 192.168.33.1
2017/10/15 14:13:44 [D] CSRF Token: W1VANRkjhjhyXv2hQMEughVL2bk6MTUwODA3NjgyNDUyMTYzODY4MQ==
2017/10/15 14:13:44 [D] Template: status/404
[Macaron] 2017-10-15 14:13:44: Completed /robots.txt 404 Not Found in 125.076789ms
2017/10/15 14:13:44 [D] Session ID: 860b458ea7d1ebc8
[Macaron] 2017-10-15 14:13:44: Completed /install 200 OK in 115.98633ms
2017/10/15 14:13:44 [D] CSRF Token: KQJghxArlFXPaEHdxTojZstchVM6MTUwODA3NjgyNDUxMDMwNTEyMg==
2017/10/15 14:13:44 [D] Template: install
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/assets/octicons/octicons.min.css for 192.168.33.1
[Macaron] [Static] Serving /vendor/assets/octicons/octicons.min.css
[Macaron] 2017-10-15 14:13:44: Completed /vendor/assets/octicons/octicons.min.css 200 OK in 5.171318ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/assets/font-awesome/css/font-awesome.min.css for 192.168.33.1
[Macaron] [Static] Serving /vendor/assets/font-awesome/css/font-awesome.min.css
[Macaron] 2017-10-15 14:13:44: Completed /vendor/assets/font-awesome/css/font-awesome.min.css 200 OK in 9.122425ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/semantic/semantic.min.css for 192.168.33.1
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/cssrelpreload/cssrelpreload.min.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/cssrelpreload/cssrelpreload.min.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/cssrelpreload/cssrelpreload.min.js 200 OK in 9.307713ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/cssrelpreload/loadCSS.min.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/cssrelpreload/loadCSS.min.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/cssrelpreload/loadCSS.min.js 200 OK in 2.671216ms
[Macaron] 2017-10-15 14:13:44: Started GET /css/index.css?v=573d3d58a1901c088bcedbf1fab14c4c for 192.168.33.1
[Macaron] [Static] Serving /css/index.css
[Macaron] 2017-10-15 14:13:44: Completed /css/index.css?v=573d3d58a1901c088bcedbf1fab14c4c 200 OK in 14.283915ms
[Macaron] [Static] Serving /vendor/plugins/semantic/semantic.min.css
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/semantic/semantic.min.css 200 OK in 63.779972ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/jquery.areyousure/jquery.are-you-sure.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/jquery.areyousure/jquery.are-you-sure.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/jquery.areyousure/jquery.are-you-sure.js 200 OK in 1.727701ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/jquery/jquery.min.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/jquery/jquery.min.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/jquery/jquery.min.js 200 OK in 7.33599ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/emojify/emojify.min.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/emojify/emojify.min.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/emojify/emojify.min.js 200 OK in 17.510201ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/autolink/autolink.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/autolink/autolink.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/autolink/autolink.js 200 OK in 11.397852ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/vue/vue.min.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/vue/vue.min.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/vue/vue.min.js 200 OK in 24.068655ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/clipboard/clipboard.min.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/clipboard/clipboard.min.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/clipboard/clipboard.min.js 200 OK in 3.765909ms
[Macaron] 2017-10-15 14:13:44: Started GET /js/index.js?v=573d3d58a1901c088bcedbf1fab14c4c for 192.168.33.1
[Macaron] [Static] Serving /js/index.js
[Macaron] 2017-10-15 14:13:44: Completed /js/index.js?v=573d3d58a1901c088bcedbf1fab14c4c 200 OK in 7.528655ms
[Macaron] 2017-10-15 14:13:44: Started GET /vendor/plugins/semantic/semantic.min.js for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/semantic/semantic.min.js
[Macaron] 2017-10-15 14:13:44: Completed /vendor/plugins/semantic/semantic.min.js 200 OK in 20.21526ms
[Macaron] 2017-10-15 14:13:45: Started GET /vendor/plugins/semantic/themes/default/assets/fonts/icons.woff2 for 192.168.33.1
[Macaron] [Static] Serving /vendor/plugins/semantic/themes/default/assets/fonts/icons.woff2
[Macaron] 2017-10-15 14:13:45: Completed /vendor/plugins/semantic/themes/default/assets/fonts/icons.woff2 200 OK in 2.514113ms
[Macaron] 2017-10-15 14:13:46: Started GET /img/favicon.png for 192.168.33.1
[Macaron] [Static] Serving /img/favicon.png
[Macaron] 2017-10-15 14:13:46: Completed /img/favicon.png 200 OK in 1.755154ms
[Macaron] 2017-10-15 14:17:35: Started POST /install for 192.168.33.1
2017/10/15 14:17:35 [I] [SQL] SELECT `TABLE_NAME`, `ENGINE`, `TABLE_ROWS`, `AUTO_INCREMENT`, `TABLE_COMMENT` from `INFORMATION_SCHEMA`.`TABLES` WHERE `TABLE_SCHEMA`=? AND (`ENGINE`='MyISAM' OR `ENGINE` = 'InnoDB' OR `ENGINE` = 'TokuDB') [gitea]
2017/10/15 14:17:35 [D] Session ID: 860b458ea7d1ebc8
2017/10/15 14:17:35 [D] CSRF Token: KQJghxArlFXPaEHdxTojZstchVM6MTUwODA3NjgyNDUxMDMwNTEyMg==
2017/10/15 14:17:35 [D] Template: install
[Macaron] 2017-10-15 14:17:35: Completed /install 200 OK in 43.055599ms
[Macaron] 2017-10-15 14:18:00: Started POST /install for 192.168.33.1
2017/10/15 14:18:00 [I] [SQL] SELECT `TABLE_NAME`, `ENGINE`, `TABLE_ROWS`, `AUTO_INCREMENT`, `TABLE_COMMENT` from `INFORMATION_SCHEMA`.`TABLES` WHERE `TABLE_SCHEMA`=? AND (`ENGINE`='MyISAM' OR `ENGINE` = 'InnoDB' OR `ENGINE` = 'TokuDB') [gitea]
2017/10/15 14:18:00 [D] Session ID: 860b458ea7d1ebc8
2017/10/15 14:18:00 [D] CSRF Token: KQJghxArlFXPaEHdxTojZstchVM6MTUwODA3NjgyNDUxMDMwNTEyMg==
2017/10/15 14:18:00 [D] Template: install
[Macaron] 2017-10-15 14:18:00: Completed /install 200 OK in 38.644534ms
[Macaron] 2017-10-15 14:19:00: Started POST /install for 192.168.33.1
2017/10/15 14:19:00 [D] Session ID: 860b458ea7d1ebc8
2017/10/15 14:19:00 [I] [SQL] SELECT `TABLE_NAME`, `ENGINE`, `TABLE_ROWS`, `AUTO_INCREMENT`, `TABLE_COMMENT` from `INFORMATION_SCHEMA`.`TABLES` WHERE `TABLE_SCHEMA`=? AND (`ENGINE`='MyISAM' OR `ENGINE` = 'InnoDB' OR `ENGINE` = 'TokuDB') [gitea]
[Macaron] 2017-10-15 14:19:00: Started GET /robots.txt for 192.168.33.1
2017/10/15 14:19:00 [D] CSRF Token: KQJghxArlFXPaEHdxTojZstchVM6MTUwODA3NjgyNDUxMDMwNTEyMg==
2017/10/15 14:19:00 [D] Session ID: 5a76718747ae39c0
2017/10/15 14:19:00 [D] CSRF Token: fB0WoOyYWDHc0SrpmuxMuZnM6k46MTUwODA3NzE0MDQ0NDgyNzEyMw==
2017/10/15 14:19:00 [D] Template: status/404
[Macaron] 2017-10-15 14:19:00: Completed /robots.txt 404 Not Found in 115.734449ms
2017/10/15 14:19:01 [I] PING DATABASE mysql
2017/10/15 14:19:01 [I] [SQL] SELECT `TABLE_NAME` from `INFORMATION_SCHEMA`.`TABLES` WHERE `TABLE_SCHEMA`=? and `TABLE_NAME`=? []interface {}{"gitea", "version"}
2017/10/15 14:19:01 [I] [SQL] CREATE TABLE IF NOT EXISTS `version` (`id` BIGINT(20) PRIMARY KEY AUTO_INCREMENT NOT NULL, `version` BIGINT(20) NULL) DEFAULT CHARSET utf8
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? AND `COLUMN_NAME` = ? [gitea version id]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? AND `COLUMN_NAME` = ? [gitea version version]
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `version` FROM `version` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:01 [I] [SQL] INSERT INTO `version` (`version`) VALUES (?) []interface {}{41}
2017/10/15 14:19:01 [I] [SQL] SELECT `TABLE_NAME`, `ENGINE`, `TABLE_ROWS`, `AUTO_INCREMENT`, `TABLE_COMMENT` from `INFORMATION_SCHEMA`.`TABLES` WHERE `TABLE_SCHEMA`=? AND (`ENGINE`='MyISAM' OR `ENGINE` = 'InnoDB' OR `ENGINE` = 'TokuDB') [gitea]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea access]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea access]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea access_token]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea access_token]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea action]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea action]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea attachment]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea attachment]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea collaboration]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea collaboration]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea comment]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea comment]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea commit_status]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea commit_status]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea deploy_key]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea deploy_key]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea email_address]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea email_address]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea external_login_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea external_login_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea follow]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea follow]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea gpg_key]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea gpg_key]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea hook_task]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea hook_task]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue_label]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue_label]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue_watch]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea issue_watch]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea label]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea label]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea lfs_meta_object]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea lfs_meta_object]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea login_source]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea login_source]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea milestone]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea milestone]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea mirror]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea mirror]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea notice]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea notice]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea notification]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea notification]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea org_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea org_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea protected_branch]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea protected_branch]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea public_key]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea public_key]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea pull_request]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea pull_request]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea release]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea release]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea repo_redirect]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea repo_redirect]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea repo_unit]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea repo_unit]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea repository]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea repository]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea star]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea star]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea team]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea team]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea team_repo]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea team_repo]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea team_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea team_user]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea two_factor]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea two_factor]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea upload]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea upload]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea user]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea user]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea user_open_id]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea user_open_id]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea version]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea version]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea watch]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea watch]
2017/10/15 14:19:01 [I] [SQL] SELECT `COLUMN_NAME`, `IS_NULLABLE`, `COLUMN_DEFAULT`, `COLUMN_TYPE`, `COLUMN_KEY`, `EXTRA`,`COLUMN_COMMENT` FROM `INFORMATION_SCHEMA`.`COLUMNS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea webhook]
2017/10/15 14:19:01 [I] [SQL] SELECT `INDEX_NAME`, `NON_UNIQUE`, `COLUMN_NAME` FROM `INFORMATION_SCHEMA`.`STATISTICS` WHERE `TABLE_SCHEMA` = ? AND `TABLE_NAME` = ? [gitea webhook]
2017/10/15 14:19:01 [W] Table user Column allow_create_organization db default is 1, struct default is true
2017/10/15 14:19:01 [W] Table repository Column is_fork db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table action Column is_deleted db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table action Column is_private db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table mirror Column enable_prune db default is 1, struct default is true
2017/10/15 14:19:01 [W] Table release Column is_draft db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table release Column is_prerelease db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table release Column is_tag db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table login_source Column is_actived db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table login_source Column is_sync_enabled db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table protected_branch Column can_push db default is 0, struct default is false
2017/10/15 14:19:01 [W] Table user_open_id Column show db default is 0, struct default is false
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `type`, `name`, `is_actived`, `is_sync_enabled`, `cfg`, `created_unix`, `updated_unix` FROM `login_source` WHERE is_actived = ? and type = ? []interface {}{true, 6}
2017/10/15 14:19:01 [I] [SQL] SELECT repo.id FROM `repository` repo WHERE repo.num_watches!=(SELECT COUNT(*) FROM `watch` WHERE repo_id=repo.id)
2017/10/15 14:19:01 [I] [SQL] SELECT repo.id FROM `repository` repo WHERE repo.num_stars!=(SELECT COUNT(*) FROM `star` WHERE repo_id=repo.id)
2017/10/15 14:19:01 [I] [SQL] SELECT label.id FROM `label` WHERE label.num_issues!=(SELECT COUNT(*) FROM `issue_label` WHERE label_id=label.id)
2017/10/15 14:19:01 [I] [SQL] SELECT `user`.id FROM `user` WHERE `user`.num_repos!=(SELECT COUNT(*) FROM `repository` WHERE owner_id=`user`.id)
2017/10/15 14:19:01 [I] [SQL] SELECT `issue`.id FROM `issue` WHERE `issue`.num_comments!=(SELECT COUNT(*) FROM `comment` WHERE issue_id=`issue`.id AND type=0)
2017/10/15 14:19:01 [I] [SQL] SELECT repo.id FROM `repository` repo WHERE repo.num_closed_issues!=(SELECT COUNT(*) FROM `issue` WHERE repo_id=repo.id AND is_closed=? AND is_pull=?) []interface {}{true, false}
2017/10/15 14:19:01 [I] [SQL] SELECT repo.id FROM `repository` repo WHERE repo.num_forks!=(SELECT COUNT(*) FROM `repository` WHERE fork_id=repo.id)
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE id > 0
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` ORDER BY id ASC LIMIT 10
2017/10/15 14:19:01 [I] [SQL] SELECT count(*) FROM `repository` WHERE id > 0 AND is_private=? []interface {}{false}
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE id!=? AND `lower_name`=? LIMIT 1 []interface {}{0, "vagrant"}
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE email=? LIMIT 1 []interface {}{"patrick.schanen@gmail.com"}
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `uid`, `email`, `is_activated` FROM `email_address` WHERE `email`=? LIMIT 1 []interface {}{"patrick.schanen@gmail.com"}
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `repo_id`, `hook_id`, `uuid`, `type`, `url`, `payload_content`, `content_type`, `event_type`, `is_ssl`, `is_delivered`, `delivered`, `is_succeed`, `request_content`, `response_content` FROM `hook_task` WHERE is_delivered=? []interface {}{false}
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `type`, `status`, `issue_id`, `index`, `head_repo_id`, `base_repo_id`, `head_user_name`, `head_branch`, `base_branch`, `merge_base`, `has_merged`, `merged_commit_id`, `merger_id`, `merged_unix` FROM `pull_request` WHERE status = ? []interface {}{1}
2017/10/15 14:19:01 [I] [SQL] BEGIN TRANSACTION
2017/10/15 14:19:01 [I] [SQL] INSERT INTO `user` (`lower_name`,`name`,`full_name`,`email`,`keep_email_private`,`passwd`,`login_type`,`login_source`,`login_name`,`type`,`location`,`website`,`rands`,`salt`,`created_unix`,`updated_unix`,`last_login_unix`,`last_repo_visibility`,`max_repo_creation`,`is_active`,`is_admin`,`allow_git_hook`,`allow_import_local`,`allow_create_organization`,`prohibit_login`,`avatar`,`avatar_email`,`use_custom_avatar`,`num_followers`,`num_following`,`num_stars`,`num_repos`,`description`,`num_teams`,`num_members`,`diff_view_style`) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?) []interface {}{"vagrant", "vagrant", "", "patrick.schanen@gmail.com", false, "501ea2648a8236488f61e1f8cd54af1071eb15723443f11dff3de466156fd46aac8194de42f95af0ab9c858c6ffbf415231e", 0, 0, "", 0, "", "", "8H3JsQMjiw", "gT9rmKyQGj", 1508077141, 1508077141, 0, false, -1, true, true, false, false, true, false, "f5c54875086d4ec1285ba4334fc6436e", "patrick.schanen@gmail.com", false, 0, 0, 0, 0, "", 0, 0, ""}
2017/10/15 14:19:01 [I] [SQL] COMMIT
[Macaron] 2017-10-15 14:19:01: Completed /install 302 Found in 1.206010991s
[Macaron] 2017-10-15 14:19:01: Started GET /user/login for 192.168.33.1
[Macaron] 2017-10-15 14:19:01: Completed /user/login 302 Found in 1.451982ms
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:01 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
[Macaron] 2017-10-15 14:19:01: Started GET / for 192.168.33.1
[Macaron] 2017-10-15 14:19:01: Completed / 200 OK in 6.272737ms
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:01 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:19:01 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE owner_id = ? AND is_mirror = ? []interface {}{1, true}
2017/10/15 14:19:01 [I] [SQL] SELECT `id`, `user_id`, `op_type`, `act_user_id`, `repo_id`, `comment_id`, `is_deleted`, `ref_name`, `is_private`, `content`, `created_unix` FROM `action` WHERE user_id=? AND is_deleted=? ORDER BY `id` DESC LIMIT 20 []interface {}{1, false}
2017/10/15 14:19:01 [I] [SQL] SELECT count(*) FROM `org_user` WHERE uid=? []interface {}{1}
[Macaron] 2017-10-15 14:19:01: Started GET /img/gitea-sm.png for 192.168.33.1
[Macaron] [Static] Serving /img/gitea-sm.png
[Macaron] 2017-10-15 14:19:01: Completed /img/gitea-sm.png 200 OK in 1.857375ms
[Macaron] 2017-10-15 14:19:02: Started GET /vendor/assets/octicons/octicons.woff2?ef21c39f0ca9b1b5116e5eb7ac5eabe6 for 192.168.33.1
[Macaron] [Static] Serving /vendor/assets/octicons/octicons.woff2
[Macaron] 2017-10-15 14:19:02: Completed /vendor/assets/octicons/octicons.woff2?ef21c39f0ca9b1b5116e5eb7ac5eabe6 200 OK in 1.29911ms
[Macaron] 2017-10-15 14:19:02: Started GET /api/v1/repos/search?uid=1&q=&limit=15 for 192.168.33.1
[Macaron] 2017-10-15 14:19:02: Completed /api/v1/repos/search?uid=1&q=&limit=15 200 OK in 2.886141ms
2017/10/15 14:19:02 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:02 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:19:02 [I] [SQL] SELECT count(*) FROM `repository` WHERE (owner_id=? OR owner_id IN (?) OR id IN (SELECT repo_id FROM `access` WHERE access.user_id = ? AND owner_id != ?)) []interface {}{1, 1, 1, 1}
2017/10/15 14:19:02 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE (owner_id=? OR owner_id IN (?) OR id IN (SELECT repo_id FROM `access` WHERE access.user_id = ? AND owner_id != ?)) ORDER BY name ASC LIMIT 15 []interface {}{1, 1, 1, 1}
[Macaron] 2017-10-15 14:19:24: Started GET /repo/create for 192.168.33.1
2017/10/15 14:19:24 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:24 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:19:24 [I] [SQL] SELECT * FROM `user` INNER JOIN `org_user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? AND `org_user`.is_owner=? ORDER BY `updated_unix` DESC, `user`.`name` ASC []interface {}{1, true}
[Macaron] 2017-10-15 14:19:24: Completed /repo/create 200 OK in 8.2283ms
[Macaron] 2017-10-15 14:19:42: Started POST /repo/create for 192.168.33.1
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:42 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:19:42 [I] [SQL] SELECT * FROM `user` INNER JOIN `org_user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? AND `org_user`.is_owner=? ORDER BY `updated_unix` DESC, `user`.`name` ASC []interface {}{1, true}
2017/10/15 14:19:42 [I] [SQL] BEGIN TRANSACTION
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
2017/10/15 14:19:42 [I] [SQL] INSERT INTO `repository` (`owner_id`,`lower_name`,`name`,`description`,`website`,`default_branch`,`num_watches`,`num_stars`,`num_forks`,`num_issues`,`num_closed_issues`,`num_pulls`,`num_closed_pulls`,`num_milestones`,`num_closed_milestones`,`is_private`,`is_bare`,`is_mirror`,`is_fork`,`fork_id`,`size`,`created_unix`,`updated_unix`) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?) []interface {}{1, "test", "Test", "", "", "", 0, 0, 0, 0, 0, 0, 0, 0, 0, false, false, false, false, 0, 0, 1508077182, 1508077182}
2017/10/15 14:19:42 [I] [SQL] DELETE FROM `repo_redirect` WHERE `owner_id`=? AND `lower_name`=? []interface {}{1, "test"}
2017/10/15 14:19:42 [I] [SQL] INSERT INTO `repo_unit` (`repo_id`, `type`, `index`, `config`, `created_unix`) VALUES (?, ?, ?, ?, ?),(?, ?, ?, ?, ?),(?, ?, ?, ?, ?),(?, ?, ?, ?, ?),(?, ?, ?, ?, ?) []interface {}{1, 1, 0, interface {}(nil), 1508077182, 1, 2, 1, interface {}(nil), 1508077182, 1, 3, 2, interface {}(nil), 1508077182, 1, 4, 3, interface {}(nil), 1508077182, 1, 5, 4, interface {}(nil), 1508077182}
2017/10/15 14:19:42 [I] [SQL] UPDATE `user` SET `lower_name` = ?, `name` = ?, `full_name` = ?, `email` = ?, `keep_email_private` = ?, `passwd` = ?, `login_type` = ?, `login_source` = ?, `login_name` = ?, `type` = ?, `location` = ?, `website` = ?, `rands` = ?, `salt` = ?, `created_unix` = ?, `updated_unix` = ?, `last_login_unix` = ?, `last_repo_visibility` = ?, `max_repo_creation` = ?, `is_active` = ?, `is_admin` = ?, `allow_git_hook` = ?, `allow_import_local` = ?, `allow_create_organization` = ?, `prohibit_login` = ?, `avatar` = ?, `avatar_email` = ?, `use_custom_avatar` = ?, `num_followers` = ?, `num_following` = ?, `num_stars` = ?, `num_repos` = ?, `description` = ?, `num_teams` = ?, `num_members` = ?, `diff_view_style` = ? WHERE `id`=? []interface {}{"vagrant", "vagrant", "", "patrick.schanen@gmail.com", false, "501ea2648a8236488f61e1f8cd54af1071eb15723443f11dff3de466156fd46aac8194de42f95af0ab9c858c6ffbf415231e", 0, 0, "", 0, "", "", "8H3JsQMjiw", "gT9rmKyQGj", 1508077141, 1508077182, 0, false, -1, true, true, false, false, true, false, "f5c54875086d4ec1285ba4334fc6436e", "patrick.schanen@gmail.com", false, 0, 0, 0, 1, "", 0, 0, "", 1}
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `repo_id`, `user_id`, `mode` FROM `collaboration` WHERE `repo_id`=? []interface {}{1}
2017/10/15 14:19:42 [I] [SQL] DELETE FROM `access` WHERE `repo_id`=? []interface {}{1}
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:19:42 [I] [SQL] INSERT INTO `watch` (`user_id`,`repo_id`) VALUES (?, ?) []interface {}{1, 1}
2017/10/15 14:19:42 [I] [SQL] UPDATE `repository` SET num_watches = num_watches + 1 WHERE id = ? []interface {}{1}
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `repo_id`=? []interface {}{1}
2017/10/15 14:19:42 [I] [SQL] INSERT INTO `action` (`user_id`,`op_type`,`act_user_id`,`repo_id`,`comment_id`,`is_deleted`,`ref_name`,`is_private`,`content`,`created_unix`) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?) []interface {}{1, 1, 1, 1, 0, false, "", false, "", 1508077182}
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git init --bare
[git-module] stdout:
Initialized empty Git repository in /home/vagrant/gitea-repositories/vagrant/test.git/

2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:42 [I] [SQL] UPDATE `repository` SET `owner_id` = ?, `lower_name` = ?, `name` = ?, `description` = ?, `website` = ?, `default_branch` = ?, `num_watches` = ?, `num_stars` = ?, `num_forks` = ?, `num_issues` = ?, `num_closed_issues` = ?, `num_pulls` = ?, `num_closed_pulls` = ?, `num_milestones` = ?, `num_closed_milestones` = ?, `is_private` = ?, `is_bare` = ?, `is_mirror` = ?, `is_fork` = ?, `fork_id` = ?, `size` = ?, `created_unix` = ?, `updated_unix` = ? WHERE `id`=? []interface {}{1, "test", "Test", "", "", "master", 1, 0, 0, 0, 0, 0, 0, 0, 0, false, true, false, false, 0, 0, 1508077182, 1508077182, 1}
2017/10/15 14:19:42 [I] [SQL] COMMIT
[Macaron] 2017-10-15 14:19:42: Completed /repo/create 302 Found in 37.944904ms
[Macaron] 2017-10-15 14:19:42: Started GET /vagrant/Test for 192.168.33.1
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:19:42 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git show-ref --verify refs/heads/master
2017/10/15 14:19:42 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:19:42 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:19:42: Completed /vagrant/Test 200 OK in 19.979207ms
[Macaron] 2017-10-15 14:22:25: Started GET /vagrant/Test/settings for 192.168.33.1
2017/10/15 14:22:25 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:22:25 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:22:25 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
[Macaron] 2017-10-15 14:22:25: Completed /vagrant/Test/settings 200 OK in 13.750277ms
2017/10/15 14:22:25 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:22:25 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:25 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:25 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:22:35: Started POST /vagrant/Test/settings for 192.168.33.1
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:22:35 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
2017/10/15 14:22:35 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
[Macaron] 2017-10-15 14:22:35: Completed /vagrant/Test/settings 302 Found in 10.90726ms
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
2017/10/15 14:22:35 [I] [SQL] BEGIN TRANSACTION
2017/10/15 14:22:35 [I] [SQL] UPDATE `repository` SET `owner_id` = ?, `lower_name` = ?, `name` = ?, `description` = ?, `website` = ?, `default_branch` = ?, `num_watches` = ?, `num_stars` = ?, `num_forks` = ?, `num_issues` = ?, `num_closed_issues` = ?, `num_pulls` = ?, `num_closed_pulls` = ?, `num_milestones` = ?, `num_closed_milestones` = ?, `is_private` = ?, `is_bare` = ?, `is_mirror` = ?, `is_fork` = ?, `fork_id` = ?, `size` = ?, `created_unix` = ?, `updated_unix` = ? WHERE `id`=? []interface {}{1, "test", "test", "", "", "master", 1, 0, 0, 0, 0, 0, 0, 0, 0, false, true, false, false, 0, 0, 1508077182, 1508077355, 1}
2017/10/15 14:22:35 [I] [SQL] COMMIT
[Macaron] 2017-10-15 14:22:35: Started GET /vagrant/test/settings for 192.168.33.1
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:22:35 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[Macaron] 2017-10-15 14:22:35: Completed /vagrant/test/settings 200 OK in 12.918816ms
2017/10/15 14:22:35 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:35 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:22:49: Started GET /vagrant/test/wiki for 192.168.33.1
2017/10/15 14:22:49 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:22:49 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:22:49 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[Macaron] 2017-10-15 14:22:49: Completed /vagrant/test/wiki 200 OK in 29.169945ms
2017/10/15 14:22:49 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:22:49 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:49 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:49 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:22:52: Started GET /vagrant/test for 192.168.33.1
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:22:52 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:22:52 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:22:52 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git show-ref --verify refs/heads/master
2017/10/15 14:22:52 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:22:52 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:52 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:52 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:22:52: Completed /vagrant/test 200 OK in 14.474648ms
[Macaron] 2017-10-15 14:22:59: Started GET /vagrant/test/wiki for 192.168.33.1
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:22:59 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:22:59 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:22:59 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[Macaron] 2017-10-15 14:22:59: Completed /vagrant/test/wiki 200 OK in 10.376592ms
2017/10/15 14:22:59 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:22:59 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:59 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:22:59 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:23:04: Started GET /explore/repos for 192.168.33.1
[Macaron] 2017-10-15 14:23:04: Completed /explore/repos 200 OK in 4.57877ms
2017/10/15 14:23:04 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:23:04 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:23:04 [I] [SQL] SELECT count(*) FROM `repository`
2017/10/15 14:23:04 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` ORDER BY created_unix DESC LIMIT 20
2017/10/15 14:23:04 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE id > 0 AND `id` IN (?) []interface {}{1}
[Macaron] 2017-10-15 14:23:06: Started GET /pulls for 192.168.33.1
[Macaron] 2017-10-15 14:23:06: Completed /pulls 200 OK in 8.562005ms
2017/10/15 14:23:06 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:23:06 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:23:06 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:23:06 [I] [SQL] SELECT `id` FROM `repository` WHERE owner_id = ? []interface {}{1}
2017/10/15 14:23:06 [I] [SQL] SELECT `repository`.`id` FROM `repository` INNER JOIN `team_user` ON repository.owner_id = team_user.org_id AND team_user.uid = ? GROUP BY repository.id []interface {}{1}
2017/10/15 14:23:06 [I] [SQL] SELECT issue.repo_id AS repo_id, COUNT(*) AS count FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? GROUP BY issue.repo_id []interface {}{1, false, true}
2017/10/15 14:23:06 [I] [SQL] SELECT `id`, `repo_id`, `index`, `poster_id`, `name`, `content`, `milestone_id`, `priority`, `assignee_id`, `is_closed`, `is_pull`, `num_comments`, `deadline_unix`, `created_unix`, `updated_unix` FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? ORDER BY `issue`.`created_unix` DESC LIMIT 10 []interface {}{1, false, true}
2017/10/15 14:23:06 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND assignee_id = ? []interface {}{false, true, 1}
2017/10/15 14:23:06 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND poster_id = ? []interface {}{false, true, 1}
2017/10/15 14:23:06 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, true, 1}
2017/10/15 14:23:06 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, true, 1}
2017/10/15 14:23:06 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{true, true, 1}
[Macaron] 2017-10-15 14:23:07: Started GET /issues for 192.168.33.1
[Macaron] 2017-10-15 14:23:07: Completed /issues 200 OK in 6.889208ms
2017/10/15 14:23:07 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:23:07 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:23:07 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:23:07 [I] [SQL] SELECT `id` FROM `repository` WHERE owner_id = ? []interface {}{1}
2017/10/15 14:23:07 [I] [SQL] SELECT `repository`.`id` FROM `repository` INNER JOIN `team_user` ON repository.owner_id = team_user.org_id AND team_user.uid = ? GROUP BY repository.id []interface {}{1}
2017/10/15 14:23:07 [I] [SQL] SELECT issue.repo_id AS repo_id, COUNT(*) AS count FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? GROUP BY issue.repo_id []interface {}{1, false, false}
2017/10/15 14:23:07 [I] [SQL] SELECT `id`, `repo_id`, `index`, `poster_id`, `name`, `content`, `milestone_id`, `priority`, `assignee_id`, `is_closed`, `is_pull`, `num_comments`, `deadline_unix`, `created_unix`, `updated_unix` FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? ORDER BY `issue`.`created_unix` DESC LIMIT 10 []interface {}{1, false, false}
2017/10/15 14:23:07 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND assignee_id = ? []interface {}{false, false, 1}
2017/10/15 14:23:07 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND poster_id = ? []interface {}{false, false, 1}
2017/10/15 14:23:07 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, false, 1}
2017/10/15 14:23:07 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, false, 1}
2017/10/15 14:23:07 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{true, false, 1}
[Macaron] 2017-10-15 14:23:09: Started GET / for 192.168.33.1
[Macaron] 2017-10-15 14:23:09: Completed / 200 OK in 4.61645ms
2017/10/15 14:23:09 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:23:09 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:23:09 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:23:09 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE owner_id = ? AND is_mirror = ? []interface {}{1, true}
2017/10/15 14:23:09 [I] [SQL] SELECT `id`, `user_id`, `op_type`, `act_user_id`, `repo_id`, `comment_id`, `is_deleted`, `ref_name`, `is_private`, `content`, `created_unix` FROM `action` WHERE user_id=? AND is_deleted=? ORDER BY `id` DESC LIMIT 20 []interface {}{1, false}
2017/10/15 14:23:09 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:23:09 [I] [SQL] SELECT count(*) FROM `org_user` WHERE uid=? []interface {}{1}
[Macaron] 2017-10-15 14:23:09: Started GET /api/v1/repos/search?uid=1&q=&limit=15 for 192.168.33.1
[Macaron] 2017-10-15 14:23:09: Completed /api/v1/repos/search?uid=1&q=&limit=15 200 OK in 2.816222ms
2017/10/15 14:23:09 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:23:09 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:23:09 [I] [SQL] SELECT count(*) FROM `repository` WHERE (owner_id=? OR owner_id IN (?) OR id IN (SELECT repo_id FROM `access` WHERE access.user_id = ? AND owner_id != ?)) []interface {}{1, 1, 1, 1}
2017/10/15 14:23:09 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE (owner_id=? OR owner_id IN (?) OR id IN (SELECT repo_id FROM `access` WHERE access.user_id = ? AND owner_id != ?)) ORDER BY name ASC LIMIT 15 []interface {}{1, 1, 1, 1}
2017/10/15 14:23:09 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE id > 0 AND `id` IN (?) []interface {}{1}
[Macaron] 2017-10-15 14:27:25: Started GET /issues for 192.168.33.1
2017/10/15 14:27:25 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
[Macaron] 2017-10-15 14:27:25: Completed /issues 200 OK in 23.488451ms
2017/10/15 14:27:25 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:27:25 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:27:25 [I] [SQL] SELECT `id` FROM `repository` WHERE owner_id = ? []interface {}{1}
2017/10/15 14:27:25 [I] [SQL] SELECT `repository`.`id` FROM `repository` INNER JOIN `team_user` ON repository.owner_id = team_user.org_id AND team_user.uid = ? GROUP BY repository.id []interface {}{1}
2017/10/15 14:27:25 [I] [SQL] SELECT issue.repo_id AS repo_id, COUNT(*) AS count FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? GROUP BY issue.repo_id []interface {}{1, false, false}
2017/10/15 14:27:25 [I] [SQL] SELECT `id`, `repo_id`, `index`, `poster_id`, `name`, `content`, `milestone_id`, `priority`, `assignee_id`, `is_closed`, `is_pull`, `num_comments`, `deadline_unix`, `created_unix`, `updated_unix` FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? ORDER BY `issue`.`created_unix` DESC LIMIT 10 []interface {}{1, false, false}
2017/10/15 14:27:25 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND assignee_id = ? []interface {}{false, false, 1}
2017/10/15 14:27:25 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND poster_id = ? []interface {}{false, false, 1}
2017/10/15 14:27:25 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, false, 1}
2017/10/15 14:27:25 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, false, 1}
2017/10/15 14:27:25 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{true, false, 1}
[Macaron] 2017-10-15 14:27:28: Started GET /pulls for 192.168.33.1
[Macaron] 2017-10-15 14:27:28: Completed /pulls 200 OK in 6.215602ms
2017/10/15 14:27:28 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:27:28 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:27:28 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:27:28 [I] [SQL] SELECT `id` FROM `repository` WHERE owner_id = ? []interface {}{1}
2017/10/15 14:27:28 [I] [SQL] SELECT `repository`.`id` FROM `repository` INNER JOIN `team_user` ON repository.owner_id = team_user.org_id AND team_user.uid = ? GROUP BY repository.id []interface {}{1}
2017/10/15 14:27:28 [I] [SQL] SELECT issue.repo_id AS repo_id, COUNT(*) AS count FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? GROUP BY issue.repo_id []interface {}{1, false, true}
2017/10/15 14:27:28 [I] [SQL] SELECT `id`, `repo_id`, `index`, `poster_id`, `name`, `content`, `milestone_id`, `priority`, `assignee_id`, `is_closed`, `is_pull`, `num_comments`, `deadline_unix`, `created_unix`, `updated_unix` FROM `issue` WHERE `issue`.`repo_id` IN (?) AND issue.is_closed=? AND issue.is_pull=? ORDER BY `issue`.`created_unix` DESC LIMIT 10 []interface {}{1, false, true}
2017/10/15 14:27:28 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND assignee_id = ? []interface {}{false, true, 1}
2017/10/15 14:27:28 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND poster_id = ? []interface {}{false, true, 1}
2017/10/15 14:27:28 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, true, 1}
2017/10/15 14:27:28 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{false, true, 1}
2017/10/15 14:27:28 [I] [SQL] SELECT count(*) FROM `issue` WHERE issue.is_closed = ? AND issue.is_pull = ? AND `repo_id` IN (?) []interface {}{true, true, 1}
[Macaron] 2017-10-15 14:27:32: Started GET / for 192.168.33.1
[Macaron] 2017-10-15 14:27:32: Completed / 200 OK in 4.174439ms
2017/10/15 14:27:32 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:27:32 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:27:32 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:27:32 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE owner_id = ? AND is_mirror = ? []interface {}{1, true}
2017/10/15 14:27:32 [I] [SQL] SELECT `id`, `user_id`, `op_type`, `act_user_id`, `repo_id`, `comment_id`, `is_deleted`, `ref_name`, `is_private`, `content`, `created_unix` FROM `action` WHERE user_id=? AND is_deleted=? ORDER BY `id` DESC LIMIT 20 []interface {}{1, false}
2017/10/15 14:27:32 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:27:32 [I] [SQL] SELECT count(*) FROM `org_user` WHERE uid=? []interface {}{1}
[Macaron] 2017-10-15 14:27:32: Started GET /api/v1/repos/search?uid=1&q=&limit=15 for 192.168.33.1
[Macaron] 2017-10-15 14:27:32: Completed /api/v1/repos/search?uid=1&q=&limit=15 200 OK in 2.940553ms
2017/10/15 14:27:32 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:27:32 [I] [SQL] SELECT * FROM `org_user` LEFT JOIN `user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:27:32 [I] [SQL] SELECT count(*) FROM `repository` WHERE (owner_id=? OR owner_id IN (?) OR id IN (SELECT repo_id FROM `access` WHERE access.user_id = ? AND owner_id != ?)) []interface {}{1, 1, 1, 1}
2017/10/15 14:27:32 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE (owner_id=? OR owner_id IN (?) OR id IN (SELECT repo_id FROM `access` WHERE access.user_id = ? AND owner_id != ?)) ORDER BY name ASC LIMIT 15 []interface {}{1, 1, 1, 1}
2017/10/15 14:27:32 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE id > 0 AND `id` IN (?) []interface {}{1}
[Macaron] 2017-10-15 14:27:41: Started GET /vagrant for 192.168.33.1
[Macaron] 2017-10-15 14:27:41: Completed /vagrant 200 OK in 7.25249ms
2017/10/15 14:27:41 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:27:41 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:27:41 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `lower_name`=? LIMIT 1 []interface {}{"vagrant"}
2017/10/15 14:27:41 [I] [SQL] SELECT `id`, `uid`, `uri`, `show` FROM `user_open_id` WHERE uid=? ORDER BY `id` ASC []interface {}{1}
2017/10/15 14:27:41 [I] [SQL] SELECT * FROM `user` INNER JOIN `org_user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:27:41 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE owner_id = ? ORDER BY updated_unix DESC LIMIT 15 []interface {}{1}
[Macaron] 2017-10-15 14:27:54: Started GET /vagrant?tab=activity for 192.168.33.1
2017/10/15 14:27:54 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
[Macaron] 2017-10-15 14:27:54: Completed /vagrant?tab=activity 200 OK in 6.249015ms
2017/10/15 14:27:54 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:27:54 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `lower_name`=? LIMIT 1 []interface {}{"vagrant"}
2017/10/15 14:27:54 [I] [SQL] SELECT `id`, `uid`, `uri`, `show` FROM `user_open_id` WHERE uid=? ORDER BY `id` ASC []interface {}{1}
2017/10/15 14:27:54 [I] [SQL] SELECT * FROM `user` INNER JOIN `org_user` ON `org_user`.org_id=`user`.id WHERE `org_user`.uid=? ORDER BY `user`.`name` ASC []interface {}{1}
2017/10/15 14:27:54 [I] [SQL] SELECT `id`, `user_id`, `op_type`, `act_user_id`, `repo_id`, `comment_id`, `is_deleted`, `ref_name`, `is_private`, `content`, `created_unix` FROM `action` WHERE user_id=? AND act_user_id=? AND is_deleted=? ORDER BY `id` DESC LIMIT 20 []interface {}{1, 1, false}
2017/10/15 14:27:54 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `id`=? LIMIT 1 []interface {}{1}
[Macaron] 2017-10-15 14:27:57: Started GET /vagrant/test for 192.168.33.1
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:27:57 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:27:57 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:27:57 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git show-ref --verify refs/heads/master
2017/10/15 14:27:57 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:27:57 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:27:57 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:27:57 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:27:57: Completed /vagrant/test 200 OK in 16.450134ms
2017/10/15 14:29:01 [I] [SQL] SELECT `id`, `repo_id`, `interval`, `enable_prune`, `updated_unix`, `next_update_unix` FROM `mirror` WHERE next_update_unix<=? []interface {}{1508077741}
2017/10/15 14:39:01 [I] [SQL] SELECT `id`, `repo_id`, `interval`, `enable_prune`, `updated_unix`, `next_update_unix` FROM `mirror` WHERE next_update_unix<=? []interface {}{1508078341}
[Macaron] 2017-10-15 14:48:05: Started GET /robots.txt for 192.168.33.1
[Macaron] 2017-10-15 14:48:05: Completed /robots.txt 404 Not Found in 2.335557ms
[Macaron] 2017-10-15 14:48:05: Started GET /vagrant/test for 192.168.33.1
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git tag -l --sort=-v:refname
2017/10/15 14:48:05 [I] [SQL] SELECT `id`, `lower_name`, `name`, `full_name`, `email`, `keep_email_private`, `passwd`, `login_type`, `login_source`, `login_name`, `type`, `location`, `website`, `rands`, `salt`, `created_unix`, `updated_unix`, `last_login_unix`, `last_repo_visibility`, `max_repo_creation`, `is_active`, `is_admin`, `allow_git_hook`, `allow_import_local`, `allow_create_organization`, `prohibit_login`, `avatar`, `avatar_email`, `use_custom_avatar`, `num_followers`, `num_following`, `num_stars`, `num_repos`, `description`, `num_teams`, `num_members`, `diff_view_style` FROM `user` WHERE `id`=? LIMIT 1 []interface {}{1}
2017/10/15 14:48:05 [I] [SQL] SELECT count(*) FROM `notification` WHERE user_id = ? AND status = ? []interface {}{1, 0x1}
2017/10/15 14:48:05 [I] [SQL] SELECT `id`, `owner_id`, `lower_name`, `name`, `description`, `website`, `default_branch`, `num_watches`, `num_stars`, `num_forks`, `num_issues`, `num_closed_issues`, `num_pulls`, `num_closed_pulls`, `num_milestones`, `num_closed_milestones`, `is_private`, `is_bare`, `is_mirror`, `is_fork`, `fork_id`, `size`, `created_unix`, `updated_unix` FROM `repository` WHERE `owner_id`=? AND `lower_name`=? LIMIT 1 []interface {}{1, "test"}
[git-module] /home/vagrant/gitea-repositories/vagrant/test.git: git show-ref --verify refs/heads/master
2017/10/15 14:48:05 [I] [SQL] SELECT count(*) FROM `release` WHERE repo_id=? AND is_draft=? []interface {}{1, false}
2017/10/15 14:48:05 [I] [SQL] SELECT `id`, `user_id`, `repo_id` FROM `watch` WHERE `user_id`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:48:05 [I] [SQL] SELECT `id`, `uid`, `repo_id` FROM `star` WHERE `uid`=? AND `repo_id`=? LIMIT 1 []interface {}{1, 1}
2017/10/15 14:48:05 [I] [SQL] SELECT `id`, `repo_id`, `type`, `index`, `config`, `created_unix` FROM `repo_unit` WHERE repo_id = ? []interface {}{1}
[Macaron] 2017-10-15 14:48:05: Completed /vagrant/test 200 OK in 13.417621ms
2017/10/15 14:49:01 [I] [SQL] SELECT `id`, `repo_id`, `interval`, `enable_prune`, `updated_unix`, `next_update_unix` FROM `mirror` WHERE next_update_unix<=? []interface {}{1508078941} 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ cd gitea-repositories 
➜  gitea-repositories l
total 12K
drwxrwxr-x  3 vagrant vagrant 4.0K Oct 15 14:19 .
drwxr-xr-x 15 vagrant vagrant 4.0K Oct 15 15:09 ..
drwxrwxr-x  3 vagrant vagrant 4.0K Oct 15 14:19 vagrant 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  gitea-repositories cd vagrant 
➜  vagrant l
total 12K
drwxrwxr-x 3 vagrant vagrant 4.0K Oct 15 14:19 .
drwxrwxr-x 3 vagrant vagrant 4.0K Oct 15 14:19 ..
drwxrwxr-x 7 vagrant vagrant 4.0K Oct 15 14:19 test.git 
```

Enter fullscreen mode Exit fullscreen mode

### 网站

*   [https://dl.gitea.io/gitea](https://dl.gitea.io/gitea)
*   [https://docs.gitea.io/en-us/install-from-binary](https://docs.gitea.io/en-us/install-from-binary)

### 待办事宜

*   [https://www . therandombits . com/313/gitea-run-as-systemd-service-in-Ubuntu-16-04-lts](https://www.therandombits.com/313/gitea-run-as-systemd-service-in-ubuntu-16-04-lts)
*   安装在~/opt/gitea/

### 图片