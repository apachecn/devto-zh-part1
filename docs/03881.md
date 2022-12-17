# 将 phpcs 作为 Git 钩子运行在 Vagrant 上

> 原文：<https://dev.to/cr0wst/running-phpcs-on-vagrant-as-a-git-hook-24gl>

使用像[vagger](https://www.vagrantup.com/)这样的虚拟机的一个主要优点是，您能够将您的开发环境与您的开发工作站隔离开来。

然而，当您想要运行脚本时，有时没有安装 PHP 的副本会导致一些复杂性。对于大多数事情，您可以使用`vagrant ssh`或 PHPStorm 的一些内置实用程序，但是如果您想自动化这些事情呢？

我将假设您已经有了一个 vagger setup 并安装了 phpcs。我还假设你用的是 Mac 或者 Linux。我试图在我的 Windows 系统上运行这个，但是 git bash 不配合。

## PHP_CodeSniffer

PHP_CodeSniffer 是一组 PHP 脚本，可以用来检测编码标准的违反。它还附带了一个脚本`phpcbf`，可以自动纠正这些违规行为。

我的通过`composer require "squizlabs/php_codesniffer=*" --dev`安装

忘记运行`phpcs`真的很容易，所以人们可能会发现它作为 git `pre-commit`钩子很有用。

## Git 挂钩

Git 在`.git/hooks/`中有各种不同的钩子，它们会在某些事件发生时自动触发。然而，如果我在我的本地机器上使用 git，但是在 vagger 上运行 PHP，我该如何利用这些钩子呢？这就是我要回答的问题。

### `pre-commit`劫

当您在提交成功之前键入`git commit`时，就会触发`pre-commit`挂钩。它使用脚本的返回代码来确定是否允许提交。

有些人会建议使用不同的挂钩，但我喜欢`pre-commit`，原因如下:

*   我经常提交，最好能对每一个都进行代码标准检查。
*   通过阻止提交，我可以在特定文件上运行`phpcbf`,并将这些更改作为提交的一部分。
*   我通常不喜欢恢复提交，或者添加不必要的提交。如果我将它用作一个`post-commit`或`pre-push`钩子，它将需要添加到历史中。

## 创建钩子

您会注意到在您的`.git/hooks/`目录中有一个`pre-commit.sample`文件。你可以重命名这个文件，或者创建一个新文件:

```
cd .git/hooks
touch pre-commit
chmod +x pre-commit 
```

在这个`pre-commit`文件中，我有以下脚本:

```
#!/bin/bash
# Runs PHPCS on specified vagrant box

# Variables ###################################
###############################################
# Vagrant Directory
# Path to your Vagrant file
vagrantDirectory='/Users/crow/Vagrant/homestead'

# PHP Application Directory
# The full path to your PHP application
applicationDirectory='/home/vagrant/code'

# PHPCS Location
# The full path to PHPCS on your Vagrant box
# This is not necessary if phpcs is installed globally
# Can also reference it within your application directory (recommended)
phpcsPath="${applicationDirectory}/vendor/squizlabs/php_codesniffer/bin/phpcs"
###############################################

# First grab the staged files
stagedFiles=()
while read file
do stagedFiles+=("${applicationDirectory}/${file}")
done < <(git diff --cached --name-only --diff-filter=AM)

# Second construct the phpcs command.
command=$(echo "${phpcsPath} -n --report=full ${stagedFiles[@]}")

# Third get SSH config from Vagrant
# This is done because sometimes vagrant ssh -c can be wonky
currentDirectory=$(pwd)
cd $vagrantDirectory
sshConfigOptions=$(vagrant ssh-config | awk 'NR>1 {print " -o "$1"="$2'})
cd $currentDirectory

# Fourth run the SSH command and capture the results
results=$(ssh $sshConfigOptions localhost $command)

# Fifth setup blocking for git commit
# This is not the ideal way to do this, but we're going to use
# regular expressions to determine success.

# You may need to modify this line depending on your phpcs settings.
foundErrors=$(grep -o "FOUND [0-9]* ERRORS" <<< ${results})
returnCode=0 # RC 0 will let the commit pass

if [[$foundErrors != '']]
then echo "${results}"
    echo "Fix or commit --no-verify"
    returnCode+=1
fi exit $returnCode 
```

我首先声明我不是 bash 专家。这个脚本缺少一些东西，最明显的是如果你的流浪者盒子没有打开，它不会显示任何好的信息。它也不会验证你给它的信息，但它对我有用。

### 可变截面

变量部分是你需要定义一些东西的地方。

*   `vagrantDirectory`是你的`Vagrantfile`所在的目录。该脚本将进入该目录并获取 SSH 连接的`vagrant ssh-config`选项。
*   这是你的文件在流浪者盒子上的位置。当在非交互式 shell 中运行命令时，将位置完全限定为各种事物通常是一个好主意。
*   `phpcsPath`用于告诉脚本在哪里可以找到 phpcs。同样，我们在这里运行的是非交互式的，所以您设置的任何别名都不起作用。

### 工作原理

脚本本身非常简单。它从获取暂存文件列表开始。这通过以下方式完成:

#### 第一步:抓取暂存文件

```
stagedFiles=()
while read file
do stagedFiles+=("${applicationDirectory}/${file}")
done < <(git diff --cached --name-only --diff-filter=AM) 
```

它使用`git diff --cached --name-only --diff-filter=AM`命令获取当前暂存的文件列表。`diff-filter`告诉它只查看已经被**添加了**或者**修改了**的文件。由于显而易见的原因，我们无法验证已被删除的文件。

该循环从命令和文件的构造和数组中获取这些信息。可能有一种方法可以将 git 的输出压缩到一行，但是我喜欢这种方法，因为如果需要的话，我可以使用文件列表来运行其他东西。

#### 第二步:构造 phpcs 命令

我们使用的命令希望您在末尾给它一个文件列表，用空格分隔。

```
command=$(echo "${phpcsPath} -n --report=full ${stagedFiles[@]}") 
```

因为我们将这些文件存储在一个数组中，所以使用`${stagedFiles[@]}`很容易得到一个空格分隔的列表。

`-n`标志告诉命令只给我们错误，而`--report=full`会给我们一个所有发现的错误的完整报告。

#### 第三步:从流浪者那里获取 SSH 配置

vagger 有一个命令`vagrant ssh`，它接受一个`-c`标志，用于通过 SSH 发送命令。当您想要发送多个命令时，这个命令可能会变得有点奇怪，我不觉得它很灵活。相反，我选择从流浪者那里获取`ssh-config`,并用它来 SSH 并发送命令。

```
currentDirectory=$(pwd)
cd $vagrantDirectory
sshConfigOptions=$(vagrant ssh-config | awk 'NR>1 {print " -o "$1"="$2'})
cd $currentDirectory 
```

我们保存当前的工作目录，切换到流浪者目录，然后构建一个`$sshConfigOptions`字符串，我们将把它传递给 SSH 命令。`awk`部分获取每一行并将其转换为`-o key=value`。

#### 第四步:运行 SSH 命令

```
results=$(ssh $sshConfigOptions localhost $command) 
```

现在我们运行 SSH 命令并将其存储在`$results`变量中。这使我们能够阻止任何可能出现的 MOTD 或横幅。

#### 第五步:如果有必要，阻止 Git 提交

这有点不方便，但是我们使用正则表达式来检查并查看是否报告了任何错误。因为 phpcs 被指示忽略警告，所以如果没有错误，它不会显示任何消息。

```
foundErrors=$(grep -o "FOUND [0-9]* ERRORS" <<< ${results})
returnCode=0 # RC 0 will let the commit pass

if [[$foundErrors != '']]
then echo "${results}"
    echo "Fix or commit --no-verify"
    returnCode+=1
fi exit $returnCode 
```

如果有错误，我们增加返回代码，这将导致提交失败，并给用户一些指示以及打印出结果。

## 结果

以下是我尝试提交一个有严重错误的文件时的结果:

```
git commit

FILE: /home/vagrant/code/app/Sample.php
----------------------------------------------------------------------
FOUND 3 ERRORS AFFECTING 3 LINES
----------------------------------------------------------------------
 2 | ERROR | [] Missing file doc comment
 3 | ERROR | [] Missing class doc comment
 5 | ERROR | [x] Line indented incorrectly; expected 4 spaces, found
   | | 0
----------------------------------------------------------------------
PHPCBF CAN FIX THE 1 MARKED SNIFF VIOLATIONS AUTOMATICALLY
----------------------------------------------------------------------

Time: 97ms; Memory: 6Mb
Fix or commit --no-verify 
```

## 其他一些需要考虑的事情

这里还有几件值得一提的事情。

### 隐藏文件

您可能有不想在 phpcs 中发现的更改。在运行提交之前隐藏更改，然后在之后弹出它们可能是个好主意。

### 那个讨厌的 MOTD

我使用 Homestead 作为我的测试环境，它没有 MOTD。然而，我的另一个盒子里有令人讨厌的 Ubuntu。

找出第一个错误发生的时间，并删除该错误之前的所有文本可能是一个好主意。这防止了许多无用信息的出现。

在这个钩子的另一个版本中，我使用`--report=summary`和下面的一组命令来删除 motd。我还展示了甘道夫照片，因为提交不会通过。

```
# You've angered the wizard - http://textart.io/art/tag/gandalf/1
# Art to show in case you messed up.
read -r -d '' gandalf << "EOM"
------------------------------------------
                           ,---.
                          / |
                         / |
You shall not pass! / |
                       / |
                  ___,' |
                < -' :
                 `-.__..--'``-,_\_ |o/ <o>` :,.)_`>
                    :/ ` ||/)
                    (_.).__,-` |\ /( `.`` `| : \'`-.) ` ; ;
                    | ` /-<
                    | ` / `.
    ,-_-.. ____/| ` :__..-'\ /,'-.__\\ ``-./ :` ; \ `\ `\ `\\  \ : ( ` / , `. \  \`  \ \\ | | ` : : .\ \  \ `\_ )) : ; | | ): :
     (`-.-'\ || |\ \ ` ; ; | | \-_ `;;._ ( ` / /_ | |
       `-.-.// ,'`-._\__/_,' ; | \:: : / ` , / |
           || | ( ,' / / |
           || ,' / |
------------------------------------------ EOM foundMessage=$(grep -o "[0-9]* ERRORS AND [0-9]* WARNINGS WERE FOUND" <<< $results)
returnCode=0
if [[$foundMessage != '']]; then echo "${gandalf}"
    phpCsReport=$(sed '/PHP\ CODE\ SNIFFER\ REPORT\ SUMMARY/,$!d' <<< "${results}")
    echo "${phpCsReport}"
    returnCode+=1
fi 
```

`sed '/PHP\ CODE\ SNIFFER\ REPORT\ SUMMARY/,$!d' <<< "${results}`命令基本上删除了匹配之前的所有文本。

### 其他要检查的东西

我的版本只检查 PHP。嗯，从技术上讲，它把所有东西都发送到 phpcs，但是只有 PHP 会被检查。然而，这也可以用来检查 JavaScript、Java 或您可能正在使用的任何其他语言。

### 多个命令

如果你要发送多个命令到 travel box，我发现把它们存储在一个临时文件中，然后把这个文件发送到 SSH 命令会容易得多。大概是这样:

```
"command one" >> commands.temp.sh
"command two" >> commands.temp.sh
"commands three" >> commands.temp.sh

results=$(cat commands.temp.sh | ssh $sshConfigOptions localhost}
rm commands.temp.sh 
```

## 结论

使用流浪者和使用 git 挂钩可以是一个超级强大的工作流。硬着头皮在本地安装 PHP 可能更容易，但这有什么意思呢？

如果你想到其他有趣的把戏，请告诉我！