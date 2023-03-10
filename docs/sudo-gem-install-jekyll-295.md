# 须藤宝石安装杰基尔

> 原文：<https://dev.to/psnebc/sudo-gem-install-jekyll-295>

## 安装

*   主要信息:我不使用哲基尔

```
patrick@lp:~ > sudo gem install jekyll
[sudo] password for patrick: 
ERROR:  Error installing jekyll:
        jekyll requires Ruby version >= 2.0.0. 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@lp:~ > rvm --default use 2.1.1

RVM is not a function, selecting rubies with 'rvm use ...' will not work.

You need to change your terminal emulator preferences to allow login shell.
Sometimes it is required to use `/bin/bash --login` as the command.
Please visit https://rvm.io/integration/gnome-terminal/ for an example. 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@lp:~ > /bin/bash --login 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@lp:~ > rvm --default use 2.1.1
ruby-2.1.1 is not installed.
To install do: 'rvm install ruby-2.1.1' 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@lp:~ > rvm
= rvm

* https://rvm.io/
* https://github.com/wayneeseguin/rvm/

== DESCRIPTION:                                                                                                                                                                               

RVM is the Ruby enVironment Manager (rvm).                                                                                                                                                    

It manages Ruby application environments and enables switching between them.                                                                                                                  

== Installation                                                                                                                                                                               

  curl -L https://get.rvm.io | bash -s stable --autolibs=enabled [--ruby] [--rails] [--trace]                                                                                                 

stable     :: Install stable RVM, good for servers.                                                                                                                                           
--ruby     :: Additionally install latest ruby version (MRI).                                                                                                                                 
--rails    :: Install gem rails into default gemset (also installs ruby=).                                                                                                                    
--trace    :: Print trace of the installation, gist output when you have problems.                                                                                                            
--autolibs :: Enable or disable autolibs see: https://rvm.io/rvm/autolibs                                                                                                                     

More details here: https://rvm.io/rvm/install/                                                                                                                                                

== Usage                                                                                                                                                                                      

  rvm [Flags] [Options] Action [Implementation[,Implementation[,...]]                                                                                                                         

== Flags

--default         :: with 'rvm use X', sets the default ruby for new shells to X.
--debug           :: Toggle debug mode on for very verbose output.
--disable-binary  :: Install from source instead of using binaries
--trace           :: Toggle trace mode on to see EVERYTHING rvm is doing.
--force           :: Force install, removes old install & source before install.
--summary         :: Used with rubydo to print out a summary of the commands run.
--latest          :: with gemset --dump skips version strings for latest gem.
--gems            :: with uninstall/remove removes gems with the interpreter.
--docs            :: with install, attempt to generate ri after installation.
--reconfigure     :: Force ./configure on install even if Makefile already exists.
--skip-gemsets    :: with install, skip the installation of default gemsets.
--quiet-curl      :: Makes curl silent when fetching data

== Options

-v|--version     :: Emit rvm version loaded for current shell
-l|--level       :: patch level to use with rvm use / install
   --bin         :: path for binaries to be placed (~/.rvm/bin/)
-S               :: Specify a script file to attempt to load and run (rubydo)
-e               :: Execute code from the command line.
--gems           :: Used to set the 'gems_flag', use with 'remove' to remove
                    gems
--archive        :: Used to set the 'archive_flag', use with 'remove' to remove
                    archive
--patch          :: With MRI Rubies you may specify one or more full paths to
                    patches

                    for multiple, specify comma separated:

                      --patch /.../.../a.patch[%prefix],/.../.../.../b.patch

                    'prefix' is an optional argument, which will be bypassed
                    to the '-p' argument of the 'patch' command. It is separated
                    from patch file name with '%' symbol.
-C|--configure   :: custom configure options. If you need to pass several
                    configure options then append them comma separated:

                      -C --...,--...,--...

--nice           :: process niceness (for slow computers, default 0)
--ree-options    :: Options passed directly to ree's './installer' on the
                    command line.
--with-rubies    :: Specifies a string for rvm to attempt to expand for set
                    operations.

== Action

(Note: for most actions, 'rvm help action-name' may provide more information.)

*usage*     :: show this usage information
version     :: show the rvm version installed in rvm_path
use         :: setup current shell to use a specific ruby version
reload      :: reload rvm source itself (useful after changing rvm source)
implode     :: (seppuku) removes the rvm installation completely.

               This means everything in $rvm_path (~/.rvm || /usr/local/rvm).

               This does not touch your profiles. However, this means that you
               must manually clean up your profiles and remove the lines which
               source RVM.
get         :: {head,stable} upgrades rvm to latest head or stable version.

               Check 'rvm help get' for more details.

               (If you experience bugs try this first with head version, then
               ask for help in #rvm on irc.freenode.net and hang around)
reset       :: remove current and stored default & system settings.

               (If you experience odd behavior try this second)
info        :: show the *current* environment information for current ruby
current     :: print the *current* ruby version and the name of any gemset
               being used.
debug       :: show info plus additional information for common issues

install     :: install one or many ruby versions

               See also: https://rvm.io/rubies/installing/
uninstall   :: uninstall one or many ruby versions, leaves their sources
remove      :: uninstall one or many ruby versions and remove their sources
reinstall   :: reinstall ruby and runs gem pristine on all gems,
               make sure to read output, use 'all' for all rubies.

migrate     :: Lets you migrate all gemsets from one ruby to another.
upgrade     :: Lets you upgrade from one version of a ruby to another, including
               migrating your gemsets semi-automatically.

wrapper     :: generates a set of wrapper executables for a given ruby with the
               specified ruby and gemset combination. Used under the hood for
               passenger support and the like.

cleanup     :: Lets you remove stale source folders / archives and other
               miscellaneous data associated with rvm.
repair      :: Lets you repair parts of your environment e.g. wrappers, env
               files and and similar files (e.g. general maintenance).
fix-permissions :: Repairs broken permissions (e.g. by sudo or chef)
osx-ssl-certs :: Helps update certificates for OpenSSL installed by rvm on OSX.
snapshot    :: Lets you backup / restore an rvm installation in a lightweight
               manner.

alias       :: Lets you set shortcut strings for convenience with 'rvm use'.
disk-usage  :: Tells you how much disk space rvm install is using.
tools       :: Provides general information about the ruby environment,
               primarily useful when scripting rvm.
docs        :: Tools to make installing ri and rdoc documentation easier.
rvmrc       :: Tools related to managing rvmrc trust and loading.
patchset    :: Tools related to managing ruby patchsets.

do          :: runs an arbitrary command against specified and/or all rubies
cron        :: Manages setup for using ruby in cron tasks.

gemset      :: gemsets: https://rvm.io/gemsets/

rubygems    :: Switches the installed version of rubygems for the current ruby.

config-get  :: display values for RbConfig::CONFIG variables.
gemdir      :: display the path to the current gem directory (GEM_HOME).

fetch       :: Performs an archive / src fetch only of the selected ruby.
list        :: show currently installed rubies, interactive output.

               https://rvm.io/rubies/list/
autolibs    :: Controls settings for automatically installing dependencies.
pkg         :: Install a dependency package {readline,iconv,zlib,openssl}

               https://rvm.io/packages/
notes       :: Display notes, with operating system specifics.

export      :: Temporarily set an environment variable in the current shell.
unexport    :: Undo changes made to the environment by 'rvm export'.
requirements  :: Installs additional OS specific dependencies/requirements for
                 building various rubies. Usually run by install.
mount       :: Install rubies from external locations.

user        :: Tools for managing RVM mixed mode in multiuser installations.
group       :: Tools for managing groups in multiuser installations.

== Implementation

*ruby*     :: MRI/YARV Ruby (The Gold Standard) {1.8.x, 1.9.x, 2.0.x, 2.1.x, 2.2.x}
jruby      :: JRuby, Ruby interpreter on the Java Virtual Machine.
rbx        :: Rubinius
ree        :: Ruby Enterprise Edition, MRI Ruby with several custom
              patches for performance, stability, and memory.
macruby    :: MacRuby, insanely fast, can make real apps (Mac OS X Only).
maglev     :: GemStone Ruby, awesome persistent ruby object store.
ironruby   :: IronRuby, NOT supported yet. Looking for volunteers to help.
system     :: use the system ruby (eg. pre-rvm state)
default    :: use the default ruby (or the system ruby if a default hasn't been set).

              https://rvm.io/rubies/default/

== Resources:

https://rvm.io/
https://www.pivotaltracker.com/projects/26822

== Contributions:

Any and all contributions offered in any form, past present or future, to the
RVM project are understood to be in complete agreement and acceptance with the
Apache License v2.0.

== LICENSE:

Copyright (c) 2009-2011 Wayne E. Seguin
Copyright (c) 2011-2015 Michal Papis

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

  Action documentation available with 'rvm help action-name':                                                                                                                                 

      alias autolibs cleanup config-get cron current debug disk-usage do docs fetch fix-permissions gemdir gemset gemset/copy get group implode info install list migrate mount notes osx-ssl-certs patchset pkg prepare reinstall remove repair requirements reset rubygems rvmrc rvmrc/to rvmrc/warning snapshot srcdir tools uninstall upgrade use user wrapper                          

  Other topics of documentation available with 'rvm help topic':                                                                                                                              

      alt color upgrade-notes                                                                                                                                                                 

  For additional information please visit RVM's documentation website:                                                                                                                        

      https://rvm.io/                                                                                                                                                                         

  If you still cannot find an answer to your question, find                                                                                                                                   
  'wayneeseguin', 'mpapis', 'richo', 'lemoinem' or 'rys' in #rvm on irc.freenode.net:                                                                                                         

      http://webchat.freenode.net/?channels=rvm 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@lp:~ > rvm info

ruby-2.2.1:

  system:
    uname:       "Linux lp 3.19.0-32-generic #37~14.04.1-Ubuntu SMP Thu Oct 22 09:41:40 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux"
    system:      "mint/17.3/x86_64"
    bash:        "/bin/bash => GNU bash, Version 4.3.11(1)-release (x86_64-pc-linux-gnu)"
    zsh:         " => not installed"

  rvm:
    version:      "rvm 1.26.11 (latest) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]"
    updated:      "12 minutes 27 seconds ago"
    path:         "/home/patrick/.rvm"

  ruby:
    interpreter:  "ruby"
    version:      "2.2.1p85"
    date:         "2015-02-26"
    platform:     "x86_64-linux"
    patchlevel:   "2015-02-26 revision 49769"
    full_version: "ruby 2.2.1p85 (2015-02-26 revision 49769) [x86_64-linux]"

  homes:
    gem:          "/home/patrick/.rvm/gems/ruby-2.2.1"
    ruby:         "/home/patrick/.rvm/rubies/ruby-2.2.1"

  binaries:
    ruby:         "/home/patrick/.rvm/rubies/ruby-2.2.1/bin/ruby"
    irb:          "/home/patrick/.rvm/rubies/ruby-2.2.1/bin/irb"
    gem:          "/home/patrick/.rvm/rubies/ruby-2.2.1/bin/gem"
    rake:         "/home/patrick/.rvm/rubies/ruby-2.2.1/bin/rake"

  environment:
    PATH:         "/home/patrick/.rvm/gems/ruby-2.2.1/bin:/home/patrick/.rvm/gems/ruby-2.2.1@global/bin:/home/patrick/.rvm/rubies/ruby-2.2.1/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/patrick/.rvm/bin:/home/patrick/.rvm/bin:/home/patrick/.rvm/bin:/home/patrick/.rvm/bin"
    GEM_HOME:     "/home/patrick/.rvm/gems/ruby-2.2.1"
    GEM_PATH:     "/home/patrick/.rvm/gems/ruby-2.2.1:/home/patrick/.rvm/gems/ruby-2.2.1@global"
    MY_RUBY_HOME: "/home/patrick/.rvm/rubies/ruby-2.2.1"
    IRBRC:        "/home/patrick/.rvm/rubies/ruby-2.2.1/.irbrc"
    RUBYOPT:      ""
    gemset:       "" 
```

