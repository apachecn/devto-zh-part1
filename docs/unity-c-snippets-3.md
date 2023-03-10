# Unity C#代码片段

> 原文：<https://dev.to/octi/unity-c-snippets-3>

日期时间和长整型。

```
DateTime now    = DateTime.Now;

// convert DateTime to long.
long nowTics = now.Ticks;

// convert long to DateTime.
DateTime dt1 = DateTime.FromFileTime (nowTics); // NG.
DateTime dt2 = DateTime.FromFileTimeUtc (nowTics); // OK!

Debug.Log ("TEST now  .. (" + now.ToString ("yyyy-MM-dd HH-mm-ss") + ")");
Debug.Log ("TEST dt1  .. (" + dt1.ToString ("yyyy-MM-dd HH-mm-ss") + ")");
Debug.Log ("TEST dt2  .. (" + dt2.ToString ("yyyy-MM-dd HH-mm-ss") + ")"); 
```

Enter fullscreen mode Exit fullscreen mode

String 和 int。

```
// string to int.
string nString = "128";
int n = int.Parse(nString);

// int to string
int a = 128;
string aString = a.ToString(); 
```

Enter fullscreen mode Exit fullscreen mode

平台判别

```
// Platform Dependent.

#if UNITY_EDITOR
Debug.Log("Unity Editor.");
#else
Debug.Log("Not Unity Editor.");
#endif

#if UNITY_EDITOR_WIN
Debug.Log("Windows editor");
#elif UNITY_EDITOR_OSX
Debug.Log("macOS editor.");
#endif

#if UNITY_STANDALONE_WIN
Debug.Log("Windows standalone.");
#elif UNITY_STANDALONE_OSX
Debug.Log("macOS standalone.");
#endif

#if UNITY_EDITOR_WIN || UNITY_STANDALONE_WIN
Debug.Log("Windows.");
#elif UNITY_EDITOR_OSX || UNITY_STANDALONE_OSX
Debug.Log("macOS.");
#endif

#if UNITY_IPHONE
Debug.Log("ios.");
#elif UNITY_ANDROID
Debug.Log("android.");
#endif 
```

Enter fullscreen mode Exit fullscreen mode

构造函数

```
// Constructor.
public class Data
{
    public int id;

    public Data( int aId ){
        id = aId;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

枚举-基本。

```
public enum Type{
    TYPE0   = 0,
    TYPE1   = 1,
    TYPE2   = 2,
    TYPE3   = 3,
    TYPE4   = 4,
    DEFAULT = TYPE0,
}

Type typeTest = Type.DEFAULT; 
```

Enter fullscreen mode Exit fullscreen mode

Enum -从整数或字符串转换。

```
using System;

Type type1 = (Type)Enum.ToObject(typeof(Type), 1);

Type type2 = (Type)Enum.Parse(typeof(Type), "TYPE2"); 
```

Enter fullscreen mode Exit fullscreen mode

Enum -转换为整数或字符串。

```
int  intType0  = (int)Type.TYPE0; // 0

string stringType0 = Type.TYPE0.toString(); // "TYPE0" 
```

Enter fullscreen mode Exit fullscreen mode

数组

```
float[] values = {1.0f, 1.0f, 1.0f};

float[] threeValues = new float[3];

// float[] mustSetSize = new float[];    // Error. 
```

Enter fullscreen mode Exit fullscreen mode

给孩子们的循环。

```
foreach (Transform child in transform)
{
    // child.gameObject.SetActive(false);
} 
```

Enter fullscreen mode Exit fullscreen mode

用 BOM 写 UTF-8

```
string filepath = "test.txt";
bool append = false;
StreamWriter sw = new StreamWriter( filepath, append, System.Text.Encoding.UTF8 );

sw.Write ("UTF-8!\n");

sw.Flush();
sw.Close(); 
```

Enter fullscreen mode Exit fullscreen mode

打印堆栈跟踪

```
Debug.Log("called from.. @"+UnityEngine.StackTraceUtility.ExtractStackTrace()); 
```

Enter fullscreen mode Exit fullscreen mode