# 当软件中存在阶级歧视时，使用 React Native 设置 iOS 与 Android

> 原文：<https://dev.to/sarachicad/when-classism-is-in-software-setting-up-ios-vs-android-with-react-native>

技术将会反映出建造它的人们的价值观。这是我最近在尝试为 React Native 设置 Android 模拟器时遇到的事情，我可能会说这是地狱。作为一名记者出身的开发者，我对技术的一个想法是，在这个世界上长大的人是多么的教条。人们实际上对他们的语言或偏好很虔诚，这对我来说很奇怪，因为我是从新闻和媒体来到这个世界的，在这里你可以在任何给定的一天使用不同的工具来完成相同的目标。随着时间的推移，这种态度影响了技术本身，第一次尝试设置 Android 模拟器就是一个很好的例子。
和大多数开发者一样，我知道我有 Mac 笔记本电脑。所以当我开始用 React Native 开发时，使用 XCode 模拟器是我的第二天性。当我需要查看代码在 Android 设备上的外观时，我遇到了一系列问题，这些问题凸显了技术创造者和使用者之间的巨大差异。
大约 82%的人[使用](https://www.theverge.com/2017/2/16/14634656/android-ios-market-share-blackberry-2016) Android，只有大约 18%的人使用 [iOS](https://www.theverge.com/2017/2/16/14634656/android-ios-market-share-blackberry-2016) ，然而建立一个 Android 模拟器是一场噩梦。想想看:试图为绝大多数用户制造技术比为一小部分用户制造技术有更多的障碍。在一个痴迷于用户体验的行业，这毫无意义——除非你想一想是谁在制造技术(能买得起苹果产品的高收入人群)，以及他们使用技术的体验，还是他们在为谁制造技术(赚钱少而购买安卓产品的人群)。
在模拟器中查看 React 原生项目就像 CLI 命令一样简单。在模拟器中看到一个 Android 项目需要安装带有特殊说明的 Android Studio，确保你的 Android 文件夹和机器上有一堆文件，最终放弃并安装 [Genymotion](https://www.genymotion.com/fun-zone/) 。克里斯·盖尔曼:没有你，我不可能做到。
那么有什么意义呢？关键是我们必须超越我们的体验去思考，至少在我们开发技术的时候，要试着考虑我们用户的体验。否则你的设备[将不会对黑皮肤](https://www.usatoday.com/videos/tech/2015/05/01/26678643/)或纹身的人起作用，你将构建完全忽视女性的[健康应用](https://techcrunch.com/2015/06/09/apple-stops-ignoring-womens-health-with-ios-9-healthkit-update-now-featuring-period-tracking/)，或者你将把黑皮肤的人[标记为大猩猩](https://blogs.wsj.com/digits/2015/07/01/google-mistakenly-tags-black-people-as-gorillas-showing-limits-of-algorithms/)。这件事很重要，因为我们是不完美的存在，所以我们建造不完美的东西，如果你不停下来想一想，你会把它搞砸的。