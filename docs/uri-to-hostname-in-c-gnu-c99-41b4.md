# C 语言中主机名的 uri(GNU-C99)

> 原文：<https://dev.to/adamkdean/uri-to-hostname-in-c-gnu-c99-41b4>

另一个 C 函数运行得很好，据我所知没有任何错误...

更新:修复了这个问题，没有泄漏！

```
/*
 * File:   utohn.c
 * Author: Adam K Dean
 * Description: gets a hostname from an uri
 *
 * Created on 11 January 2011, 22:12
 */

/* int utohn(char *uri, char *hostname)
 * Uri to Hostname
 * Author: Adam K Dean
 */
char *utohn(char *uri)
{
    char *pch, *hostname;
    if ((pch = strstr(uri, "://")) != NULL)
    {
        pch += 3;
        int i = 0;
        while(pch[i] != '/' && pch[i] != ':' && pch[i] != '\0') i++;
        hostname = (char *)calloc(i + 1, sizeof(char));
        return strncpy(hostname, pch, i);
    }
    else return NULL;
} 
```

Enter fullscreen mode Exit fullscreen mode

和使用代码:

```
int main(int argc, char** argv)
{
    char hn1[] = "http://www.gentoo.org/";
    char hn2[] = "http://www.gentoo.org/docs/";
    char hn3[] = "http://www.gentoo.org:80/";
    char hn4[] = "127.0.0.1";

    printf("%s -> %s\n", hn1, utohn(hn1));
    printf("%s -> %s\n", hn2, utohn(hn2));
    printf("%s -> %s\n", hn3, utohn(hn3));
    printf("%s -> %s\n", hn4, utohn(hn4));

    return (EXIT_SUCCESS);
} 
```

Enter fullscreen mode Exit fullscreen mode

和结果:

`http://www.gentoo.org/ -> www.gentoo.org
http://www.gentoo.org/docs/ -> www.gentoo.org
http://www.gentoo.org:80/ -> www.gentoo.org
127.0.0.1 -> (null)`

页（page 的缩写）s，我和 gentoo 没有关系，实际上我使用 centos..我只是喜欢输入 gentoo 有多简单..非常好！

享受