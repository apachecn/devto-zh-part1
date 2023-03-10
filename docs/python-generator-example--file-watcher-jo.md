# Python 生成器示例:文件监视器

> 原文：<https://dev.to/an33shk4tl4/python-generator-example--file-watcher-jo>

## Python - Generators == >使用文件监视器演示

```
import os
from datetime import datetime as dt
import time

def checkfile(filename):
    try:
        st = os.stat(filename)
        fmtime = st.st_mtime
    except Exception as e:
        print e.message
        return
    print "*********init modification timestamp" , dt.fromtimestamp(fmtime).ctime()
    while True:
        print 'entered while loop'
        st = os.stat(filename)
        print 'collected file stats'
        yield dt.fromtimestamp(fmtime).ctime()
        print 'yield executed'
        if dt.fromtimestamp(st.st_mtime).ctime() <> dt.fromtimestamp(fmtime).ctime():
            print "File modification changed - new file modification timestamp", dt.fromtimestamp(fmtime).ctime()
            fmtime = st.st_mtime
            print 'set new mod time inside if condition'
        else:
            print 'Mod timestamps equal'
        time.sleep(2)

chk = checkfile(r'C:\filetowatch.txt')

for i in chk:
    print chk.next()
    print '---for loop next iteration after gen call' 
```

Enter fullscreen mode Exit fullscreen mode

Python 有一个有趣的特性，叫做生成器，它允许你享受迭代器的好处，但是没有内存开销。一般来说，迭代器是内存中的一个对象，带有一个对象列表。我在上面写了一个简单的代码，演示了生成器是如何工作的。您需要创建另一个脚本来删除并重新创建一个文件，以便看到这一工作。

### 那是什么代码？

该代码只是监视文件修改时间，并识别文件中是否有任何更改。一个简单的文件监视程序。我使用了 python 库，因此没有外部依赖性。

### 学习

文件创建时间对相同的文件名无效(至少在 windows 中)，因为 Windows 不会更改创建时间。查看这篇[文章](https://support.microsoft.com/en-us/help/172190/windows-nt-contains-file-system-tunneling-capabilities)了解更多关于“名称”隧道的信息

请随时留下您的反馈...