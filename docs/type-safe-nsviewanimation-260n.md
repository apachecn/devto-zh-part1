# 类型安全 NSViewAnimation

> 原文：<https://dev.to/masakihori/type-safe-nsviewanimation-260n>

```
protocol ViewAnimationTarget {}
extension NSView: ViewAnimationTarget {}
extension NSWindow: ViewAnimationTarget {}

struct ViewAnimationAttributes {

    private(set) var animations: [NSViewAnimation.Key: Any]

    init(target: ViewAnimationTarget, startFrame: NSRect? = nil, endFrame: NSRect? = nil, effect: NSViewAnimation.EffectName? = nil ) {

        animations = [.target: target]
        animations[.startFrame] = startFrame
        animations[.endFrame] = endFrame
        animations[.effect] = effect
    }

    var startFrame: NSRect? {
        get { return animations[.startFrame] as? NSRect }
        set { animations[.startFrame] = newValue }
    }

    var endFrame: NSRect? {
        get { return animations[.endFrame] as? NSRect }
        set { animations[.endFrame] = newValue }
    }

    var effect: NSViewAnimation.EffectName? {
        get { return animations[.effect] as? NSViewAnimation.EffectName }
        set { animations[.effect] = newValue }
    }
}

class ViewAnimation: NSViewAnimation, NSAnimationDelegate {

    var completeHandler: (() -> Void)?

    init(viewAnimations: [ViewAnimationAttributes]) {

        super.init(viewAnimations: viewAnimations.map { $0.animations })
    }

    required init?(coder: NSCoder) {
        fatalError("Can not initialize with NSCoder")
    }

    /// コンプリートハンドラー付き
    /// start with completion handler
    func start(completeHandler: @escaping () -> Void) {

        delegate = self
        self.completeHandler = completeHandler

        start()
    }

    ///
    func animationDidEnd(_ animation: NSAnimation) {

        completeHandler?()
        delegate = nil
        completeHandler = nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

示例

```
let view1 = NSView()

// 個々を別々に設定
// divide initialize and setting. 
var animeAttr1 = ViewAnimationAttributes(target: view1)
animeAttr1.startFrame = NSRect(x: 0, y: 0, width: 100, height: 100)
animeAttr1.endFrame = NSRect(x: 100, y: 100, width: 100, height: 100)
animeAttr1.effect = .fadeIn

let view2 = NSView()

// 初期化時に全部設定
// initialize with all setting.
let animeAttr2 = ViewAnimationAttributes(target: view2,
                                         startFrame: NSRect(x: 100, y: 100, width: 100, height: 100),
                                         endFrame: NSRect(x: 0, y: 0, width: 100, height: 100),
                                         effect: .fadeOut)

let animation = ViewAnimation(viewAnimations: [animeAttr1, animeAttr2])

animation.duration = 0.5

// コンプリートハンドラー付きstart
/// start with completion handler
animation.start {
    print("Finish animation")
} 
```

Enter fullscreen mode Exit fullscreen mode