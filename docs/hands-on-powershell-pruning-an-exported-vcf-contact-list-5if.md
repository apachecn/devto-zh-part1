# Powershell 实践:修剪导出的 VCF 联系人列表

> 原文：<https://dev.to/brthanmathwoag/hands-on-powershell-pruning-an-exported-vcf-contact-list-5if>

最近，当我在山里徒步旅行时，我遇到了雨云，全身湿透了。当我最终到达阿尔卑斯山小屋时，我发现我手机的电源开关不再工作了。它可以正常开机，但是一旦屏幕关闭，就不可能再打开了。幸运的是，在我寻找新手机的时候，这只够备份所有数据。

当新手机收到邮件，我正要导入联系人列表时，我想起我一直想清理一下。你看，当我设置好之前的手机时，我登录了我的谷歌账户，这导致了整个 Gmail 地址簿的下载。谢谢，我想是谷歌，但我甚至不用手机收发邮件。一些电子邮件被附加到现有的联系人中，但相当多的不匹配，导致一些人的重复条目，以及大约 10 年前我只发送过一次消息的个人和公司的垃圾条目。

所以我想，这将是一个摆脱他们的好机会。

我查看了导出的联系人列表，幸运的是，它只是一个带有串联的 [vCards](https://en.wikipedia.org/wiki/VCard) 的平面文本文件。这是它的样子:

```
BEGIN:VCARD
VERSION:2.1
N:;Lastname;Firstname;;
FN:Nickname
TEL;CELL;PREF:123456789
END:VCARD
BEGIN:VCARD
VERSION:2.1
N:Surname;Firstname;;;
FN:Firstname Surname
EMAIL;PREF:email@test.com
PHOTO;ENCODING=BASE64;JPEG:/9j/4AAQSkZJRgABAQAAAQABAAD/2wCEAAUDBAoJCAsJCQk
 ...
 LS5apuBYSw02xdyPEsRPJxMByx33iEoQZdohkFlugEIHljtYiFfNHtnp/9k=

END:VCARD
BEGIN:VCARD
VERSION:2.1
N:;Nickname1;;;
FN:Nickname1
TEL;CELL;PREF:456789123
END:VCARD
BEGIN:VCARD
VERSION:2.1
N:;Nickname2;;;
FN:Nickname2
TEL;CELL;PREF:789123456
END:VCARD

etc... 
```

Enter fullscreen mode Exit fullscreen mode

所以我需要做的是将文本分割成单独的 vCard 记录，然后过滤掉那些不包含以`TEL;`开头的行的记录。

在 Powershell 中，这可以通过:
来实现

```
$in = 'unfiltered.vcf'
$out = 'filtered.vcf'

Get-Content $in `
    | Out-String `
    | Select-String -pattern '(?s)BEGIN:VCARD.*?END:VCARD' -AllMatches `
    | % { $_.Matches} `
    | % { $_.Value } `
    | ? { $_.Contains("`nTEL;") } `
    | Out-File -Encoding ascii $out 
```

Enter fullscreen mode Exit fullscreen mode

(请原谅我的突出显示，目前 pandoc 中没有 powershell 荧光笔，即将发布的版本将有[可加载语言定义](https://github.com/jgm/pandoc/issues/3334)，所以我将能够使用 [m0t0k1 的 kate-powershell](https://github.com/m0t0k1/kate-powershell)

这里有一些有趣的部分。因为`Select-String`不能匹配管道上的多个元素，我们必须在`Get-Content`输出上使用`Out-String`,这样整个文件作为一个字符串处理，而不是逐行处理。`(?s)`支持多行模式匹配，就像传统 perl regexp 中的 [`/s`修饰符一样。还因为我们正在处理一个巨大的字符串，所以必须设置`-AllMatches`，以便 Powershell 在找到第一个匹配后不会停止——类似于 perl 中的`/g`。最后，我们需要显式地在`Out-File`上设置编码。否则，它将与 BOM 一起保存在 UTF-16LE 中，这至少在我的手机上造成了一个一般的`Could not import contacts`错误。](https://perldoc.perl.org/perlre.html#Modifiers)

现在，上面的代码在我的机器上运行了一秒钟，就为了一个 200 KB 左右的文件，但是如果你的联系人列表包含很多 base64 编码的照片，一次删除整个文件并对其应用 regex 可能不是最好的主意。相反，我们可以逐行读取文件，并手动缓冲它们:

```
$in = 'unfiltered.vcf'
$out = 'filtered.vcf'

$curr = @()
Get-Content $in | % {
    $curr += $_
    if ($_.StartsWith("TEL;")) {
        $has_tel = $true
    }
    if ($_ -eq 'END:VCARD') {
        if ($has_tel) {
            $curr
        }
        $curr = @()
        $has_tel = $false
    }
} | Out-File -Encoding ascii $out 
```

Enter fullscreen mode Exit fullscreen mode

不太聪明，时间更长，但可能伸缩性更好。

*本帖原载于[blog . tznvy . eu](https://blog.tznvy.eu/2017-06-09-pruning-exported-vcf-contact-list)T3】*