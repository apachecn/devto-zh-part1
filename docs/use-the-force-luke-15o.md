# 使用原力，卢克

> 原文：<https://dev.to/terceranexus6/use-the-force-luke-15o>

[![](img/7c7874aae09dfa943dda118f3cb0ebde.png)T2】](https://i.giphy.com/media/XBMbRaUrnjXvq/giphy.gif)

计算机科学中的某些东西让我们想起了赛博朋克，当然，读心术是我发现的最具赛博朋克特色的东西之一。让我解释一下:有一个叫做 **Mindflex** 的玩具，使用 arduino 很容易破解，所以你可以读取某些脑电波，并在 C 代码中使用它们。我倾向于专攻安全...但这是很多*的乐趣*。

mindflex 的硬件有一个 EGG 芯片、一个无线电发射器和一个微控制器。 *hack* 在于使用一些焊接能力将 EGG 芯片与 arduino 连接起来，并使用 arduino 的库“大脑”,这将允许我们使用它。要做到这一点，就像移除头戴设备的塑料盖一样简单，在上面开一个洞，焊接我们需要的东西，再次盖上盖子，将电缆穿过洞，连接 arduino。在重新盖上盖子之前，我建议先试试这个装置，看看焊接是否合适。焊接应该是这样的:

[![](img/e321242bfc0503b5286912bf9bb05442.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QM3pMVyZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://frontiernerds.com/files/imagecache/half-column/t-pin-soldered.jpg)

焊接进入一个 T 标记的引脚和地面(图片不是我的，但我做了同样的事情)。一旦我们检查了硬件，我们应该开始研究大脑 arduino 库。这是一个基本的示例代码:

```
#include <Brain.h>

// Set up the brain parser, pass it the hardware serial object you want to listen on.
Brain brain(Serial);

void setup() {
        // Start the hardware serial.
        Serial.begin(9600);
}

void loop() {
        // Expect packets about once per second.
        // The .readCSV() function returns a string (well, char*) listing the most recent brain data, in the following format:
        // "signal strength, attention, meditation, delta, theta, low alpha, high alpha, low beta, high beta, low gamma, high gamma"   
        if (brain.update()) {
                Serial.println(brain.readCSV());
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/1fe6366d7ae5101e853a2439068e0872.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jTAD0hxB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.giphy.com/media/UsmcxQeK7BRBK/giphy.webp)

这将基本上显示不同的脑波读取的数据，打印在序列中。mindflex 基本上读取浓度(用尤达声音读取)*如果你身上有强大的力量*，这是你应该注意的措施:

**(1-3Hz):睡眠
**θ**(4-7Hz):放松、沉思
**低阿尔法** (8-9Hz):闭眼、放松
**高阿尔法** (10-12Hz)
**低贝塔** (13-17Hz):警觉、专注
**高贝塔** (18-30Hz)** 

 ****但这毕竟是个玩具，并不能*完美地*工作。我正在尝试混合使用 arduino、python、bash 和 openssl，让它在算法和其他令人讨厌的东西中随机工作。但在未来，它可能有助于假肢运动或虚拟现实控制。未来似乎很*赛博朋克*！****