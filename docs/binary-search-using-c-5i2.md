# 使用 c#的二分搜索法

> 原文：<https://dev.to/aloksdiptim/binary-search-using-c-5i2>

[![alt text](img/1d58a3c736cc2377441a9f0601a92eff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2MGNDgtx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0i8tqf0sjtadcdm51m3x.jpg)

#### 如果你需要帮助，我会分解文件。

```
 public static int BinarySearch(int arraySize, int targetNumber)
    {

        Random random = new Random();
        int[] figures = new int[arraySize];

        while (!figures.Contains(targetNumber))
        {
            for (int i = 0; i < figures.Length; i++)
            {
                figures[i] = random.Next(0, 15);
            }
        }

        //SORTING OUT THE ARRAY
        Array.Sort(figures);

        int minimum = 0;
        int maximum = figures.Length - 1;

        l1:

        int center = (maximum + minimum) / 2;

        if (figures[center] == targetNumber)
        {
            return center;
        }

        if (minimum > maximum)
        {
            Console.WriteLine("This kind of Array doesnt exist and this is false. Please add some element to the element. Thanks!");
        }

        if (figures[center] < targetNumber)
        {
            minimum = center + 1;
        }

        if (figures[center] > targetNumber)
        {
            maximum = center - 1;
        }

        if (figures[center] == targetNumber)
        {
            return center;
        }

        goto l1;

    }

    static void Main(string[] args)
    {
        Console.WriteLine(BinarySearch(10, 8));

        Console.WriteLine("The End");
        Console.Read();
    }
} 
```

}