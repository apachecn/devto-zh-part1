# 从数组中删除重复项(使用 C#)

> 原文：<https://dev.to/dance2die/removing-duplicates-from-an-array-using-c-1fl>

* <small>精选图片——[唐·克劳利](https://www.flickr.com/photos/doncrowley/)的[removed.jpg](https://www.flickr.com/photos/doncrowley/2194796278/in/photolist-4kWUt9-7nJpV8-7t4XtP-8yCe1C-5iFzwq-8yCd2u-247GL-2hZHyH-247Er-j7z7T-b5fzx-4vqboo-h8DgpC-4WH8Lw-Re2ChR-jnfYUr-4WHaDw-4ZRqzt-247GT-247GS-4Ky6xK-247GN-247Gz-247GK-arm2re-6Vb9QA-5XRyEq-b3d2BD-G9oA9n-e4VTdH-dvgarU-dkRhkT-bsfoE5-8ZmUAd-5bWCeC-6aAXSt-jKr7zA-baHzmc-4WCR5x-syW9C5-jnfXcD-dg8w16-4WCVQp-4WCUsF-4ZRqex-4WHbmu-nN3hSJ-6icUnS-SSCTUz-8jAAex)，被 SA 用在[下](https://creativecommons.org/licenses/by-sa/2.0/)</small>

这篇文章来自去年的*但是看了[赛](https://dev.to/saigowthamr)的文章，*

 *## 文章不再可用

去检查一下👆出~真好

我想分享一个 C#版本。

* * *

我尝试了几种不同的方法来删除数组中的重复项。

数组可以是有序的，也可以是无序的。

这个问题有很多解决方案，我将展示两种不同的“无序”数组方法，一种是有序数组方法。

让我们先解决简单的问题。

#### **案例 1—**从一个*有序*数组中删除重复项。

假设给你一个数组，`{1, 2, 2, 3, 4, 4, 4, 5}`。

由于数组是有序的，您可以简单地检查下一项是否与当前检查的项相同。这是实现算法的函数。

```
private static int[] RemoveDuplicatesByCheckingPreviousElement(int[] a)
{
    List<int> result = new List<int>();
    for (int i = 0; i < a.Length - 1; i++)
    {
        if (a[i] != a[i + 1])
        {
            result.Add(a[i]);
        }
    }

    result.Add(a[a.Length - 1]);
    return result.ToArray();
} 
```

Enter fullscreen mode Exit fullscreen mode

如果下一项与当前项不同，则只添加当前项。例如]

例如)而在第 3 项“2”上，则下一项是“3”，因此我们知道自从数组被排序以来我们已经通过了所有可能的重复项。

我们需要将最后一项添加到结果中，因为最后一项没有下一个元素来检查是否重复(第 12 行)。

你可以在这个 YouTube 视频中找到更多的细节，[如何在 C/C++](https://youtu.be/kdAiCZQVuvI) 中从排序后的数组中删除重复项。

源代码是 C++的，但我把它翻译成了 C#。

#### **案例 2.1**–使用集合从一个*无序*数组中移除重复项。

根据定义，集合不允许重复值。所以在。NET 中，通过传递数组来实例化 HashSet 对象会自动移除重复项。

但是这个练习的重点是手动完成。我使用 [HashSet](https://msdn.microsoft.com/en-us/library/bb359438(v=vs.110).aspx) ，因为它有 O(1)的查找时间，而数组有 O(N)的时间复杂度。

算法很简单。在遍历数组中的每一项时，如果没有看到当前项，我们将它添加到集合中，如下面的代码片段所示。

```
private static int[] RemoveDuplicatesUsingHashTable(int[] a)
{
    HashSet<int> alreadySeen = new HashSet<int>();
    foreach (int item in a)
    {
        if (!alreadySeen.Contains(item))
            alreadySeen.Add(item);
    }
    return alreadySeen.ToArray();
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这个 YouTube 视频上找到细节， [[ **采访**提问】数组](https://youtu.be/H1TOX-TposY)中的重复整数。

#### **案例 2.2**–通过排序从一个*无序*数组中移除重复项，并检查下一个元素是否有重复项。

该算法是对当前数组进行排序，并应用情况 1 算法。

```
private static int[] RemoveDuplicatesBySortingFirst(int[] a)
{
    Array.Sort(a);
    return RemoveDuplicatesByCheckingPreviousElement(a);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意`RemoveDuplicatesByCheckingPreviousElement`是案例 1 中的函数名。

对数组进行排序需要 O(N log N)时间，而情况 1 需要 O(N)时间，因此总时间复杂度为 O(N)。

### 结论

当你在面试中被要求解决这个问题时，问面试官数组是否排序。根据上下文使用上述任何算法；

如果阵列是有序的，则使用情况 1 算法，否则使用 2.1 或 2.2。但是请注意，案例 2.2 返回的数组与原始数组的顺序不同。

例如)给定一个数组`a = {8, 1, 1, 3, 2, 2}`，

```
RemoveDuplicatesBySortingFirst(a) 
```

Enter fullscreen mode Exit fullscreen mode

以上函数返回`{1, 2, 3, 8}`，**而不是** `{8, 1, 3, 2}`。

完整的工作源代码可以在 [GitHub](https://github.com/dance2die/Demo.LearnByDoing/blob/master/Demo.LearnByDoing.General/RemoveDuplicatesProgram.cs) 上找到。

*帖子[删除数组中的重复项(使用 C#)](https://www.slightedgecoder.com/2017/03/18/removing-duplicates-array-using-c/) 最早出现在[微边缘编码器](https://www.slightedgecoder.com)上。**