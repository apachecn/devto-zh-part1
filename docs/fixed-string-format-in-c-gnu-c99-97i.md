# 固定字符串。C 语言格式(GNU-C99)

> 原文：<https://dev.to/adamkdean/fixed-string-format-in-c-gnu-c99-97i>

目前，我已经修复了 strrep 字符串替换函数，因为使用 atoi 时出现了一个 bug。

我还意识到我把它叫做 string . replace——我的错——它实际上是我想到的 string.format，但更多的是一个 var，用来替换没有格式化的 var！

我现在已经完成了 C#字符串的编写。在 C (GNU-C99)中替换复制，效果很好。下面的代码将允许您像在 C#中一样在格式字符串中替换字符串(char *s)。例如，您可以输入“这{0}个测试，{0}它{1}？”以及“是”和“正在工作”来获得“这是一个测试，它正在工作吗？”。

更新:修复，现在无泄漏。

```
/*
 * File:   strrep.c
 * Author: Adam K Dean
 * Description: string replacement (replication of string.replace for C)
 *
 * Created on 11 January 2011, 10:01
 * Fixed on 12 January 2011, 12:44
 */

char *strrep(char *format, int argc, char *arg, ...);
void itoa(int value, char *str, int base);
void strreverse(char *begin, char *end);

int main()
{
    char *ret = strrep("this {0} a test, {0} it {1}?", 2, "is", "working");
    printf("%s\n", ret);

    return 0;
}

/* char *strrep(char *format, int argc, char *arg, ...)
 * String.Replace replication
 * Author: Adam K Dean
 */
char *strrep(char *format, int argc, char *arg, ...)
{
    char *tmp_left, *tmp_right;
    char start, end, param;
    int l_len, r_len, f_sz;

    va_list marker;
    va_start(marker, *arg);

    for(int i = 0; i < argc; i++)
    {
        printf("arg: %s\n", arg);

        for(int c = 1; c < strlen(format) - 1; c++)
        {
            start = format[c-1];
            param = format[c];
            end = format[c+1];

            char *ialpha = (char *)malloc((int_strlen(i) + 1) * sizeof(ialpha));
            itoa(i, ialpha, 10);

            if (start == '{' && end == '}' && isdigit(param) && param == ialpha[0])
            {
                l_len = c - 1;
                r_len = (strlen(format) - 3) - l_len;

                tmp_left = (char *)calloc(l_len + 1, sizeof(char));
                tmp_right = (char *)calloc(r_len + 1, sizeof(char));

                strncpy(tmp_left, format, l_len);
                strncpy(tmp_right, &format[c + 2], r_len);                

                f_sz = l_len + r_len + strlen(arg);

                format = (char *)calloc(f_sz + 1, sizeof(char));

                strcpy(format, tmp_left);
                strcpy(&format[l_len], arg);
                strcpy(&format[l_len + strlen(arg)], tmp_right);

                free(tmp_left);
                free(tmp_right);
            }

            free(ialpha);
        }

        arg = va_arg(marker, char *);
    }

    va_end(marker);
    return format;
}

/* int_strlen(int val)
 * String length of an int
 * Author: Adam K Dean
 */
int int_strlen(int val)
{
    int v = val / 10;
    int i = 1, count = 1;

    while(v > i - 1)
    {
        i *= 10;
        count ++;
    }

    return count;
}

void strreverse(char *begin, char *end)
{
    char aux;
    while(end > begin)
        aux = *end, *end-- = *begin, *begin++ = aux;
}

void itoa(int value, char *str, int base)
{
    static char num[] = "0123456789abcdefghijklmnopqrstuvwxyz";

    char* wstr=str;
    int sign;

    if (base<2 || base>35){ *wstr='\0'; return; }

    if ((sign=value) < 0) value = -value;

    do *wstr++ = num[value % base]; while(value /= base);

    if(sign < 0) *wstr++ = '-';
    *wstr = '\0';

    strreverse(str, wstr - 1);
} 
```

Enter fullscreen mode Exit fullscreen mode