# 如何将 CSV 和 XLS 数据导入熊猫

> 原文：<https://dev.to/m0nica/how-to-import-csv-and-xls-data-into-pandas-ejl>

[![Pandas](img/cbdd228a6054984cbad421235119a569.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vRW3rgvM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k6n0dfj5z0zz9l1fbht5.jpg) 
[熊猫](http://pandas.pydata.org/)是一个 Python 数据分析库。它允许您摆弄数据并执行强大的数据分析。

在这个例子中，我将向你展示如何在 Pandas 中从 CSV 和 Excel 文件中读取数据。然后，您可以将读取的输出保存为 Pandas 数据帧。以下练习中使用的样本数据由[https://mockaroo.com/](https://mockaroo.com/)生成。

```
import pandas as pd 
```

Enter fullscreen mode Exit fullscreen mode

```
csv_data_df = pd.read_csv('data/MOCK_DATA.csv') 
```

Enter fullscreen mode Exit fullscreen mode

用`.head()`预览数据的前 5 行，以确保它已加载。

```
csv_data_df.head() 
```

Enter fullscreen mode Exit fullscreen mode

```
.dataframe thead tr:only-child th {
    text-align: right;
}

.dataframe thead th {
    text-align: left;
}

.dataframe tbody tr th {
    vertical-align: top;
} 
```

Enter fullscreen mode Exit fullscreen mode

|  | 身份证明（identification） | 名字 | 姓氏 | 电子邮件 | 性别 | ip 地址 |
| --- | --- | --- | --- | --- | --- | --- |
| Zero | one | 罗斯 | Ricart | rricart0@berkeley.edu | 男性的 | 217.151.154.186 |
| one | Two | 詹恩 | 辉瑞 | jpizer1@usnews.com | 女性的 | 104.123.13.234 |
| Two | three | 德莱尼 | Sulley | dsulley2@xing.com | 男性的 | 6.101.0.150 |
| three | four | 尼斯湖水怪 | 费恩 | nfeirn3@samsung.com | 女性的 | 97.93.173.170 |
| four | five | 女子名 | 飘忽效果器 | nflanner4@woothemes.com | 女性的 | 174.228.138.242 |

如果你还没有，你将需要`pip install xlrd`。以便从 Excel 导入数据。

```
import xlrd
excel_data_df = pd.read_excel('data/MOCK_DATA.xlsx') 
```

Enter fullscreen mode Exit fullscreen mode

```
excel_data_df.head() 
```

Enter fullscreen mode Exit fullscreen mode

```
.dataframe thead tr:only-child th {
    text-align: right;
}

.dataframe thead th {
    text-align: left;
}

.dataframe tbody tr th {
    vertical-align: top;
} 
```

Enter fullscreen mode Exit fullscreen mode

|  | 身份证明（identification） | 名字 | 姓氏 | 电子邮件 | 性别 | ip 地址 |
| --- | --- | --- | --- | --- | --- | --- |
| Zero | one | 克洛里斯 | 安特利夫 | cantliff0@shareasale.com | 女性的 | 131.17.2.171 |
| one | Two | 布莱恩！布莱恩 | 吉罗克 | bgierok1@posterous.com | 男性的 | 245.41.126.3 |
| Two | three | 粉状填料 | 技能 | fskells2@creativecommons.org | 女性的 | 75.0.34.132 |
| three | four | 动力操作研究机构（dynamic operators research apparatus 的缩写） | 教务长 | dprivost3@newsvine.com | 女性的 | 51.202.4.39 |
| four | five | 安娜贝拉 | 黑客 | ahucker4@typepad.com | 女性的 | 124.80.181.41 |

图片由 jballeis(自己的作品) [CC BY-SA 3.0](http://creativecommons.org/licenses/by-sa/3.0) 提供，通过维基共享

*本文的一个版本最初由 Monica Powell 于[www.datalogues.com](http://www.datalogues.com/)T3】发表*