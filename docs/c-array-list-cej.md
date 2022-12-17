# c++数组列表

> 原文：<https://dev.to/crhodes2/c-array-list-cej>

我正在尝试解决在数组中找到并显示最大年龄的算法。这是我目前掌握的情况。

```
#include <iostream>

using namespace std;

int main()
{
    int brothers[5] = {6,3,24,12,22};
    int i = 0;
    int eldestAge = 0;

    for (int j = 0; j < brothers[i]; ++j)
        {
            if (eldestAge < brothers[i])
            {
                eldestAge = brothers[i];
            }
            else
            {
                // do nothing
            }
        }

        cout << "Eldest Age is = " + eldestAge << endl;

        system("pause");
} 
```

然而，输出除了报价之外不显示任何内容。请让我知道我做错了什么。