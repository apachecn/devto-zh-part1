# macOS 中的 ForceTouch

> 原文：<https://dev.to/eonist/forcetouch-in-macos>

以下是在 macOS 中处理 ForceTouch 时的一些便利片段:

[![img](img/d8b5acb105875b63176599c0cb733433.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pXzp1ZbW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/stylekit/img/master/ForceTouchEvent_anim.gif)

### 事件处理程序:

```
var prevStage = 0
override func pressureChange(with event: NSEvent) {
    let curStage:Int = event.stage
    if event.pressureBehavior == NSPressureBehavior.primaryDeepClick,prevStage != curStage {
        switch (curStage,prevStage){
            case (0,1):
                Swift.print("from idle to clickStage")
            case (1,0):
                Swift.print("from clickStage to idle")
            case (1,2):
                Swift.print("from clickStage to deepClickStage")
            case (2,1):
                Swift.print("from deepStage to clickStage")
            default: break;//isn't possible
        }
        Swift.print("stageChange")
        prevStage = curStage/*always set prevStage to curStage on stage change*/
    }
    Swift.print("pressureChange")
} 
```

Enter fullscreen mode Exit fullscreen mode

### 正常化压力:

```
/**
 * NOTE: calculates the entire range of the stage pressures so from stage 0 to 1 the pressure goes from 0 to 0.5 and from stage 1 to 2 the linear pressure goes from 0.5 to 1 this makes it easier to scale things in a linear fashion from 0 to 1 in the entire stage range
 */
var linearPressure:CGFloat{
    if event.stage == 0 {
        return 0
    }else if event.stage == 1{
        return pressure / 2
    }else /*if stage == 2*/ {
        return 0.5 + (pressure / 2)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

由 https://github.com/eonist/Element 提供的用户界面