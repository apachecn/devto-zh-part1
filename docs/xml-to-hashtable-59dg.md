# XML 到哈希表

> 原文：<https://dev.to/adamkdean/xml-to-hashtable-59dg>

如今，XML 经常被用于很多事情，主要是因为它非常灵活、轻量级并且易于理解。从配置文件到布局，好像都在用。系统内部的所有方法。Xml 您拥有您可能需要的一切，当然，除非您像我一样沉迷于通用列表和散列表...

我不喜欢遍历节点，我也不喜欢孩子，所以在代码中看到 ChildNode 会让我内心哭泣。今天，我向您介绍一种方法，将那些烦人的 XmlNodes 转换成我们都能理解的东西，即哈希表。或者，一个散列表的散列表，附带一些通用列表。

让我们看看下面的 XML，它很好也很简单。

你可以在这里找到 XML 文件:[http://www.w3schools.com/XML/note.xml](http://www.w3schools.com/XML/note.xml)

```
<note>
    <to>Tove</to>
    <from>Jani</from>
    <heading>Reminder</heading>
    <body>Don't forget me this weekend!</body>
</note> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用我的 XML to Hashtable 解析器方法`ParseNode`轻松访问它:

```
string xmlFilePath = @"http://www.w3schools.com/XML/note.xml";

XmlDocument doc = new XmlDocument();
doc.Load(xmlFilePath);
XmlNode main = doc.DocumentElement;
Hashtable note = ParseNode(main);

string to = note["to"].ToString();
string from = note["from"].ToString();
string heading = note["heading"].ToString();
string body = note["body"].ToString(); 
```

Enter fullscreen mode Exit fullscreen mode

但是如果有多个音符呢？如果有许多标签是相同的呢？我已经领先你了，夏洛克，看看这有多简单。

我们将使用的 XML 遵循这个简单的布局:[http://www.w3schools.com/XML/simple.xml](http://www.w3schools.com/XML/simple.xml)

```
<breakfast_menu>
    <food>
        <name>Belgian Waffles</name>
        <price>$5.95</price>
        <description>two of our famous Belgian Waffles with plenty of real maple syrup</description>
        <calories>650</calories>
    </food>
    <food>
        <name>Strawberry Belgian Waffles</name>
        <price>$7.95</price>
        <description>light Belgian waffles covered with strawberries and whipped cream</description>
        <calories>900</calories>
    </food>
</breakfast_menu> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们只需选择所需的节点(食物)，将其转换为一个列表，然后遍历每一份食物。

```
string xmlFilePath = @"http://www.w3schools.com/XML/simple.xml";

XmlDocument doc = new XmlDocument();
doc.Load(xmlFilePath);
XmlNode docNode = doc.DocumentElement;
Hashtable menu = ParseNode(docNode);

foreach (Hashtable food in (List<hashtable>)menu["food"])
{
    Console.WriteLine("Name: {0}", food["name"]);
    Console.WriteLine("Price: {0}", food["price"]);
    Console.WriteLine("Description: {0}", food["description"]);
    Console.WriteLine("Calories: {0}", food["calories"]);
    Console.WriteLine("");
}

Console.ReadKey(); 
```

Enter fullscreen mode Exit fullscreen mode

我们将得到输出:

```
Name: Belgian Waffles
Price: $5.95
Description: two of our famous Belgian Waffles with plenty of real maple syrup
Calories: 650

Name: Strawberry Belgian Waffles
Price: $7.95
Description: light Belgian waffles covered with strawberries and whipped cream
Calories: 900

Name: Berry-Berry Belgian Waffles
Price: $8.95
Description: light Belgian waffles covered with an assortment of fresh berries and whipped cream
Calories: 900

and so on.. 
```

Enter fullscreen mode Exit fullscreen mode

所以，事不宜迟，你们都渴望复制粘贴的部分(我希望！)，方法:

```
public static Hashtable ParseNode(XmlNode node)
{
    Hashtable ht = new Hashtable();
    // loop through all nodes within the node
    foreach (XmlNode n in node.ChildNodes)
    {
        string name = n.Name;
        object value = null;

        // if it has nodes within this node, and more than just one, then parse them
        if (n.HasChildNodes)
        {
            /* "In order to understand recursion, one must first understand recursion." */
            if (n.ChildNodes.Count > 1) value = (object)ParseNode(n);
            else
            {
                // if theres only one, it may be the value, so take the value
                if (n.ChildNodes[0].NodeType == XmlNodeType.Text)
                    value = (object)n.ChildNodes[0].Value;
                else value = (object)ParseNode(n);
            }
        }
        else value = (object)n.Value;

        // as hashtables can't have a key the same, and xml can have two nodes of the same name
        // we have to put the hashtables into a list if there are more than one of the same node
        // example: <test></test> -> ht["test"] = Hashtable
        // but: <test></test><test></test> -> ht["test"] = List<hashtable>
        if (ht.ContainsKey(name))
        {
            // list exists, add to it
            if (ht[name] is List<hashtable>)
            {
                List<hashtable> list = (List<hashtable>)ht[name];
                list.Add((Hashtable)value);
                ht[name] = list;
            }
            // list doesn't exist, so create it
            else if (ht[name] is Hashtable)
            {
                List<hashtable> list = new List<hashtable>();
                Hashtable htTmp = (Hashtable)ht[name];
                list.Add(htTmp);
                list.Add((Hashtable)value);
                ht[name] = list;
            }
        }
        else ht.Add(name, value);
    }
    return ht; // and return it
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我最喜欢的方法之一。我希望它能像帮助我一样帮助你，这是我阅读 Xml 文件的首选方法。也许有一天我会写一个 Hashtable to XML writer。有一天。