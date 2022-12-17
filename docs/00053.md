# 第一篇文章，picolisp 脚本

> 原文：<https://dev.to/cess11/first-post-picolisp-script-mok>

今天我做了一个在线程序，将当前目录下的一组 ISO 编码的 XML 文件转换成 UTF 8。

```
(for X (dir) (and (match '(@A "." "x" "m" "l") (chop X))(and (out "sh.sh" (prinl "#! /bin/sh"))(out "+sh.sh" (prinl (pack "iconv -f ISO-8859-1 -t UTF-8 " X " > utf8_" X)))(call 'sh "sh.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

一个可读性更强的注释版本可以是这样的:

```
(for X (dir) # for every file name in this directory
    (and 
        (match '(@A "." "x" "m" "l") (chop X)) # check if *.xml
        (and # it is or this is skipped, so...
            (out "sh.sh" # we write a new file
                (prinl "#! /bin/sh")) # with a hashbang (out "+sh.sh" # and then add an instruction
                (prinl 
                    (pack "iconv -f ISO-8859-1 -t UTF-8 " X " > utf8_" X)))
            (call 'sh "sh.sh"] # and finally run it 
```

Enter fullscreen mode Exit fullscreen mode

现在这不是很利索，但它可以很快变成一个更实用的风格。

```
(de qconv (Lst) # '(FromCode ToCode OutPrefix InSuffix FileList) 
    (for X (last Lst)
        (and 
            (match '(@A ~(chop (cadddr Lst))) (chop X))
            (and 
                (out "sh.sh" 
                    (prinl "#! /bin/sh"))
                (out "+sh.sh" 
                    (prinl 
                        (pack "iconv -f " (car Lst) "-t " (cadr Lst) " " X " > " (caddr Lst) X)))
                )))
    (call 'rm "sh.sh"] 
```

Enter fullscreen mode Exit fullscreen mode

不过还是很丑。