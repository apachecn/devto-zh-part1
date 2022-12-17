# 安装 openssh-server

> 原文：<https://dev.to/psnebc/install-openssh-server-23po>

注意:编辑 sshd_config

```
sudo vim /etc/ssh/sshd_config 
```

Enter fullscreen mode Exit fullscreen mode

安装:openssh-server

```
psnc@psnc  sudo apt-get install openssh-server
Paketlisten werden gelesen... Fertig
Abhängigkeitsbaum wird aufgebaut.       
Statusinformationen werden eingelesen.... Fertig
Die folgenden Pakete wurden automatisch installiert und werden nicht mehr benötigt:
  linux-image-4.4.0-104-generic linux-image-4.4.0-109-generic linux-image-extra-4.4.0-104-generic linux-image-extra-4.4.0-109-generic
Verwenden Sie »sudo apt autoremove«, um sie zu entfernen.
The following additional packages will be installed:
  openssh-sftp-server
Vorgeschlagene Pakete:
  rssh molly-guard monkeysphere
Empfohlene Pakete:
  ncurses-term ssh-import-id
The following NEW packages will be installed
  openssh-server openssh-sftp-server
0 to upgrade, 2 to newly install, 0 to remove and 0 not to upgrade.
Es müssen 374 kB an Archiven heruntergeladen werden.
Nach dieser Operation werden 1.013 kB Plattenplatz zusätzlich benutzt.
Möchten Sie fortfahren? [J/n] y
Holen:1 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 openssh-sftp-server amd64 1:7.2p2-4ubuntu2.4 [38,7 kB]
Holen:2 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 openssh-server amd64 1:7.2p2-4ubuntu2.4 [335 kB]
Es wurden 374 kB in 0 s geholt (1.163 kB/s).
Vorkonfiguration der Pakete ...
Vormals nicht ausgewähltes Paket openssh-sftp-server wird gewählt.
(Lese Datenbank ... 573932 Dateien und Verzeichnisse sind derzeit installiert.)
Vorbereitung zum Entpacken von .../openssh-sftp-server_1%3a7.2p2-4ubuntu2.4_amd64.deb ...
Entpacken von openssh-sftp-server (1:7.2p2-4ubuntu2.4) ...
Vormals nicht ausgewähltes Paket openssh-server wird gewählt.
Vorbereitung zum Entpacken von .../openssh-server_1%3a7.2p2-4ubuntu2.4_amd64.deb ...
Entpacken von openssh-server (1:7.2p2-4ubuntu2.4) ...
Trigger für man-db (2.7.5-1) werden verarbeitet ...
Trigger für ufw (0.35-0ubuntu2) werden verarbeitet ...
Trigger für systemd (229-4ubuntu21) werden verarbeitet ...
Trigger für ureadahead (0.100.0-19) werden verarbeitet ...
ureadahead will be reprofiled on next reboot
openssh-sftp-server (1:7.2p2-4ubuntu2.4) wird eingerichtet ...
openssh-server (1:7.2p2-4ubuntu2.4) wird eingerichtet ...
Creating SSH2 RSA key; this may take some time ...
2048 SHA256:8+QjbLm5qR8LJSCTc8r++wVjeQI/bvWGSRL/xheYRmM root@psnc (RSA)
Creating SSH2 DSA key; this may take some time ...
1024 SHA256:tiKYu5Y9MAb/jqNohH1QosX9flnOfnMKf+D+/P46A2g root@psnc (DSA)
Creating SSH2 ECDSA key; this may take some time ...
256 SHA256:sCsK7LQZmcbs+sgYJ0XrNyIpmRkC9KE0uIxHYX7aJV4 root@psnc (ECDSA)
Creating SSH2 ED25519 key; this may take some time ...
256 SHA256:JjgOPN6QwEFP2JjLBa0O8XzS2bSHdHSiORRvJJ67/DM root@psnc (ED25519)
Trigger für systemd (229-4ubuntu21) werden verarbeitet ...
Trigger für ureadahead (0.100.0-19) werden verarbeitet ...
Trigger für ufw (0.35-0ubuntu2) werden verarbeitet ... 
```

Enter fullscreen mode Exit fullscreen mode