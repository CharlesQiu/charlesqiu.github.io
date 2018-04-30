---
layout: post
title: Run Script的两个技巧
categories: iOS
tags: Xcode
---

- 使编译版本号自增

  ```shell
  buildNumber=$(/usr/libexec/PlistBuddy -c "Print CFBundleVersion" "$INFOPLIST_FILE")
  buildNumber=$(($buildNumber + 1))
  /usr/libexec/PlistBuddy -c "Set :CFBundleVersion $buildNumber" "$INFOPLIST_FILE"
  ```

- 工程内部获取编译时间

  1. 在 Info.plist 里增加一个 String 类型的键，命名了 BuildTimeString（随意命名，记得第三行换成自己的命名）

  2. 在 Run Script 增加以下语句

     ```shell
     appVersion=$(/usr/libexec/PlistBuddy -c "Print CFBundleShortVersionString" "$INFOPLIST_FILE")
     buildTime="$(date +%Y年%m月%d日%H:%M:%S) Version: $appVersion(build $buildNumber)"
     /usr/libexec/PlistBuddy -c "Set :BuildTimeString $buildTime" "$INFOPLIST_FILE"
     ```

     ​

ps：以上可写在同一个脚本里面