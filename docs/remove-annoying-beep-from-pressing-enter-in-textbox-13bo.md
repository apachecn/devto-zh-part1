# 消除在文本框中按回车键时恼人的嘟嘟声

> 原文：<https://dev.to/adamkdean/remove-annoying-beep-from-pressing-enter-in-textbox-13bo>

继续有用的片段和方法，今天我向你强大的读者展示一个非常有用的简单方法来消除在文本框中按 enter 键时令人讨厌的声音。我们都遇到过这种情况，试图让 enter 把我们从用户名带到密码，然后到提交按钮..但是那个声音..必须..爪..眼睛..在外...或者耳朵..但不管怎样，这真的很简单，读一读，学一学:

```
private void txtInput_KeyUp(object sender, KeyEventArgs e)
{
    if (e.KeyCode == Keys.Enter)
    {
        e.SuppressKeyPress = true;
    }
}

private void txtInput_KeyPress(object sender, KeyPressEventArgs e)
{
    if (e.KeyChar == (char)Keys.Enter)
    {
        // any logic would go here
        e.Handled = true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了。非常容易，非常简单，非常非常有用。

对我来说，回到工作中，我的一个朋友 Mihn 昨天和我谈论重构，他不知道他已经播下了种子，这些种子会成长为巨大的果实，当我在工作中彻底检查新网站的整个管理员区域，并把它从基于 web 的应用程序改为桌面应用程序时..该死的米纳，该死的你！

*我听到 by 说成果是一个真实的词。