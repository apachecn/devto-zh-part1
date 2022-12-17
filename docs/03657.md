# sudo apt-get 安装 pandoc

> 原文：<https://dev.to/psnebc/sudo-apt-get-install-pandoc-34m>

```
# Update System & Install Pandoc
➜  ~ sudo apt-get update && sudo apt-get install pandoc
[sudo] password for webdev: 
Hit:1 http://archive.ubuntu.com/ubuntu xenial InRelease
Hit:2 http://ppa.launchpad.net/git-core/ppa/ubuntu xenial InRelease                                                                       
Get:3 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]                                                                                           
Ign:4 http://mint-mirror.gwendallebihan.net/packages sonya InRelease                                                                                                      
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]                                                                                                  
Hit:6 http://ppa.launchpad.net/jonathonf/vim/ubuntu xenial InRelease                                                                                           
Get:7 http://mint-mirror.gwendallebihan.net/packages sonya Release [24,1 kB]                                                                                   
Hit:8 http://ppa.launchpad.net/kelleyk/emacs/ubuntu xenial InRelease                                                                     
Get:9 http://mint-mirror.gwendallebihan.net/packages sonya Release.gpg [819 B]                                                                            
Hit:10 http://ppa.launchpad.net/kubuntu-ppa/backports/ubuntu xenial InRelease                                                                             
Get:11 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]                                              
Get:12 http://archive.canonical.com/ubuntu xenial InRelease [11,5 kB]                                                               
Hit:13 http://ppa.launchpad.net/webupd8team/java/ubuntu xenial InRelease                     
Get:14 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [374 kB] 
Get:15 http://mint-mirror.gwendallebihan.net/packages sonya/main amd64 Packages [18,1 kB]                 
Get:16 http://mint-mirror.gwendallebihan.net/packages sonya/main i386 Packages [17,5 kB]                                
Get:17 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [643 kB]            
Get:18 http://security.ubuntu.com/ubuntu xenial-security/main i386 Packages [348 kB]                                
Get:19 http://security.ubuntu.com/ubuntu xenial-security/main Translation-en [165 kB]                                            
Get:20 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [176 kB]                                   
Get:21 http://security.ubuntu.com/ubuntu xenial-security/universe i386 Packages [152 kB]                                    
Get:22 http://security.ubuntu.com/ubuntu xenial-security/universe Translation-en [93,5 kB]                                  
Get:23 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [3.208 B]                                  
Get:24 http://security.ubuntu.com/ubuntu xenial-security/multiverse i386 Packages [3.388 B]                    
Get:25 http://archive.canonical.com/ubuntu xenial/partner amd64 Packages [3.120 B]                       
Get:26 http://archive.canonical.com/ubuntu xenial/partner i386 Packages [3.120 B]   
Get:27 http://archive.ubuntu.com/ubuntu xenial-updates/main i386 Packages [611 kB]  
Get:28 http://archive.ubuntu.com/ubuntu xenial-updates/main Translation-en [269 kB]
Get:29 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [540 kB]
Get:30 http://archive.ubuntu.com/ubuntu xenial-updates/universe i386 Packages [516 kB]
Get:31 http://archive.ubuntu.com/ubuntu xenial-updates/universe Translation-en [220 kB]
Get:32 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [15,8 kB]
Get:33 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse i386 Packages [15,0 kB]
Fetched 4.531 kB in 4s (1.047 kB/s)                          
Reading package lists... Done
Reading package lists... Done
Building dependency tree        
Reading state information... Done
The following additional packages will be installed:
  liblua5.1-0 libluajit-5.1-2 libluajit-5.1-common pandoc-data
Suggested packages:
  texlive-latex-recommended texlive-xetex texlive-luatex pandoc-citeproc etoolbox wkhtmltopdf
The following NEW packages will be installed:
  liblua5.1-0 libluajit-5.1-2 libluajit-5.1-common pandoc pandoc-data
0 upgraded, 5 newly installed, 0 to remove and 2 not upgraded.
Need to get 7.028 kB of archives.
After this operation, 55,2 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 liblua5.1-0 amd64 5.1.5-8ubuntu1 [102 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libluajit-5.1-common all 2.0.4+dfsg-1 [35,3 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial/universe amd64 libluajit-5.1-2 amd64 2.0.4+dfsg-1 [205 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/universe amd64 pandoc-data all 1.16.0.2~dfsg-1 [84,0 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/universe amd64 pandoc amd64 1.16.0.2~dfsg-1 [6.601 kB]
Fetched 7.028 kB in 7s (908 kB/s)                                                                                                                                         
Selecting previously unselected package liblua5.1-0:amd64.
(Reading database ... 239350 files and directories currently installed.)
Preparing to unpack .../liblua5.1-0_5.1.5-8ubuntu1_amd64.deb ...
Unpacking liblua5.1-0:amd64 (5.1.5-8ubuntu1) ...
Selecting previously unselected package libluajit-5.1-common.
Preparing to unpack .../libluajit-5.1-common_2.0.4+dfsg-1_all.deb ...
Unpacking libluajit-5.1-common (2.0.4+dfsg-1) ...
Selecting previously unselected package libluajit-5.1-2:amd64.
Preparing to unpack .../libluajit-5.1-2_2.0.4+dfsg-1_amd64.deb ...
Unpacking libluajit-5.1-2:amd64 (2.0.4+dfsg-1) ...
Selecting previously unselected package pandoc-data.
Preparing to unpack .../pandoc-data_1.16.0.2~dfsg-1_all.deb ...
Unpacking pandoc-data (1.16.0.2~dfsg-1) ...
Selecting previously unselected package pandoc.
Preparing to unpack .../pandoc_1.16.0.2~dfsg-1_amd64.deb ...
Unpacking pandoc (1.16.0.2~dfsg-1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up liblua5.1-0:amd64 (5.1.5-8ubuntu1) ...
Setting up libluajit-5.1-common (2.0.4+dfsg-1) ...
Setting up libluajit-5.1-2:amd64 (2.0.4+dfsg-1) ...
Setting up pandoc-data (1.16.0.2~dfsg-1) ...
Setting up pandoc (1.16.0.2~dfsg-1) ...
Processing triggers for libc-bin (2.23-0ubuntu9) ... 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  ~ dpkg -L pandoc
/.
/usr
/usr/bin
/usr/bin/pandoc
/usr/share
/usr/share/lintian
/usr/share/lintian/overrides
/usr/share/lintian/overrides/pandoc
/usr/share/doc
/usr/share/doc/pandoc
/usr/share/doc/pandoc/copyright
/usr/share/doc/pandoc/README.gz
/usr/share/doc/pandoc/changelog.Debian.gz
/usr/share/doc/pandoc/README.Debian
/usr/share/doc/pandoc/NEWS.Debian.gz
/usr/share/doc/pandoc/TODO.Debian
/usr/share/doc/pandoc/BUGS
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/pandoc.1.gz 
```

