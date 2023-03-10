# unity XR(VR/AR/MR)2017 年更新

> 原文：<https://dev.to/yasei_no_otoko/unity-xrvrarmr-update-in-2017-mpg>

[本文参与了 Unity Advent Calendar 2017。](https://qiita.com/advent-calendar/2017/unity)

Unity 的 VR 功能在 2017.2 中将名称改为 XR，添加了很多功能和规格变更。
本文对从 2017.1 到本月发行的 2017.3 的功能逐一进行概要说明。

# 2017.1

Unity 2017.1 以支持. NET4.6 和追加时间线等主体的大幅功能更新为主，几乎没有 VR 的更新，但是还是有两个较大的。

### OpenVR SDK 标准添加到 PC 版 VR SDK 支持列表

在 Unity5.x 中，在 unity VR 支持中使用 OpenVR SDK 时必须手动添加。
那个从 2017.1 开始，标准在 Unity VR 支持中也追加了 OpenVR SDK。
但是，在将 Oculus Rift 添加到动作对象中时需要注意。
Unity VR 支持的标准中，Oculus SDK 的配置优先于 OpenVR SDK，所以
如果检测到 Oculus Rift，就会在 Oculus SDK 上工作。
这是以 OpenVR(Vive )为前提进行开发的软件在 Oculus Rift 上运行时，会引起面向 OpenVR SDK 的组件在 Oculus SDK 上无法工作，行为失常，无法进行等问题 为此，将 Oculus SDK 另行编入同一场景，检测用[unity engine.VR.VR settings.loaded device name](https://docs.unity3d.com/ja/540/ScriptReference/VR.VRSettings-loadedDeviceName.html)连接的 HMD 是 Oculus 还是 OpenVR HMD

### Ambisonic 音源支持

从 2017.1 开始，支持输入立体音源格式的 Ambisonic 音频格式，并使用支持 Ambisonic 解码的音频插件。
目前支持 Ambisonic 解码的主要 VR 音频 SDK 有[Oculus Audio SDK](https://developer.oculus.com/documentation/audiosdk/latest/concepts/book-audiosdk/) 和[Google Resonance Audio SDK](https://developers.google.com/resonance-audio/) 两种 因为可以独立于支持 Unity 标准音频立体声化的 Spetializer Plugin 进行选择，所以也可以与不支持 Ambisonic 解码的[Steam Audio](https://developers.google.com/resonance-audio/) 进行组合( Steam Audio )

# 2017.2

在 Unity 2017.2 中，Unity 2017.1 像谎言一样有了大幅的更新，AR 功能也通过标准支持，因此 UnityEngine.VR 被更新为 UnityEngine.XR。

### OpenVRがMac 64bit(Metal)に対応

为了配合 6 月在 WWDC 上发布的 SteamVR 和 HTC Vive 的 Mac 支持，Unity 也支持标准行为。

### 与 Vive 的模特动作对应

迄今为止，在确认 VR HMD 的描绘和动作时，必须将 HMD 安装在 PC 上进行动作。 播放 Unity 2017.2 中添加的“Mock HMD - Vive”时，即使在未连接 HMD 的情况下，也能再现与 Vive 相同的纵横比、投影矩阵、遮挡网格、FOV、纹理分辨率 编辑器的 Game View 将绘制分割的双目纹理。

### 核心支持

这是 8 月发布的 Android 的 AR 功能。 本来 Unity 2017.2 计划支持 Tango，Unity 2017.2 Beta 中也存在，但正式版改为 ARCore (通用相机)，无法用 Unity 开发 Tango (专用相机) APP。

### 遐想 ViewでのVideo 异步 Reprojetionサポート

为了在 Daydream View 中平滑地显示视频，支持 Google VR 不通过 Unity 渲染循环直接访问视频的功能。
由于可以忽略 Unity 的纹理过滤来绘制视频，因此 Daydream View 中的视频质量得到了提高。 还支持可选的 DRM 保护功能。

### Windows 混合现实 SDKのサポート

Windows Holographic 支持已得到更新，HoloLens 和 windows 混合 reality 耳机均支持 UWP 平台的 Unity VR 支持。 选择 UWP 平台后，还可以像 Oculus 和 Vive 那样通过编辑器播放来调试 windows 混合就绪耳机。

### 添加 TrackedPoseDriver 组件

到目前为止，在处理 HMD 和 6DoF 控制器的位置跟踪时，可以利用各公司的 VR SDK，或者通过[unity engine.VR.input tracking](https://docs.unity3d.com/jp/540/ScriptReference/VR.InputTracking.html)分别取得 position、rotation Unity 2017.2 中新增了 TrackedPoseDriver，其中 GameObject 是标准组件 GUI 中支持位置跟踪的设备，如头部( HMD )、右手/左手控制器和彩色照相机( AR )

### 立体 Instancingのサポート

在迄今为止的 Unity VR 支持中，有将面向 HMD 的多个面板的绘制按面板划分纹理分别进行的多遍渲染、在单一的巨大纹理上分别进行绘制来减少绘制上下文的切换的单遍渲染这两种 (参照)
在 2017.2 中，使用 DirectX11 的[RenderTarget Array Index](https://msdn.microsoft.com/en-us/library/windows/desktop/dn933226(v=vs.85).aspx?f=255&MSPPError=-2147217396) ，按照 GPU Instancing 的要领进行双目绘图的实例化的方法。 使用上有 Windows 10、DirectX11(12 也不能)、Forward 渲染专用和严格的限制。
虽然前景看好，但目前由于实验性的功能，在 Unite Austin 2017 的时候，CPU 时间从 Multi Pass 减少了 27%，Single Pass 减少了 8%，另外，全屏后效果等的应对也是严峻的数字。

### Vuforia 的支持

作为老字号的主要 AR SDK 的 Vuforia 在 Unity 中受到了标准支持。 可以从改名为 XR 支持的 PlayerSettings 的 VR 支持栏中添加。

### EditorVR 的基础支持

Unity 2017.2 实现了一些从 Unity5.x 时代开始在 EditorVR 端实现的功能，从而提高了兼容性。

### 可以在 Player Settings API 中取得和设定 VR 支持(从 p4 开始)

向 PlayerSettings 类添加了三个 Get/SetVirtualRealitySupported、Get/SetVirtualRealitySDK 和 GetAvailableVirtualRealitySDKs

# 2017.3

在 2017.3 中，与 XR 没有直接关系，但之前的网格顶点数 65k 限制一下子扩展到了 4G，可以在 Unity 中直接处理超高多边形网格和点云，也可以在 XR 中进行编辑器播放等，确实如此 关于 XR，2017.2 有很多，所以控制得很低调，但还是有比较大的东西。

### 深度缓冲 Sharingのサポート

在 Windows MR 和 Oculus 中添加了对深度缓冲器的共享。 这可以通过在 OS 和 Oculus 运行时共享 Unity 中的深度缓冲器来稳定全息焦距，或者使 Oculus Dash 前面的游戏中的对象半透明等。

### Daydream VR 的 6DoF 独立设备的对应

Daydream 一栏支持位置跟踪的一体化设备。 但是，截至撰写本文的 2017 年 12 月，Daydream 6DoF 独立设备尚未发布上市计划。 (联想正在开发这一消息在 6 月的谷歌 I/o 上公布)

### 試験的可脚本化渲染 PipelineのXRサポート

Scriptable Render Pipeline 现在也可以在 XR 上运行，以便通过 C#脚本编辑从 Unity 5.6 开始开发的 Unity 绘制管道。 Scriptable Render Pipeline 本身在[GitHub 上](https://github.com/Unity-Technologies/ScriptableRenderPipeline)正在进行开发，预计将于 2018.1 正式版。

### Mac 中的单路径支持

支持 2017.2 时不支持的单遍立体渲染，从而提高了性能。

### VRでのTerrain Treeが改善

到目前为止，在 VR 中使用 Unity 标准的 Terrain 时，billboard rotation 变得很麻烦，但这一点得到了改善，billboard 和网格绘制的过渡得到了改善，billboard 纹理也在 sort-independent 中进行了 alpha 混合

### Jitter 投影矩阵的 XR 支持

Post Processing Stack 等安装的临时抗锯齿( TAA )所需的 Jitter 投影矩阵至今不支持双目绘制，因此使用 TAA 时，左右会显示完全不同的结果，无法实用。 在 2017.3 中，Jitter 的投影矩阵支持双目绘制，可以实用化。

### 视频播放器的 360 度视频支持

通过在 2017.3 中新添加的 Skybox/Panoramic 中输入在 Unity 标准视频播放器中设定的 180/360 度视频的渲染目标，可以仅通过 Unity 标准功能播放 180/360 度视频。 (也支持并排、上下配置的立体动画)
Video Player 方面没有特别变更，像 Skybox/Panoramic 一样需要在材质方面进行应对，所以这一点需要注意。

# 2018.1 的预定

即使是明年发布的 2018.x，对 XR 的支持还将更新。 在此，在公式的[路线图](https://unity3d.com/jp/unity/roadmap)中写入 XR 相关的功能。

### AR 功能的跨平台支持

Unity 2018.1 新增了同时支持 Google ARCore/Apple ARKit 的跨平台标准 AR 组件，可以用更少的时间创建支持两者的 ar APP。

### 360 度捕获的实现

无论是在编辑器上还是在运行时，Unity 的捕获功能都可以捕获包括立体声在内的 360 度立方体贴图。 2018.1 时为外部。

# 在结尾

2017 年在 Unity 上有 3 次主要版本更新，其中都包含 XR 功能的更新。 2018 年也将不断增加功能。 利用新的便利功能和平台，快乐地进行 XR 开发吧！