# 软件开发人员测验

> 原文：<https://dev.to/funkysi1701/software-developer-quiz-3p8d>

搬家，缺乏互联网和缺乏灵感导致了最近帖子的缺乏，但希望能有更多的帖子出现。我整理了一些文件，发现了一个旧的软件开发测试。我想我应该试一试。

非常感谢 Keith 最初编写了这个测验。

**问题:**

1.  编写一个函数，确定一个字符串是否以大写字母 A-Z 开头
2.  写一个函数，确定已知半径的圆的面积
3.  将整数数组中的所有值相加
4.  给定一个名为“Nodes”的表，该表具有以下结构和示例数据(参见下面的问题之后):…其中 ID 是主键，ParentID 引用 ID，请完成以下操作:
    *   编写一个存储过程来返回给定节点 ID 下的所有节点
    *   描述如何编写查询来返回给定节点 ID 下任意深度的所有节点(即递归)
5.  写一个函数来得到 100 万的质数
6.  你已经得到了下面的代码来回顾(在表格下面)——你会给开发者什么样的评论？

| 身份证明 | ParentID | 名字 | 类型 | 深度 |
| --- | --- | --- | --- | --- |
| one | 空 | 我的文档 | 文件夹 | Zero |
| Two | one | 我的照片 | 文件夹 | one |
| three | one | 我的简历 | 文件 | one |
| four | Two | 我的照片 | 文件 | Two |

```
CREATE PROCEDURE GetNode
@NodeId INT
AS

DECLARE @ID INT, @ParentID INT, @Name NVARCHAR(255)
DECLARE @Type NVARCHAR(20), @Depth INT

SELECT @ID = ID FROM Nodes WHERE ID = @ID
SELECT @ParentID = ParentID FROM Nodes where ID = @ID
IF (EXISTS(SELECT NULL FROM Nodes WHERE ID = @ID AND Name = NULL))
SELECT @Name = ''
ELSE
SELECT @Name = Name FROM Nodes WHERE ID = @ID

SELECT @Type = Type FROM Nodes WHERE ID = @ID
SELECT @Depth = Depth FROM Nodes WHERE ID = @ID

SELECT @ID, @ParentID, @Name, @Type, @Depth 
```

Enter fullscreen mode Exit fullscreen mode

**我的答案:**

```
static bool GetUpper(string var)
{
  if (char.IsUpper(var[0]))
  {
    return true;
  }
  else
  {
    return false;
  }
}

static double AreaOfCircle(int radius)
{
  double area = 0;
  area = Math.PI * radius * radius;
  return area;
}

static int SumArray()
{
  int[] MyArray = new int[10] { 1, 2, 5, 12, 4, 9, 8, 18, 9, 6 };
  int Sum = MyArray.Sum();
  return Sum;
}

create procedure getnodes
(
  @node int
)
select * from dbo.nodes where parentid = @node 
```

Enter fullscreen mode Exit fullscreen mode

对于一个递归查询，我会写一些类似于的东西，但是它需要根据深度来定制，比如深度越高，连接越多

```
select * from dbo.nodes n1
join dbo.nodes n2 on n1.id = n2.ParentId
join dbo.nodes n3 on n2.id = n3.ParentId
where n1.parentid = 4544054

static void prime()
{
  Console.WriteLine("Prime: 1");
  for (long i = 3; i <= 1000000;i++ )
  {
    bool isprime = true;
    for (long j = 2; j <i; j++)
    {
      if(i%j==0)
      {
        isprime = false;
        break;
      }
    }
    if (isprime)
    {
      Console.WriteLine("Prime: "+i);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

参数周围没有括号，@NodeId 参数从未使用，select[@ Id](https://dev.to/id)= id from dbo . nodes 其中 id = [@id](https://dev.to/id) 与传递给它的 id 相同是无意义的设置，Name = NULL 应该是 Name is NULL，no from 在上次查询中指定。可能还有更多问题。