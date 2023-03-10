# 片段之间的统一

> 原文：<https://dev.to/octi/unity-dotween-snippets-5p1>

*   导入库。

```
using DG.Tweening; 
```

Enter fullscreen mode Exit fullscreen mode

*   延迟行动。

```
DOVirtual.DelayedCall (0.5f, ()=>Foo());

DOVirtual.DelayedCall (0.5f, ()=>Debug.Log("called after 0.5 sec.")); 
```

Enter fullscreen mode Exit fullscreen mode

*   闪烁 UGUI 图像。

```
Image image = this.gameObject.GetComponent<Image> ();

DOTween.ToAlpha (
    () => image.color,
    color => image.color = color,
    0.2f,  // target alpha.
    2.0f   // time.
).SetLoops (-1, LoopType.Yoyo).SetEase (Ease.InOutCirc); 
```

Enter fullscreen mode Exit fullscreen mode

*   无限循环 UGUI 对象。

```
RectTransform rt = progressObj.transform as RectTransform;
rt.DORotate (new Vector3 (0.0f, 0.0f, -360.0f), 1.0f, RotateMode.LocalAxisAdd).SetLoops (-1, LoopType.Restart).SetEase(Ease.Linear); 
```

Enter fullscreen mode Exit fullscreen mode

*   让自己的参数和变量 tween (.to )。

```
DOTween.To(
  () => GetSomeFloat(),
  (x) => SetSomeFloat(x),
  TargetSomeFloatValue,
  TweenDurationTime
); 
```

Enter fullscreen mode Exit fullscreen mode