# 用 c#进行线性搜索

> 原文：<https://dev.to/aloksdiptim/linear-search-with-c-b6c>

```
 class Program
    {

        static void Main(string[] args)
        {
            linearIndex(0);
            Console.ReadLine();
        }

        public static int linearIndex(int searchNumber)
        {

            int index = 0;
            int[] intArray = { 
        47,56,38,5,40,17,26,58,56,25,10,14,38,10,36,15,36,53,55,33,10,48,6,45,

       38,10,49,47,53,10,52,45,32,46,55,19,55,53,11,39,36,25,26,41,15,1,43,
       40,13,53,39,11,53,33,5,21,36,53,15,36};

         l1:   if (searchNumber == intArray[index])
            {
                Console.WriteLine("found at! : {0}", index);
                return index;
            }

            index ++;
            if (index < intArray.Length)
            {
                goto l1;
            }
            else
            {
                Console.WriteLine("Not found at any Index");
            }

            return 0;
        }

    } 
```

控制台应用程序从 main 方法开始，我传入一个 int 0 来检查 arrayList，

有一个比较，只要数组的索引小于数组长度，我们就可以继续搜索。

如果选项是最大限度的，那么这意味着我们没有看到号码。