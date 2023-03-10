# 安装 Drupal 控制台

> 原文：<https://dev.to/psnebc/install-drupal-console-536g>

```
Welcome to the Emacs shell

~ $ curl https://drupalconsole.com/installer -L -o drupal.phar
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  698k  100  698k    0     0   299k      0  0:00:02  0:00:02 --:--:--  299k
~ $ php drupal.phar
Drupal Console Launcher 1.5.0

Usage:
  command [options] [arguments]

Options:
  -h, --help             Display this help message
  -q, --quiet            Do not output any message
  -V, --version          Display this application version
      --ansi             Force ANSI output
      --no-ansi          Disable ANSI output
  -n, --no-interaction   Do not ask any interactive question
  -e, --env[=ENV]        The Environment name [default: "prod"]
      --root[=ROOT]      Define the Drupal root to be used in command execution
      --debug            Switches on debug mode
      --learning         Generate a verbose code output
  -c, --generate-chain   Shows command options and arguments as yaml output to be used in chain command
  -i, --generate-inline  Shows command options and arguments as inline command
  -d, --generate-doc     Shows command options and arguments as markdown
  -t, --target[=TARGET]  Site name you want to interact with (for local or remote sites)
  -l, --uri=URI          URI of the Drupal site to use (for multi-site environments or when running on an alternate port)
  -y, --yes              Skip confirmation and proceed
  -v|vv|vvv, --verbose   Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  about                     Displays basic information about Drupal Console project
  chain                     Chain command execution
  check                     System requirement checker
  exec Execute an external command.
  help Displays help for a command init                      Copy configuration files.
  list                      Lists all available commands
 debug
  debug:chain (dch)         List available chain files.
  debug:settings (dse)      List user Drupal Console settings.
  debug:site (dsi)          List all known local and remote sites.
 develop
  develop:contribute        Download Drupal + Drupal Console to contribute.
 quick
  quick:start               Download, install and serve a new Drupal project
 settings
  settings:set              Change a specific setting value in DrupalConsole config file
 site
  site:new                  Download a new Drupal project
~ $ mv drupal.phar /usr/local/bin/drupal
Renaming: Permission denied, /home/psnc/drupal.phar, /usr/local/bin/drupal
~ $ sudo mv drupal.phar /usr/local/bin/drupal
[sudo] password for psnc: 
~ $ sudo chmod +x /usr/local/bin/drupal
[sudo] password for psnc: 
~ $ drupal init

 Select destination to copy configuration:
  [0] /home/psnc/.console/
 > 0
0

 Select language [en]:
 > en
en

 Enter temporary file path [/tmp]:
 > /tmp

 Copy chain files examples (yes/no) [no]:
 > no

 Copy site alias files examples (yes/no) [no]:
 > no

 Shows information for learning purposes? (yes/no) [no]:
 > no

 Show inline representation of the executed command? (yes/no) [no]:
 > no

 Show chain representation of the executed command? (yes/no) [no]:
 > no

 Generate autocomplete files (yes/no) [no]:
 > no

Copied files

 1 - /home/psnc/.console/defaults.yml

Bash: Bash support depends on the http://bash-completion.alioth.debian.org/
project which can be installed with your package manager of choice. Then add
this line to your shell configuration file.
source "$HOME/.console/console.rc" 2>/dev/null

Bash or Zsh: Add this line to your shell configuration file:
source "$HOME/.console/console.rc" 2>/dev/null

Fish: Create a symbolic link
ln -s ~/.console/drupal.fish ~/.config/fish/completions/drupal.fish

Generated or updated files
 Generation path: phar:///usr/local/bin/drupal/bin/../
 1 - /home/psnc/.console/config.yml

 Generated lines of code: 25                     

~ $ source "$HOME/.console/console.rc" 2>/dev/null

~ $ sudo drupal self-update
[sudo] password for psnc: 
 Checking for updates from version: "1.5.0"
 The latest version "1.5.0", was already installed on your system.
~ $ 
```

Enter fullscreen mode Exit fullscreen mode