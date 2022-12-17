# 使用 GoogleVR 包中的 GvrEditorEmulator 预置，不要向下下沉摄像机，直到父对象在 Unity 编辑器上居中

> 原文：<https://dev.to/mutatedbread/using-gvreditoremulator-prefab-from-googlevr-package-and-not-sinking-the-camera-downward-until-the-parent-center-on-unity-editor-9m>

如果你正在制作一款针对**纸板**或**白日梦**平台的游戏。你可能需要将**的 GvrEditorEmulator 预置**和**的 GoogleVR 包**一起放到场景中，并**将一个摄像机**与之配对。

* * *

假设这是你设置场景的方式:

1.  你必须在一个游戏对象父对象中子摄像机。
2.  父对象有一个形状。
3.  **摄像机**应该在**偏离母体**中心的母体“头部”。
4.  您已将 GvrEditorEmulator 与该摄像机配对并启用。

* * *

好的一面是，你可以通过点击`Alt + mouse moving around`进行 360 度全景拍摄。

然而，**相机似乎正在向下下沉**。

GvrEditorEmulator 脚本总是试图将摄像机的本地位置(相对于父对象的位置)默认为 Vector3。零即(0，0，0)。

* * *

为了防止这种情况:

```
// line 90
m_camera.transform.localPosition = neck;

// line 103
m_camera.transform.localPosition = Vector3.Zero; 
```

Enter fullscreen mode Exit fullscreen mode

用你最喜欢的 IDE 打开 GvrEditorEmulator 脚本，注释掉第 90 行和第 103 行。

这将防止相机从预定的 y 位置向下下沉。