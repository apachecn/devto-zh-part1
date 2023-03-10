# 自动向 git 提交添加票号

> 原文：<https://dev.to/tomoyukikashiro/add-ticket-number-to-git-commit-automatically-4fgh>

这篇文章最初发表于[添加票号到 git 自动提交](https://blog.tomoyukikashiro.me/post/add-ticket-number-to-git-commit-automatically)

像 Github、pivotal tracker 这样的大多数票追踪器都有将你的承诺与票(故事)联系起来的功能。但是每次当你提交复制和粘贴的机票号码是恼人的…

所以我使用 git hook 创建了一个脚本来自动将票号添加到您的提交中。

## 脚本

```
$ mkdir -p ~/.git-templates/hooks
$ touch ~/.git-templates/hooks/prepare-commit-msg
$ chmod u+x ~/.git-templates/hooks/prepare-commit-msg 
```

然后，您可以将该脚本复制并粘贴到`prepare-commit-msg`

```
#!/bin/sh

LF=$'\\\x0A'
BRANCH_NAME=$(git symbolic-ref --short HEAD)

if [[$BRANCH_NAME =~ .*\/[0-9]* ]]; then
  PREFIX=$(echo $BRANCH_NAME | sed -e 's/\(.*\)\/\([0-9]*\)/\1/')
  NUMBER=$(echo $BRANCH_NAME | sed -e 's/\(.*\)\/\([0-9]*\)/\2/')
  if [$PREFIX = 'story']; then
      MESSAGE="[#$NUMBER]"
  else
      MESSAGE="GH-$NUMBER"
  fi
  if [$NUMBER]; then
    sed -i.back "1s/^/$LF$LF$MESSAGE$LF/" "$1"
  fi
fi 
```

## 配置

要启用此脚本，请运行以下命令。

```
$ git config core.hooksPath ~/.git-templates/hooks/ 
```

## 用法

该脚本希望您的分支使用以下格式命名

### 枢轴跟踪器

```
story/1111 
```

### Github

```
issue/1111
fix/1111
feature/1111 
```

当你每次提交脚本的时候用这个格式把那个数字加到你的提交里！！

### 枢轴跟踪器

```
[#1111] 
```

### Github

```
GH-1111 
```