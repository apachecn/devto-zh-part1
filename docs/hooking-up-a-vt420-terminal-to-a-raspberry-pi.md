# 将 VT420 终端连接到树莓派

> 原文：<https://dev.to/dvdmuckle/hooking-up-a-vt420-terminal-to-a-raspberry-pi>

几个月前，我请一个朋友帮助我做一个项目，为用户提供一个公共的 SSH”终端，它将采用壁挂式笔记本电脑的形式，安装一个简单的 Linux 和一个提示用户输入 SSH 凭证的脚本。嗯，快进几个月，我发现了物理串行终端的魔力，以及它们仍然可以用于现代机器的事实。我还有一个没有被使用的 Raspberry Pi，所以我把两个放在一起，开始使用 Pi 构建一个公共 SSH 终端。

我的第一个挑战是试图找到一个好的终端。大多数终端(如果不是全部的话)可以使用标准的 DB9 串行连接，这可以归结为 USB。我在两个选项之间挣扎，主要是“更传统的多功能一体机选项，或者更像现代 PC 的东西。

> 我应该购买哪个终端？请记住，一个看起来比另一个多 200 美元。[pic.twitter.com/y0FfivHgHt](https://t.co/y0FfivHgHt)
> 
> –大卫·马科尔([@ DVD Muckle](https://dev.to/dvdmuckle))[2016 年 9 月 15 日](https://twitter.com/dvdmuckle/status/776480068671594496)

最后，我选择了 VT420，因为我喜欢琥珀色的显示器，我还找到了将它连接到 Pi 的说明。我还发现我需要一个适配器和一根 12 月 MMJ 格式的零调制解调器电缆。我从[这里](http://www.pacificcable.com/)两个都拿了。零调制解调器电缆类似于电话线，而适配器是你必须自己焊接的东西。因为我的焊接技术不如以前，我最终烧伤了我的拇指，放弃了焊接适配器，而是从[这里](http://www.ebay.com/itm/DEC-H8571-J-DB9-Female-To-MMJ-Female-Adapter-9-PIN-SERIAL-TO-6-PIN-DEC423-MMJ-/262196697200?hash=item3d0c241870:g:8BEAAOSwo3pWct8P)订购了一个 OEM。注意仍然需要零调制解调器电缆将适配器连接到终端。拿到这些部件后，我就可以用串口把终端连接到电脑上了。

> 尼亚哈！它还活着！[pic.twitter.com/tFwjnB8NaD](https://t.co/tFwjnB8NaD)
> 
> –大卫·马科尔([@ DVD Muckle](https://dev.to/dvdmuckle))[2016 年 10 月 1 日](https://twitter.com/dvdmuckle/status/782257576310288385)

现在，我有一个选择。我可以使用 Pi 上的 GPIO 引脚来获得一个串行端口，或者使用一个串行转 USB 适配器。我两样都点了，想看看一个会不会比另一个好。最终的结果是 USB 适配器更容易配置，也更可靠，因为许多人[在/r/retrobattlestations](https://www.reddit.com/r/retrobattlestations/comments/55wnkl/question_serial_console_on_a_rpi_1b_wraspbian/) 上附和。我订购了一个带 PL2303 芯片的 USB 适配器。不惜一切代价避免 CH340/HL340，它会导致终端上的文字损坏，被广泛认为是“易贝垃圾”

这是事情发生的地方…不像记录的那样。大多数在串行终端上获得 tty 的文档都使用 sysvinit，特别是 inittab 文件。Raspbian Jessie 使用 systemd，所以它没有 inittab 文件。然而，如果我们让它成为一个系统服务，我们仍然可以得到一个串行终端。将框架文件`/lib/systemd/system/serial-getty@.service`复制到`/etc/systemd/system/`，并根据需要重新命名。如果你使用的是 USB 转串行适配器，将其重命名为`serial-getty@ttyUSB0.service`。它被重命名为什么并不重要，但它有助于易用性。接下来，编辑框架文件中的`ExecStart=`行以使用您的终端。我的看起来像`ExecStart=-/sbin/agetty -a sshuser -p ttyUSB0 19200 vt420`。你可能需要在 ttyUSB0 前面加一个-L。注意波特率设置为 19200。这在你的终端上必须是相同的。我使用[中的设置来配置我的 VT420。用`sudo systemctl daemon-reload && sudo systemctl start serial-getty@ttyUSB0.service`启用 systemd 服务。](http://buzzdavidson.com/?p=425)

最后，我将 SSH 脚本复制到我的机器上，并正确地配置了一切。如果你想使用这个脚本，你可以在[这里](https://github.com/dvdmuckle/SSHThinClient)找到它和它的说明。

这就是你要的，一个公共的 SSH 终端。在可预见的未来，或者至少只要我还是克拉克大学的学生，我就会坐在校园里计算机科学系的休息室里。任何问题都可以直接到我的推特或者下面的评论。再次感谢 ColtonDRG 对 SSH 脚本的帮助，没有他，这个项目可能不会发生。

帖子[将 VT420 终端连接到树莓派](https://dvdmuckle.xyz/index.php/2016/10/25/hooking-up-a-vt420-terminal-to-a-raspberry-pi/)最早出现在 [dvdmuckle](https://dvdmuckle.xyz) 上。