Enter fullscreen mode Exit fullscreen mode

```
 patrick@lp:~ > rvm --default use 2.2.1 
Using /home/patrick/.rvm/gems/ruby-2.2.1 
```

Enter fullscreen mode Exit fullscreen mode

```
patrick@lp:~ > gem install jekyll     
Fetching: colorator-0.1.gem (100%)
Successfully installed colorator-0.1
Fetching: sass-3.4.21.gem (100%)
Successfully installed sass-3.4.21
Fetching: jekyll-sass-converter-1.4.0.gem (100%)
Successfully installed jekyll-sass-converter-1.4.0
Fetching: rb-fsevent-0.9.7.gem (100%)
Successfully installed rb-fsevent-0.9.7
Fetching: ffi-1.9.10.gem (100%)
Building native extensions.  This could take a while...
Successfully installed ffi-1.9.10
Fetching: rb-inotify-0.9.7.gem (100%)
Successfully installed rb-inotify-0.9.7
Fetching: listen-3.0.6.gem (100%)
Successfully installed listen-3.0.6
Fetching: jekyll-watch-1.3.1.gem (100%)
Successfully installed jekyll-watch-1.3.1
Fetching: kramdown-1.10.0.gem (100%)
Successfully installed kramdown-1.10.0
Fetching: liquid-3.0.6.gem (100%)
Successfully installed liquid-3.0.6
Fetching: mercenary-0.3.5.gem (100%)
Successfully installed mercenary-0.3.5
Fetching: rouge-1.10.1.gem (100%)
Successfully installed rouge-1.10.1
Fetching: safe_yaml-1.0.4.gem (100%)
Successfully installed safe_yaml-1.0.4
Fetching: jekyll-3.1.2.gem (100%)
Successfully installed jekyll-3.1.2
Parsing documentation for colorator-0.1
Installing ri documentation for colorator-0.1
Parsing documentation for sass-3.4.21
Installing ri documentation for sass-3.4.21
Parsing documentation for jekyll-sass-converter-1.4.0
Installing ri documentation for jekyll-sass-converter-1.4.0
Parsing documentation for rb-fsevent-0.9.7
Installing ri documentation for rb-fsevent-0.9.7
Parsing documentation for ffi-1.9.10
Installing ri documentation for ffi-1.9.10
Parsing documentation for rb-inotify-0.9.7
Installing ri documentation for rb-inotify-0.9.7
Parsing documentation for listen-3.0.6
Installing ri documentation for listen-3.0.6
Parsing documentation for jekyll-watch-1.3.1
Installing ri documentation for jekyll-watch-1.3.1
Parsing documentation for kramdown-1.10.0
Installing ri documentation for kramdown-1.10.0
Parsing documentation for liquid-3.0.6
Installing ri documentation for liquid-3.0.6
Parsing documentation for mercenary-0.3.5
Installing ri documentation for mercenary-0.3.5
Parsing documentation for rouge-1.10.1
Installing ri documentation for rouge-1.10.1
Parsing documentation for safe_yaml-1.0.4
Installing ri documentation for safe_yaml-1.0.4
Parsing documentation for jekyll-3.1.2
Installing ri documentation for jekyll-3.1.2
Done installing documentation for colorator, sass, jekyll-sass-converter, rb-fsevent, ffi, rb-inotify, listen, jekyll-watch, kramdown, liquid, mercenary, rouge, safe_yaml, jekyll after 18 seconds
14 gems installed 
```

Enter fullscreen mode Exit fullscreen mode