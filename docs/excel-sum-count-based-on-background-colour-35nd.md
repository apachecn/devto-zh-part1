# 基于背景颜色的 Excel 汇总/计数

> 原文：<https://dev.to/adamkdean/excel-sum-count-based-on-background-colour-35nd>

今天我不得不使用 VB。距离上次接触已经七八年了，今天只是为了在 Excel 中简单完成一个解法。我想念我的花括号。

有人问我如何根据单元格的背景颜色计算出一个`SUM()`，看起来没有任何内置的东西可以做到这一点。我的 [StackOverflow 问题](http://stackoverflow.com/questions/17829816/how-can-i-find-sum-of-highlighted-cells)并没有带来任何好的想法，所以看起来 VBA 的剧本是我唯一的选择。

[![Example](img/109f28c4c7b198581d0cb13cfe631fce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--M0L9Dz0g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.stack.imgur.com/Ii27n.png)

下面的代码片段——通过按 Alt + F11 插入，然后转到 Insert 和 Module——将基于第一个参数中传递的单元格的背景颜色进行`SUM()`,第二个参数是范围:

```
Function SUMCLR(rColor As Range, rRange As Range)
    Dim rCell As Range
    Dim lCol As Long
    Dim vResult
    lCol = rColor.Interior.ColorIndex
    For Each rCell In rRange
        If rCell.Interior.ColorIndex = lCol Then
            vResult = WorksheetFunction.SUM(rCell, vResult)
        End If
    Next rCell
    SUMCLR = vResult
End Function 
```

Enter fullscreen mode Exit fullscreen mode

下面的代码片段执行计数，而不是求和运算:

```
Function COUNTCLR(rColor As Range, rRange As Range)
    Dim rCell As Range
    Dim lCol As Long
    Dim vResult
    lCol = rColor.Interior.ColorIndex
    For Each rCell In rRange
        If rCell.Interior.ColorIndex = lCol Then
            vResult = 1 + vResult
        End If
    Next rCell
    COUNTCLR = vResult
End Function 
```

Enter fullscreen mode Exit fullscreen mode

然后在单元格中，只需使用公式，例如`=SUMCLR(A1, B1:B10)`和`=COUNTCLR(A1, B1:B10)`。