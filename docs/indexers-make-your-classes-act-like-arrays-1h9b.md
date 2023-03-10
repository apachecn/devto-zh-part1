# 索引器——让你的类像数组一样工作

> 原文：<https://dev.to/adamkdean/indexers-make-your-classes-act-like-arrays-1h9b>

索引器很棒，我今天才真正开始有意识地使用它们，但它们是一个伟大的发现，从现在起我一定会将它们融入到我的代码中。

正如你在这里看到的，我们有一个类，但是我们访问一个值，就好像它是一个数组，或者是一个`Hashtable`。想想你该如何使用它！

```
VarClass vc = new VarClass();
vc["key"] = "value";
Console.WriteLine(vc["key"].ToString()); 
```

Enter fullscreen mode Exit fullscreen mode

代码也非常简单，它就像一个带有输入的属性，这里我们只是在哈希表的顶部放了一个层，当然你可以添加一些更有用的东西，但这向你展示了它是如何工作的:

```
class VarClass
{
    private Hashtable ht = new Hashtable();

    public object this[object key]
    {
        get
        {
            return ht[key];
        }
        set
        {
            ht[key] = value;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，你把他们放在方括号中的对象作为参数，你创建了一个属性，但是作为名字，你放了关键字 this，指的是实际的类对象。

我只是觉得这很简单，但又很棒..

我实际上是在 Dream.In.Code 为某人写一个简单的散列表克隆时遇到这个问题的。为了满足你垂死的好奇心，我将在这里发布这个令人敬畏的类:

```
class HashtableEx
{
    private List<object> keys = new List<object>();
    private List<object> values = new List<object>();

    public object this[object key]
    {
        get
        {
            int index = keys.IndexOf(key);
            if (index == -1) return null;
            else return values[index];
        }
        set
        {
            if (keys.Contains(key))
            {
                int index = keys.IndexOf(key);
                values[index] = value;
            }
            else
            {
                keys.Add(key);
                values.Add(value);
            }
        }
    }

    public object Get(object key)
    {
        int index = keys.IndexOf(key);
        return values[index];
    }

    public void Add(object key, object value)
    {
        keys.Add(key);
        values.Add(value);
    }

    public void Remove(object key)
    {
        int index = keys.IndexOf(key);
        keys.RemoveAt(index);
        values.RemoveAt(index);
    }

    public void Clear()
    {
        keys = new List<object>();
        values = new List<object>();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

像这样使用它:

```
static void Main()
{
    HashtableEx ht = new HashtableEx();

    ht["hello"] = "world";

    Console.WriteLine("Value of \"hello\": {0}", ht["hello"]);
    Console.WriteLine("Type of \"hello\": {0}", ht["hello"].GetType());

    Console.ReadKey();
} 
```

Enter fullscreen mode Exit fullscreen mode

这就给出了输出:

```
Value of "hello": world
Type of "hello": System.String 
```

Enter fullscreen mode Exit fullscreen mode