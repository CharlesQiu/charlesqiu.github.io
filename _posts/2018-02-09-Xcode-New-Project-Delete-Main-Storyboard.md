---
layout: post
title: Xcode 新建工程去除 Main.Storyboard
categories: iOS
tags: Xcode
---

一共三步：

1. 找到 info.plist -> 删除 Main storyboard file base name 键和值
2. 删除 Main.Storyboard
3. 在 AppDelegate.swift 加上
```swift
  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {

      self.window = UIWindow(frame: UIScreen.main.bounds)
      self.window?.backgroundColor = UIColor.white
      self.window?.rootViewController = XXXViewController()
      self.window?.makeKeyAndVisible()

<<<<<<< HEAD
=======
      WXApi.registerApp(weiChatAppId)

>>>>>>> 25065b61fc3ed0ea3fc0e57300291b63d9cebf45
      return true
  }
```
