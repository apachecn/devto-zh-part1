# 真正的跨平台。NET MIDI API

> 原文：<https://dev.to/atsushieno/managed-midi-the-truly-cross-platform-net-midi-api-56hk>

我有一个(令人惊讶的)长期项目，名为[“managed-midi”](https://github.com/atsushieno/managed-midi)，它将提供跨平台 C#/。NET MIDI 访问 API。我这里的“跨平台”不是营销术语；它的目标是 Linux (ALSA)、macOS (CoreMIDI)和 Windows (WinMM)，以及 Android、iOS 和 UWP。

实际上，我的 managed-midi 项目不是关于跨平台的好处。这不仅是一个结构化的音乐作品，也是信息传递的基础。这是我开发这个库的主要动机。

但是我要讨论跨平台部分。

没有任何现有的努力来提供 Midi 访问 API 吗？我唯一能想到的项目就是 [NAudio](https://github.com/naudio/NAudio) ，只关心 Windows。我的主要桌面是 Linux 上的 GNOME，所以不能用。这是 C#开发者生态系统中一个典型的巨大问题，他们只关心 Windows。

音频和音乐库总是那样，很少有人对跨平台和平台特定的开发感兴趣。这种开发情况类似于开发“诱饵-开关”PCL，但就像我一般不关心 Mac 和 UWP 一样，人们不关心他们不使用的平台。

具有讽刺意味的是，MIDI 特性被归入特定平台的 API 组中，而 MIDI 本身被设计成与设备无关。

然而，C++世界的情况正在发生变化。VST 现在也可以在 Linux 上使用了(我指的是斯坦伯格的原始库)。我们看到新一代的 Daw 在 Linux 上工作(Renoise，Bitwig Studio，Tracktion WaveForm 等。).

## 环绕跨平台 MIDI API

早在 2009 年，当我开始在我脑海中的其他小项目中启动这个项目时(当时这个项目甚至没有名称和 repo)，我发现了 [PortMIDI](http://portmedia.sourceforge.net/) ，这是一个支持 Windows、Mac 和 Linux 的跨平台 MIDI 库，我觉得它很酷。我不想处理特定于平台的 MIDI APIs，尤其是我不熟悉的东西。相反，我围绕它编写了一个 P/Invoke 包装器和 OO 风格的 API。我认为我的想法很好(我仍然有点)-快速得到我们需要的东西很重要，对吗？那些聪明的本地开发人员处理平台细节，我利用了结果。

这就是这个图书馆的开端。我只是想要一个 SMF 解析器和一个播放 MIDI 歌曲的 MIDI 播放器，我可以使用我的音乐宏语言编译器 [mugene](https://github.com/atsushieno/mugene) 来创作这些歌曲。甚至没有适当的抽象。

然而，让基于这个库的应用程序工作起来有点麻烦。我的初选。NET 运行时一直都是 Mono，我当时主要在 Windows 上。我不太关心 Linux。由于 portmidi 是一个纯粹的第三方库，它总是需要二进制共享库。在 Windows 上 portmidi 对我来说很痛苦，因为它需要 JNI 头文件来构建。它必须以 32 位形式提供给 Mono(当时在 Windows 上只支持 32 位 x86 ), 32 位二进制不能工作。由于某种原因. NET 框架。我不熟悉他们。我几乎放弃了自己提供二进制文件的想法。

几年后，我找到了 [RtMidi](https://github.com/thestk/rtmidi) 而且更简单。不幸的是，它只是 C++，所以我为它写了 C wrapper(我[最终为这个项目贡献了](https://github.com/thestk/rtmidi/commit/a5c375c7))。它也有和 portmidi 一样的问题，但是不需要 JNI 的东西，所以构建二进制文件不是很痛苦。此外，发展是积极的(在那一刻)。

仍然没有适当的抽象，但它是隐式完成的。抽象只作为`MidiPlayer`类完成(然后我有了`PortMidiPlayer`和`RtMidiPlayer`)。我不太在乎那个。

## MIDI API 抽象

我开发托管 midi 的主要原因是编写可用的 MIDI 应用程序。所以不一定是 C#。还有另一种努力是把 MIDI 带到跨平台的世界——Web MIDI API。它有望在网络浏览器上运行，但目前只有 Chrome 支持它。

早期的 Web MIDI 支持对于 Linux 软件合成器来说实际上并不太好(Chrome 最初只支持 Mac，软件合成器需要特殊的权限才能启用)，但它的 API 结构提供了丰富的信息。它给了我一些基本的想法，告诉我我们可以/应该在任何平台上提供什么，不提供什么。

因此，我开始重构整个 API 结构。与以前的 C 库相比，Web MIDI API 的好处在于它是一种面向对象的风格，更接近 C#。

因此，当前的 API 结构如下:

*   `IMidiAccess`
*   `IMidiPortDetails`
*   `IMidiPort`
    *   `IMidiInput`
    *   `IMidiOutput`

MIDI 信息被表示为字节数组。它与 Web MIDI 和 RtMidi 相同(portmidi 不同，有自己的消息类型)。

## 实现直接访问原生 API

即使使用 RtMidi，我也不愿意为 Windows(我已经将我的主要桌面切换到 Linux)和 Mac(我讨厌苹果对 Adobe Flash 的态度，因为这个原因我抛弃了我的 iPhone 并切换到 Android)构建那些库，特别是因为我必须提供我的包含 C API 的自定义库。

抽象出 API 后，我开始考虑可以实现特定于平台的实现，并切换每个平台的默认实现，这样我就不必再费心构建原生二进制文件了。

尽管我面临的第一个问题是 PCL 中没有 P/Invoke 可用性。我很快想到它不适合 PCL。但是还有另一个技术趋势。NET: PCL 的“诱饵和开关”技术，这使得提供一个具有特定平台实现的公共 API 成为可能。从技术上讲，这个问题似乎已经解决了。

第一个本地实现目标是 Windows。这种状态下的 Windows 意味着:Windows 桌面和 UWP(我不关心 WinRT，没有官方的 MIDI API，那些用户习惯于被限制他们的能力)。当时，我想要的是让我的可视 MIDI 播放器 [xmdsp](https://github.com/atsushieno/xmdsp) 在 Windows、Mac 和 Linux 上无缝工作，并且它是在基于 WPF 和桌面的 [xwt](https://github.com/mono/xwt) 之上。NET 框架。因此 WinMM 很重要。WinMM 很简单。UWP 是更有组织的 API，包装它很容易，但因为我没有任何管理 midi 应用程序，它是完全未经测试的。

CoreMidi 是不同的。它甚至不是从桌面开始的。我实际上想的是让托管 midi 在 Android 上工作(这甚至不是针对 Android MIDI API——这是针对我的 [fluidsynth-midi-service](https://github.com/atsushieno/fluidsynth-midi-service/) 项目，但在这里讨论这个太长了)。无论如何，手机在那个时候出现了。Android MIDI API 的类名很糟糕，但是包装起来很容易。

当我为 iOS 编写 CoreMidi 实现时，我没有任何在 iOS 上运行的托管 Midi 应用程序，所以我甚至没有尝试过。即使在 Mac 上，我也只试过一次 Xamarin。Mac 开始与 Xwt 和 CoreMidi 一起工作(有一个复杂的库解析 bug，我不得不等待 2017 年的修复)。就 API 而言，CoreMidi 比其他的要复杂一点，因为它们有端点的概念。

和...最后是 ALSA。我不想在 ALSA 上工作，因为 rtmidi 只是工作，构建 rtmidi 总是很容易，而 ALSA 很复杂。但是到处复制 librtmidi.so 是混乱的(就像[这一期](https://github.com/atsushieno/managed-midi/issues/8))而且只要我依赖 rtmidi 我就不能解决一些奇怪的[问题，比如这个](https://github.com/atsushieno/managed-midi/issues/1)，所以我最终学习了 ALSA，并将其实现为 [alsa-sharp](https://github.com/atsushieno/alsa-sharp/) 。

## 根本问题出在“偷梁换柱”的把戏上。净标准

在这种状态下，managed-midi 缺少很多像随处输入支持这样的特性(令人惊讶？我还不需要它)和设备连接状态检测器(它是 Web MIDI API 的一部分，所以也应该被实现)。我只关心输出(我的主要目标是得到一个 MIDI 播放器)。设备检测在 ALSA 上很复杂，因为它必须在 ALSA 之外进行，可能 WinMM 也不提供这个功能。

NuGet 打包是另一个问题——虽然我打算构建一个跨平台的库，但它不是“诱饵-开关”PCL 或。NET 标准库。原因如下:Xamarin。Mac full profile 需要自己的`ProjectTypeGuids`，也就是说基本不同于。NET Framework 桌面配置文件。它们必须是不同的。我不能依赖 Xamarin。因为我不在 Mac 上(即使我有 Mac，也不应该要求任何人在 Mac 上工作)并且整个桌面库不能是 Xamarin。Mac 特定的(见[这篇文章](https://medium.com/@donblas/xamarin-mac-and-netstandard2-708a06890302)中的 PCL/NuGet 目标名字)。

因此，对于同一个 netstandard 名字对象(net461)，托管 midi 有两个不同的程序集。这使得不可能包装任何一个。因为没有理由偏爱 Xamarin。对于 net461 的 Mac 专用汇编，基本忽略。

这很了不起。NET 标准设计者应该解决。