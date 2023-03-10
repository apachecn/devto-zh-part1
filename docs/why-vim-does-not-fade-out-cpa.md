# 为什么 Vim 没有淡出？

> 原文：<https://dev.to/mattn/why-vim-does-not-fade-out-cpa>

你有没有想过，为什么 Vim 20 多年来仍然被很多人使用，没有淡出？

# 名声在外

有些人说:

> 我不明白为什么还有人继续使用化石般的文本编辑器。

[![fossil](img/21a748167c3770b6f1a7f668a2620606.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3-ei9A38--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kbtr52l8kdcsatavil2y.jpg)

当然，Vim 是基于字符的文本编辑器。如果 Vim 和 20 年前一样，你的观点将是正确的。但是你必须知道 Vim 现在仍然在变化。

# 事实上

事实上，Vim 不停地改进了许多特性。

# 版本 5

*   添加一些图形界面，包括 Win32 图形界面
*   Python/Perl 接口
*   和许多有用的功能

## 版本 6

*   支持 UTF-8
*   竖直裂缝
*   Netrw
*   服务器-客户端
*   快速修复
*   和许多有用的功能

## 版本 7

*   拼写检查
*   支持 MzScheme
*   全方位完成
*   标签页
*   vimgrep
*   和许多有用的功能

## 版本 8

*   异步 I/O 支持、通道和作业、计时器
*   部分函数，λ/闭包函数
*   包装
*   DirectX 绘图
*   GTK+ 3 支持

最近，Vim 增加了新特性`:terminal`。可以在 Vim 中打开终端窗口。运行 shell，查看日志文件，发送电子邮件，在`:terminal`上启动 Emacs。这个特性给了我们很多可能性。

[![terminal](img/c14ae97e71bdbfeb53c4312f80d4ba91.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--x2AO0koE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k6pzmzioolqf6yjxonku.png)

在`:help design-not`中，该特性被认为是 Vim 的不良设计。但文字写于 20 年前。你能说 20 年前写的设计即使在现在也永远正确吗？Vim 的作者布莱姆·米勒决定修改文档。

> Vim 不是一个 shell 或操作系统。它提供了一个终端窗口，您可以在其中运行 shell 或调试器。例如，能够通过 ssh 连接做到这一点。但是如果你不需要文本编辑器，那就不在讨论范围之内了(用 screen 或 tmux 之类的东西代替)。

有趣的是，对:terminal 的支持包括 Windows。(仅供参考:我实现了 windows:终端，耶！)

[![terminal on Windows](img/8d9e9dc77588d8e7f1ab236ffd27c1ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4WtiPMHi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/goh9sj7nm6nb2k6i62iu.png)

您可以在 Windows GUI/CUI 上使用`:terminal`来运行 Windows 命令提示符。这个不取决于 Cygwin/msys2。只需要 winpty.dll 和 winpty-agent.exe，描述见`:help Terminal-mode`底部。您可以在 Vim 中使用一个窗口，就像它是命令提示符一样。要了解终端的更多功能，请参见`:help terminal`。

# 贡献者

你最好知道有多少贡献者给 vim-dev 发送补丁，我们正在讨论修复错误和增加新功能。比如，你知道这个 2017 年 10 月有多少次作案吗？

*   [8.0.1169:用‘list’和‘cul’高亮显示一个过多的字符](https://github.com/vim/vim/commit/5ece3e359c4b96d0f9bf1ce690223545f40f34b8)
*   [8.0.1170:使用 termdebug 导致 100%的 CPU 时间](https://github.com/vim/vim/commit/f33606112a59de6965692840b47d9cb4b924fc97)
*   [8.0.1171:弹出测试还是有点片状](https://github.com/vim/vim/commit/712549e04eddd6687c4b7654ec9af6da6c365603)
*   [8.0.1172:当给定 E734 时，选项仍被设置](https://github.com/vim/vim/commit/2a6a6c3014e728cd01c750b0f60484d4eaf22a8c)
*   [8 . 0 . 1173:CTRL-L](https://github.com/vim/vim/commit/19a3d68b2cd4beb1ee1d97a84ad4e860ffe0cbb2)后终端窗口不再重绘
*   [8.0.1174 : Mac Terminal.app 的白色不正确](https://github.com/vim/vim/commit/a0a6f2776eabfbd4d9174fabdf3921beb7348eda)
*   [8.0.1175:没有+termresponse 的构建失败](https://github.com/vim/vim/commit/c902609f69b5662484f78dbf509715940d4134e4)
*   [8.0.1176 : job_start()不能正确处理引号和反斜杠](https://github.com/vim/vim/commit/d78f03f86045184dfd191f00359baa61e2e79d1f)
*   [8.0.1177:在终端窗口中，弹出菜单未被清除](https://github.com/vim/vim/commit/54e5dbf58e16d0d757a315480c9e63ac7f8a74c7)
*   [8.0.1178:在 MS-Windows 上使用旧编译器](https://github.com/vim/vim/commit/73f4439ca690d8224df8f88dc71a43fafdcc89bf)
*   [8 . 0 . 1179:Test _ popup _ and _ window _ resize()并不总是通过](https://github.com/vim/vim/commit/631820536e4084e01bf990f9314ec385b60b21d7)
*   [8 . 0 . 1180:MS-Windows test clean 目标删除颜色脚本](https://github.com/vim/vim/commit/75f69e5a183e00cb5aa05d43c1ecf3b7dead7a98)
*   [8.0.1181:使用 Vim 命令的测试在 MS-Windows 上失败](https://github.com/vim/vim/commit/4635e11c6bd63d8be90a2d9455802c6e7bde28f5)
*   [8.0.1182:无法看到或更改 mzscheme dll 名称](https://github.com/vim/vim/commit/0ab35b279f9d64f32e99424166b047f90cb042d8)
*   [8.0.1183 : MS-Windows 构建指令已经过时](https://github.com/vim/vim/commit/18cfa940e2bbf657e8f90e6486812876198ef963)
*   [8.0.1184:未测试:marks 命令](https://github.com/vim/vim/commit/9b69f22e66d51d764e9ade87ae8a57ac13ab7348)
*   [8.0.1185 : Ruby 库包含次要版本号](https://github.com/vim/vim/commit/9202162c5c069c925b6b9bf84e546fbd362cdf46)
*   [8.0.1186:仍然有相当多的旧风格测试](https://github.com/vim/vim/commit/4a6fcf8047de13c7949ab2f27f7774acaec4ae4d)
*   [8.0.1187:在 Travis 上用 lua 建造 OSX 失败](https://github.com/vim/vim/commit/8065cf2bfba93a8aeed29f591152edeb0841bff6)
*   [8 . 0 . 1188:MS-Windows 上的 autocmd 测试失败](https://github.com/vim/vim/commit/1d68d9b2bd60d848552c08763e590edde16056c9)
*   [8.0.1189 : E172 实际上没有用](https://github.com/vim/vim/commit/6047e2c722347ae9443c2632c6e061918ccef9c6)
*   [8.0.1190:在 BufWritePre 事件](https://github.com/vim/vim/commit/2c33d7bb69c4c2c5b0e39b03cc4b0c04cfdfbb0b)中打开新窗口后不可用
*   [8.0.1191 : MS-Windows:安装程序中缺少 32 位和 64 位文件](https://github.com/vim/vim/commit/6199d43f4b59a9bb1c87d408c5b33fa19a23ebcd)
*   [8.0.1192 : MS-Windows:默认不启用终端功能](https://github.com/vim/vim/commit/ac8069bb63c5954c787fe93b7a9265de3c0c6853)
*   [8.0.1193:使用 getbufinfo()](https://github.com/vim/vim/commit/b2c8750c4e95f64a8dff912af81e13318b3f6ed6) 清除缓冲区时崩溃
*   [8.0.1194:端子的实际 fg 和 bg 颜色未知](https://github.com/vim/vim/commit/65e4c4f6868882a380c319632a1728a5e7d274ad)
*   [8.0.1195:无法在 MS-Windows 上构建](https://github.com/vim/vim/commit/9377df3ecd0bd3acb5d46cb8af7fe60867f247f2)
*   [8 . 0 . 1196:t _ RF 未设置时崩溃](https://github.com/vim/vim/commit/a20f83df1d646a2d296af835ba1154f09986f102)
*   [8.0.1197 : MS-Windows 构建指令不是最新的](https://github.com/vim/vim/commit/81b07b527e8b9c35d12fd45b63885d6938564723)
*   [8.0.1198:旧版编译器不知道 uint8_t](https://github.com/vim/vim/commit/00ce63dc230b6c8f55997b09396aee6d6d722734)
*   [8.0.1199:当“剪贴板”为“自动选择加”时，设置星形寄存器](https://github.com/vim/vim/commit/8bfe07b70878c83c838320cc6a93d62cd78b953f)
*   [8.0.1200:测试关闭铃声两次](https://github.com/vim/vim/commit/67418d97b457d2e27fe342472d3c9fd342ffc47f)
*   [8 . 0 . 1201:“yL”受到“scrolloff”的影响](https://github.com/vim/vim/commit/44cc4cf72fdd12cd9a779031d443aac4254d51ae)
*   [8.0.1202 : :wall 给出一个终端窗口的错误](https://github.com/vim/vim/commit/059db5c29ffef283a4b90bab9228708fa32e3dd2)
*   [8.0.1203:终端窗口错误处理排版字符](https://github.com/vim/vim/commit/6daeef1933be68055aabe1d55f8467d46a707753)
*   [8 . 0 . 1204:QuitPre 自动命令可能会得到错误的文件名](https://github.com/vim/vim/commit/87ffb5c1a3aa506a1be07af4e794b3753f839dc3)
*   [8.0.1205:可以卸载已更改的缓冲区](https://github.com/vim/vim/commit/ff930cad8a9100eeb04256aab1a14de993c1d7e9)
*   [8.0.1206:没有用于进入或离开命令行的 autocmd】](https://github.com/vim/vim/commit/fafcf0dd59fd9c4ef743bb333ae40d1d322b6079)
*   [8.0.1207:分析跳过第一个和最后一个脚本行](https://github.com/vim/vim/commit/67435d9983965c5c77fc74f0559779ce4554dacb)
*   [8.0.1208:“状态线”删除空组，突出显示更改](https://github.com/vim/vim/commit/6b89dbb55f84c485310c8c9e094dbafe3ecbace6)
*   [8.0.1209:仍然有太多的旧样式测试](https://github.com/vim/vim/commit/53f0c962394dc6bc66d5b0762af9434e672d1b25)
*   [8.0.1210 : CTRL-G 和 CTRL-T 被 typeahead 忽略](https://github.com/vim/vim/commit/f8e8c0643b1cd97db11912bc4f773e1328a0da02)
*   [8.0.1211:不能通过拖拽&下拉](https://github.com/vim/vim/commit/ca05aa24af88836f8aa792360b3780589f294981)来重新排序标签页
*   [8.0.1212 : MS-Windows:撕裂式菜单在 64 位上不起作用](https://github.com/vim/vim/commit/66857f410426ca335f4771a58a32b2d14a7e52b9)
*   [8.0.1213:设置“mzschemedll”没有效果](https://github.com/vim/vim/commit/2e4cb3b042faba5efc9b5a8b4f86942a417494ce)
*   [8.0.1214:当 EXITFREE 置位时访问释放的内存](https://github.com/vim/vim/commit/4f1982800f0aff28df6875e718a786f6c4b11ad9)
*   [8.0.1215:较新的 gcc 警告隐式失败](https://github.com/vim/vim/commit/2f40d129bf45cd35976e4120336ae6d504f5a5dd)
*   [8.0.1216:文件命令](https://github.com/vim/vim/commit/6ce650480844bfaa5410874416b4a2e15f40b870)的 tabline 并不总是更新
*   [8.0.1217:无法在调试模式下使用远程评估来检查变量](https://github.com/vim/vim/commit/d99388ba8535a6fecf7d0bf7b982832c0b816062)
*   [8.0.1218:写入 autocmd 中释放的内存](https://github.com/vim/vim/commit/8d84ff1a3c8cfe59399d3f675ec080066582fdb6)
*   [8.0.1219:端子测试不稳定](https://github.com/vim/vim/commit/f204e05ae9f6bc5d922d14d457e16b590efbf3e5)
*   [8.0.1220:跳过空状态行组是不正确的](https://github.com/vim/vim/commit/235dddf1f4afe3a40047dbf2aca1bd177b7be18b)
*   [8.0.1221:仍然有太多的旧样式测试](https://github.com/vim/vim/commit/15993ce9210e8b8d4bc11e1d640f6447b18d3e6c)
*   [8.0.1222:测试功能相互干扰](https://github.com/vim/vim/commit/ce11de87e26e1420703242f8e07b4fd69c4032ba)
*   [8.0.1223:使用自动完成和标签页时崩溃](https://github.com/vim/vim/commit/9ad89c6c4f89cd710d8244d8010b8b0ae30ba79d)
*   [8.0.1224:测试功能之间仍然存在干扰](https://github.com/vim/vim/commit/cf1ba35fc2ebd41b9a7738bbd1f026f5311560aa)
*   [8.0.1225:不检查拼写区域是否为零](https://github.com/vim/vim/commit/ee03b941241eae1d36bc29b84eec09116cefe7cd)
*   [8.0.1226:编辑和弹出测试失败](https://github.com/vim/vim/commit/2a45d64d0a7ab28d77eee277244e76dbbf8c2db8)
*   [8 . 0 . 1227:readfile()中未定义的左移](https://github.com/vim/vim/commit/dc1c98129484e7879bc6dbf38e523beb730988b6)
*   [8 . 0 . 1228:GUI 测试中的无效内存访问](https://github.com/vim/vim/commit/0e19fc07e73214f94441cb3a495504a1de21eb07)
*   [8 . 0 . 1229:vim _ str 2 NR()中的条件始终为真](https://github.com/vim/vim/commit/9a91c7a1f9134f799b8672a4e3844781263e8cf3)
*   [8.0.1230:可视模式下 CTRL-A 使用选择后字符](https://github.com/vim/vim/commit/ce1577502693940f1f9285cd803a075b738a2330)
*   [8.0.1231:扩展文件名删除破折号](https://github.com/vim/vim/commit/c312b8b87a589ed8452dbf0f555f05ff86d04692)
*   [8.0.1232 : MS-Windows 用户对默认映射感到困惑](https://github.com/vim/vim/commit/c3fdf7f80b2febdd8a8f7a1310631567d257d66a)
*   [8 . 0 . 1233:dos 安装程序中的打字错误](https://github.com/vim/vim/commit/b9fce6cbf7ed0a2766582c5db797ccf99a838a13)
*   [8.0.1234 : MS-Windows:合成字符未正确显示](https://github.com/vim/vim/commit/a6ce1ccf5c10baa5c2a25897c46961d751a21dda)
*   [8.0.1235:无法在大型构建中禁用终端功能](https://github.com/vim/vim/commit/ef83956e1e67736b4c6b886d897b74f022622a74)
*   8.0.1236 : Mac 功能令人困惑
*   [8.0.1237:":设置滚动&经常出错](https://github.com/vim/vim/commit/af2d20c6285c1d2973e3d9b5e8f727e3ed180493)
*   [8.0.1238:增量搜索仅显示一个匹配](https://github.com/vim/vim/commit/2e51d9a0972080b087d566608472928d5b7b35d7)
*   [8.0.1239:不能将 lambda 用于 searchpair()](https://github.com/vim/vim/commit/48570488f17e397183ea7d5c7ca67d6e4ffb013d) 的 skip 参数
*   8.0.1240 : MS-Windows: term_start()不支持环境

这包括添加功能和修复错误。你能说 Vim 的发展状态不活跃吗？Vim 将继续变化，很可能不会变成化石。