# [C 语言]能不能基于这个简单的 decode 函数写一个字符串编码函数？

> 原文：<https://dev.to/jochemstoel/c-language-can-you-write-a-string-encode-function-based-on-this-simple-decode-function-4eoo>

你好。从 CCProxy 的文档中，我得到了这个解码用户密码的函数。(数字串到字符串)
我还需要完全相反的东西，对密码进行编码所需的代码。(转换为“数字字符串”)。

对于懂一点 C 的人来说，这应该是相当微不足道的。如果你能用 JavaScript 复制这个函数，那就更好了。这样，我就不必使用命令行应用程序子进程来完成这个简单的编码/解码。

你能帮我吗？谢谢！

```
#include "stdafx.h" 
#include <stdio.h>
#include <stdlib.h>
#include <string.h> 
#define _MAX_BUF_LEN 2014 
static char* PasswordDecode(char * szPassword)
{
    char szEncode[1024];
    char strDecodePass[_MAX_BUF_LEN + 1] = { "" }, strPass[_MAX_BUF_LEN + 1] = { "" };

    strcpy(strDecodePass, szPassword);

    for (unsigned int i = 0; i < strlen(strDecodePass) / 3; i++)
    {
        char szCode[_MAX_BUF_LEN + 1];
        strcpy(szCode, strDecodePass + i * 3);
        szCode[3] = 0;
        int nCode = atoi(szCode);
        nCode = 999 - nCode;
        sprintf(szEncode, "%c", nCode);
        strcat(strPass, szEncode);
    }

    strcpy(szPassword, strPass);
    return szPassword;
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
int main(int argc, char **argv)
{
    printf(
        PasswordDecode(argv[1])
    );

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

要测试您的编码函数是否正确，请使用以下纯文本密码和它们的等效编码。

```
babble
901902901901891898

anus
902889882884

abcdefghijklmnopqrstuvwxyz1234567890
902901900899898897896895894893892891890889888887886885884883882881880879878877950949948947946945944943942951 
```

Enter fullscreen mode Exit fullscreen mode