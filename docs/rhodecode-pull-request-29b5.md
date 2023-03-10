# 在 RhodeCode 中 Pull Request 会导致错误，因此是临时修补程序

> 原文：<https://dev.to/shiena/rhodecode-pull-request-29b5>

[在 RhodeCode](http://rhodecode.org/) 中 Pull Request 时发生错误，输出了以下日志。
因为是`UnicodeEncodeError`，所以用 python 混合了固定的 str 和 unicode 的模式。
此时在`production.ini`中设定了`lang=ja`以表示为日语，如果想到`lang=en`，Pull Request 就成功了。

```
[error] 2013-07-26 00:55:06.550 ERROR [rhodecode.controllers.pullrequests] Traceback (most recent call last):
[error]   File "/home/rhodecode/rhodecode-venv/lib/python2.7/site-packages/rhodecode/controllers/pullrequests.py", line 340, in create
[error]     other_ref, revisions, reviewers, title, description
[error]   File "/home/rhodecode/rhodecode-venv/lib/python2.7/site-packages/rhodecode/model/pull_request.py", line 87, in create
[error]     pull_request=new
[error]   File "/home/rhodecode/rhodecode-venv/lib/python2.7/site-packages/rhodecode/model/changeset_status.py", line 135, in set_status
[error]     text='Auto status change to %s' % (ChangesetStatus.get_status_lbl(status)),
[error]   File "/home/rhodecode/rhodecode-venv/lib/python2.7/site-packages/pylons/i18n/translation.py", line 42, in __str__
[error]     return str(self.eval())
[error] UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-4: ordinal not in range(128) 
```

在 Traceback 上追踪看起来很可疑的`ChangesetStatus.get_status_lbl(status)`的处理的话，因为使用了 gettext 所以是正确的。
在`lang=en`中，因为在 ASCII 代码的范围内，所以也可以在 str 中工作，但是在其他语言中，因为不在 ASCII 代码的范围内，所以是预想的模式。
如以下补丁所示，将格式字符串设为 unicode 后，Pull Request 幸运地启动了。

```
--- rhodecode/model/changeset_status.py.orig    2013-06-16 12:58:07.000000000 +0900
+++ rhodecode/model/changeset_status.py 2013-07-26 01:39:31.683912780 +0900
@@ -132,7 +132,7 @@
         if not comment:
             from rhodecode.model.comment import ChangesetCommentsModel
             comment = ChangesetCommentsModel().create(
-                text='Auto status change to %s' % (ChangesetStatus.get_status_lbl(status)),
+                text=u'Auto status change to %s' % (ChangesetStatus.get_status_lbl(status)),
                 repo=repo,
                 user=user,
                 pull_request=pull_request, 
```

※补记
向本家报告错误后，很快就被[吸收了](https://secure.rhodecode.org/rhodecode/changeset/7baea802f739c2af885e98865c1a5ad8ccfeab10)。
我想会在下次以后的版本中发行。