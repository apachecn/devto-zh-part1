# 修改您上次提交的消息

> 原文：<https://dev.to/adamkdean/modify-your-last-commit-message-42o0>

修改上一次提交的消息很容易，只需添加`--amend`并再次提交。

```
git commit -m "Fixed issue where udpate failed" 
```

Enter fullscreen mode Exit fullscreen mode

哦，又来了，看那个错别字！

```
git commit --amend -m "Fixed issue where update failed" 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。