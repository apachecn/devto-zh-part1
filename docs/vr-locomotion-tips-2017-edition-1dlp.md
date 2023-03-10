# 虚拟现实运动技巧，2017 年版

> 原文：<https://dev.to/homerdalords/vr-locomotion-tips-2017-edition-1dlp>

*原发于我的博客[此处](https://guendeli.github.io/blog/VR-Motions/)T3】*

对于主流客户手中只有 2 年历史的媒体，我们可以说虚拟现实的用户交互和体验发展非常快。更多的是在游戏中引入 AAA 工作室，比如 Bethesda。

让我们挑选一些 2017 年 UX 的想法，并将它们添加到更经典的运动方法中。

受 Windows MR's Cliffhouse(虚拟现实菜单)和 Bethesda's Doom/Fallout 虚拟现实启发的设计理念。

## **先决条件:**

回购可用[这里](https://github.com/Guendeli/GearVr-motion-plus)
最重要的是，我们将使用 Oculus GearVR 进行这些，它应该可以毫无争议地用于 Rift，因为它使用的是 Oculus Utilitie 的 CameraRig，并且可以很容易地为 Daydream 和/或 SteamVR 重写，因为大多数交互都在 [ArcTeleporter.cs](https://github.com/Guendeli/GearVr-motion-plus/blob/master/Assets/Scripts/Locomotion/SharedCode/ArcTeleporter.cs) 中。

## **里面是什么？:**

我们的主要定位方法将是弧形(也称为 Bezier)传送点，让我们使用手动跟踪控制器指向我们要去的地方。两个经典的运动显然是瞬移和冲刺。然后我们会看看我们可以添加什么来使它们变得更好。但首先，让我们解决一个关于曲线的无聊部分。

## **贝塞尔曲线**

二次贝塞尔曲线是实现弧形传送器最简单的方法，另一种是抛物线。它由三个点组成:起点/终点和一个控制点。和两条线段:起点和控制点之间的第一条“S0 ”,以及控制点和终点之间的第二条“S1”。

[![altText](img/f0cb55075e577fc8491f818bd6e30eef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X6fCFKAG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/8MtqOGU.png)

两个附加点 Q0 和 Q1 将通过沿着 S0 和 S1 用范围在 0 和 1 之间的某个值 t 进行插值来获得。另一段 S2 将通过连接 Q0 和 Q1 创建，然后通过沿此段插值，点“Q2”将是创建我们的曲线的结果。

[![altText](img/73fc65fdc6d0ddae9e4ae3f3987a2b24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eis18WU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://scontent.fmad3-7.fna.fbcdn.net/v/t39.2365-6/22879649_542336362777632_507106561005453312_n.gif%3Foh%3D5b6c3b1502c70f2a6a26ac8915ce68e3%26oe%3D5AF2D279)

这可以用下面的代码片段来表达:

```
Vector3 SampleCurve(Vector3 start, Vector3 end, Vector3 control, float t) {
      // Interpolate along line S0: control - start;
      Vector3 Q0 = Lerp(start, control, t);
      // Interpolate along line S1: S1 = end - control;
      Vector3 Q1 = Lerp(control, end, t);
      // Interpolate along line S2: Q1 - Q0
      Vector3 Q2 = Lerp(Q0, Q1, t)
      return Q2; // Q2 is a point on the curve at time t
  } 
```

Enter fullscreen mode Exit fullscreen mode

## **瞬移**

在大多数早期虚拟现实体验中，这种方法第一次被作为无晕动病的可行移动方法采用，无论是淡入/淡出还是立即。通过指向静态传送点或者——我们正在使用的——弧形传送点。

***side note/History:***在 VR 中，开发者将摄像机的完全控制权交给了玩家的...内克，这是交易的一部分。但是为了 **[可访问性](http://gameaccessibilityguidelines.com/)** ，一些支持坐式 vr 游戏的方法是必要的。Oculus 开始要求使用操纵杆进行“快速旋转”，作为他们游戏的最佳实践，因此这在今天变得很普遍。这就引出了我们的问题:移动 VR 控制器只有一个触发器和一个触摸板。

微软在他们的 cliffhouse 菜单中实现了这一点，让用户在传送时校准它的旋转方向。一个适度的实现如下所示:

> [贝塞尔传送](//imgur.com/szl4Y)

```
//......SOME CODE.......

// Unity Methods
void Update () {
        if (!HasController) {
            return; 
        }
        bool currentTriggerState = OVRInput.Get (OVRInput.Button.PrimaryIndexTrigger);

        // If the trigger was released this frame
        if (lastTriggerState && !currentTriggerState) {
            Vector3 forward = objectToMove.forward;
            Vector3 up = Vector3.up;

            // If there is a valid raycast
            if (arcRaycaster!= null && arcRaycaster.MakingContact) {
                if (objectToMove != null) {
                    if (teleportedUpAxis == UpDirection.TargetNormal) {
                        up = arcRaycaster.Normal;
                    }
          objectToMove.position = arcRaycaster.HitPoint + up * height;
                }
            }

            if (OVRInput.Get (OVRInput.Touch.PrimaryTouchpad)) {
                forward = TouchpadDirection;
            }
      objectToMove.rotation = Quaternion.LookRotation(forward, up);
        }
        lastTriggerState = currentTriggerState;
}

// Helper Methods
Matrix4x4 ControllerToWorldMatrix {
        get {
            if (!HasController) {
                return Matrix4x4.identity;
            }

            Matrix4x4 localToWorld = arcRaycaster.trackingSpace.localToWorldMatrix;

            Quaternion orientation = OVRInput.GetLocalControllerRotation(Controller);
            Vector3 position = OVRInput.GetLocalControllerPosition (Controller);

            Matrix4x4 local = Matrix4x4.TRS (position, orientation, Vector3.one);

            Matrix4x4 world = local * localToWorld;

            return world;
        }
    }

Vector3 TouchpadDirection {
        get {
            Vector2 touch = OVRInput.Get(OVRInput.Axis2D.PrimaryTouchpad);
            Vector3 forward = new Vector3 (touch.x, 0.0f, touch.y).normalized;
            forward = ControllerToWorldMatrix.MultiplyVector (forward);
            forward = Vector3.ProjectOnPlane (forward, Vector3.up);
            return forward.normalized;
        }
    }

  //...........MORE CODE............... 
```

Enter fullscreen mode Exit fullscreen mode

## **突进瞬移:**

对于动作快节奏的游戏/体验来说非常酷，dash 给用户一种力量感，在 DOOM VR(以及平面屏幕 DOOM 游戏 btw)中设计得很好，但如果实施不当会导致疾病。
这里的罪魁祸首是，虽然如果你坐在沙发上看电视，它可能会很宽，但如果你在走路、跑步或专注于面前的任务，它会变小。这让我们想到了隧道效应*。*

 *> [查看 imgur.com 的帖子](//imgur.com/u9h5xWY)

实现使用了一个附加到主摄像机的 Quad/Plane/UIPanel 子设备上的遮罩。然后通过调整阿尔法或规模，它创造了一个很酷的晕影效果，减少移动时的 FOV。在 photoshop 中，只需点击 3 次，就可以创建一个蒙版样本，如下所示:

[![altText](img/18ba0f0aed0c741f4d66341b296a6a85.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hfoJvY8P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fM3NHuu.png)

**结论:**

> 对于 R&D 和实验来说，虚拟现实仍然是一个伟大的领域，今天被接受为良好实践的东西肯定会成为明天的错误。所以不要认为这些是理所当然的，你可以随意试验、调整和测试(很多)。*