# Ctrl+A 选择文本框中的所有文本

> 原文：<https://dev.to/adamkdean/ctrl-a-select-all-text-in-a-text-box-44mp>

这里还有一个快速剪辑。在不到 7 秒钟的时间内将全选(ctrl+a)添加到一个普通的文本框中，下面是方法。使用 KeyDown 事件，查找 control 和 A，然后选择 all，抑制按键以停止发出“乒乒乓乓”的噪音:

```
private void txtInput_KeyDown(object sender, KeyEventArgs e)
{
    if (e.Control && e.KeyCode == Keys.A)
    {
        txtInput.SelectAll();
        e.Handled = true;
        e.SuppressKeyPress = true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了。享受