Enter fullscreen mode Exit fullscreen mode

```
# Pandoc Version
➜  ~ pandoc -v
pandoc 1.16.0.2
Compiled with texmath 0.8.4.1, highlighting-kate 0.6.1.
Syntax highlighting is supported for the following languages:
    abc, actionscript, ada, agda, apache, asn1, asp, awk, bash, bibtex, boo, c,
    changelog, clojure, cmake, coffee, coldfusion, commonlisp, cpp, cs, css,
    curry, d, diff, djangotemplate, dockerfile, dot, doxygen, doxygenlua, dtd,
    eiffel, email, erlang, fasm, fortran, fsharp, gcc, glsl, gnuassembler, go,
    haskell, haxe, html, idris, ini, isocpp, java, javadoc, javascript, json,
    jsp, julia, kotlin, latex, lex, lilypond, literatecurry, literatehaskell,
    llvm, lua, m4, makefile, mandoc, markdown, mathematica, matlab, maxima,
    mediawiki, metafont, mips, modelines, modula2, modula3, monobasic, nasm,
    noweb, objectivec, objectivecpp, ocaml, octave, opencl, pascal, perl, php,
    pike, postscript, prolog, pure, python, r, relaxng, relaxngcompact, rest,
    rhtml, roff, ruby, rust, scala, scheme, sci, sed, sgml, sql, sqlmysql,
    sqlpostgresql, tcl, tcsh, texinfo, verilog, vhdl, xml, xorg, xslt, xul,
    yacc, yaml, zsh
Default user data directory: /home/webdev/.pandoc
Copyright (C) 2006-2015 John MacFarlane
Web:  http://pandoc.org
This is free software; see the source for copying conditions.
There is no warranty, not even for merchantability or fitness
for a particular purpose. 
```

Enter fullscreen mode Exit fullscreen mode

```
# To remove the pandoc following command is used:

sudo apt-get remove  pandoc 
```

Enter fullscreen mode Exit fullscreen mode

```
# Following command is used to remove the pandoc
# package along with its dependencies:

sudo apt-get remove --auto-remove pandoc 
```

Enter fullscreen mode Exit fullscreen mode

```
# Following command should be used with care as it deletes all the 
# configuration files and data:

sudo apt-get purge pandoc

# or you can use following command also:

sudo apt-get purge --auto-remove pandoc 
```

Enter fullscreen mode Exit fullscreen mode

# 链接

*   pandoc[http://pandoc.org](http://pandoc.org)
*   入门[http://pandoc.org/getting-started.html](http://pandoc.org/getting-started.html)
*   试试 Pandoc Online[http://pandoc.org/try](http://pandoc.org/try)