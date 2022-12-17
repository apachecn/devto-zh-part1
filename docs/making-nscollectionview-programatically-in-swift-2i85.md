# 在 Swift 中以编程方式制作 NSCollectionView

> 原文：<https://dev.to/onmyway133/making-nscollectionview-programatically-in-swift-2i85>

原帖[https://github.com/onmyway133/blog/issues/131](https://github.com/onmyway133/blog/issues/131)

以下是如何用程序创建`NSCollectionView`的方法。我们需要将它嵌入到`NScrollView`中，以便滚动工作。代码在 Swift 4 中

### NSCollectionView

```
let layout = NSCollectionViewFlowLayout()
layout.minimumLineSpacing = 4

collectionView = NSCollectionView()
collectionView.dataSource = self
collectionView.delegate = self
collectionView.collectionViewLayout = layout
collectionView.allowsMultipleSelection = false
collectionView.backgroundColors = [.clear]
collectionView.isSelectable = true
collectionView.register(
  Cell.self,
  forItemWithIdentifier: NSUserInterfaceItemIdentifier(rawValue: "Cell")
) 
```

Enter fullscreen mode Exit fullscreen mode

### NScrollView

```
scrollView = NSScrollView()
scrollView.documentView = collectionView
view.addSubview(scrollView) 
```

Enter fullscreen mode Exit fullscreen mode

### NSCollectionViewItem

```
final class Cell: NSCollectionViewItem {
  let label = Label()
  let myImageView = NSImageView()

  override func loadView() {
    self.view = NSView()
    self.view.wantsLayer = true
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### nscollectionview data source

```
func collectionView(_ collectionView: NSCollectionView, numberOfItemsInSection section: Int) -> Int {
  return coins.count
}

func collectionView(_ collectionView: NSCollectionView, itemForRepresentedObjectAt indexPath: IndexPath) -> NSCollectionViewItem {
  let cell = collectionView.makeItem(
    withIdentifier: NSUserInterfaceItemIdentifier(rawValue: "Cell"),
    for: indexPath
  ) as! Cell

  let coin = coins[indexPath.item]

  cell.label.stringValue = coin.name
  cell.coinImageView.image =
    NSImage(named: NSImage.Name(rawValue: "USD"))
    ?? NSImage(named: NSImage.Name(rawValue: "Others"))

  return cell
} 
```

Enter fullscreen mode Exit fullscreen mode

### NSCollectionViewDelegateFlowLayout

```
func collectionView(_ collectionView: NSCollectionView, didSelectItemsAt indexPaths: Set<IndexPath>) {
    guard let indexPath = indexPaths.first,
    let cell = collectionView.item(at: indexPath) as? Cell else {
      return
  }
}

func collectionView(_ collectionView: NSCollectionView, didDeselectItemsAt indexPaths: Set<IndexPath>) {
  guard let indexPath = indexPaths.first,
    let cell = collectionView.item(at: indexPath) as? Cell else {
      return
  }
}

func collectionView(_ collectionView: NSCollectionView, layout collectionViewLayout: NSCollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> NSSize {

  return NSSize(
    width: collectionView.frame.size.width,
    height: 40
  )
} 
```

Enter fullscreen mode Exit fullscreen mode