# 在列表中循环，一次一步

> 原文：<https://dev.to/adamkdean/cycling-through-a-list-one-step-at-a-time-4ami>

因此，我目前正在做的项目将有一个存储在通用列表中的服务器(节点)列表(目前)，节点控制器将需要循环遍历这些列表，并在返回原始列表之前向每个服务器发出请求。这样，负载可以在不同的节点之间平衡。

如何做到这一点似乎很简单，但我的大脑就是做不到..告诉我如何做到这一点，可能是因为隔壁正在吵吵闹闹地做爱，这意味着我必须改变我的播放列表，让它变得更大声、更饱满，这不仅让我的思维列车脱轨，还把钢水倒在了铁轨上。

反正隔壁三分钟奇迹现在好像也做好了，我想出了一个很好看的小索引器。

这更多的是给我参考，而不是给你教育，但你永远不知道，你可能会有顿悟！

```
using System;
using System.Collections.Generic;

namespace ListShuffle
{
    class Program
    {
        static void Main(string[] args)
        {
            ListShuffle ls = new ListShuffle();
            ls.Run();
        }
    }

    class ListShuffle
    {
        private int index = 0;
        private List<item> items = new List<item>();

        public void Run()
        {

            Item a = new Item("A");
            Item d = new Item("D");

            items.Add(a);
            items.Add(new Item("B"));
            items.Add(new Item("C"));
            items.Add(d);

            Console.WriteLine(items[index].Name);       // a
            Console.WriteLine(items[NextIndex()].Name); // b
            Console.WriteLine(items[NextIndex()].Name); // c
            Console.WriteLine(items[NextIndex()].Name); // d

            items.Remove(a);

            Console.WriteLine(items[NextIndex()].Name); // b
            Console.WriteLine(items[NextIndex()].Name); // c
            Console.WriteLine(items[NextIndex()].Name); // d
            Console.WriteLine(items[NextIndex()].Name); // b

            items.Remove(d);

            Console.WriteLine(items[NextIndex()].Name); // c
            Console.WriteLine(items[NextIndex()].Name); // b
            Console.WriteLine(items[NextIndex()].Name); // c
            Console.WriteLine(items[NextIndex()].Name); // b

            Console.ReadKey();
        }

        private int NextIndex()
        {
            if (++index >= items.Count) index = 0;
            return index;
        }
    }

    class Item
    {
        public string Name { get; set; }
        public Item(string name) { Name = name; }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，我意识到这不是真正的“洗牌”,但是当你洗牌的时候，比如说在监狱里，你不会随意把脚放在任何地方，随意选择你要去的地方。不，那叫跳舞。洗牌，是一点一点的移动。

隔壁的超人看起来很勇敢，很高兴我在他休息的时候完成了这件事。

FML