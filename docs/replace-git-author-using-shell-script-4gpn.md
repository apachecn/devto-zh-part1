# 使用 shell 脚本替换 git 作者

> 原文：<https://dev.to/leonardofaria/replace-git-author-using-shell-script-4gpn>

这是救了我好几次的老把戏了。有时人们会忘记设置他们的姓名和电子邮件信息。以下脚本对修复错误很有用:

```
#!/bin/sh

git filter-branch -f --env-filter '

an="$GIT_AUTHOR_NAME"
am="$GIT_AUTHOR_EMAIL"
cn="$GIT_COMMITTER_NAME"
cm="$GIT_COMMITTER_EMAIL"

if ["$GIT_COMMITTER_EMAIL" = "old@email.com"]
then
  cn="New author name"
  cm="new@email.com"
fi

export GIT_AUTHOR_NAME="$an"
export GIT_AUTHOR_EMAIL="$am"
export GIT_COMMITTER_NAME="$cn"
export GIT_COMMITTER_EMAIL="$cm"
'

echo "Run after"
echo "git push origin +master:master" 
```

Enter fullscreen mode Exit fullscreen mode