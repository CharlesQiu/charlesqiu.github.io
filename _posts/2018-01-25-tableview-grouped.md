---
layout: post
title: 隐藏 GroupedTableView 上边多余的间隔
categories: iOS
tags: UIKit
---

当设置 UITableview 的 Style 为 Grouped 时，会出现下面这种情况：

![001](/assets/images/2018/20180125_001.jpeg)

这里其实是一个被 UITableView 默认填充的 HeaderView，如果直接设置 HeaderView 高度为 0 也不行，但可以设置成 CGFloat.leastNormalMagnitude 和 0.1。这边有个黑魔法可以使用：

```swift

class TableViewController: UITableViewController {

    override func viewDidLoad() {
        let rect: CGRect = CGRect(x: 0, y: 0, width: 0, height: CGFloat.leastNormalMagnitude)
        tableView.tableHeaderView = UIView(frame: rect)
    }

}

```
![001](/assets/images/2018/20180125_002.jpeg)


#### **UITableViewHeader 的一些研究：**
  1. 若传入的 height == 0，则 height 被设置成默认值；
  2. 若 height 小于屏幕半像素对应的高度，这个 header 不在另一个像素渲染。

半像素也就是 1.0 / scale / 2.0，如在 @2x 屏上是 0.25，所以高度设置为 0.1 也行。
