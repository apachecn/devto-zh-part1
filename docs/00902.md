# 使用 set builtin 使您的 shell 脚本更加安全。

> 原文：<https://dev.to/brianbola/make-your-shell-scripts-safer-with-set-builtin-2ie4>

set 允许您更改壳选项的值并设置位置参数，或者显示壳变量的名称和值。

set-e
-e 选项将导致 bash 脚本在命令失败时立即退出。这通常很好，因为它可以防止意外错误造成的损坏。

set -u
该选项使 bash shell 将未设置的变量视为错误并立即退出。这对于调试&捕捉变量名中的错别字很有用。
也是为了保持脚本的整洁。

set -o pipefail
该选项将管道的退出代码设置为最右边命令的代码，以非零状态退出，或者如果管道的所有命令都成功退出，则设置为零。

当管道中的前一个命令不成功时，这对于停止导致损害的脚本执行非常有用。

在你的下一个脚本中包含以下内容，并从中获益。

```
#!/bin/bash
set -euo pipefail 
```

Enter fullscreen mode Exit fullscreen mode