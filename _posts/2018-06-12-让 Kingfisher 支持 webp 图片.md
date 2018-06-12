---
layout: post
title: 让 Kingfisher 支持 webp 图片
categories: iOS
tags: Kingfisher
---

1. #### 引入 KingfisherWebP 库

   ```pod 'KingfisherWebP'```

2. #### 添加 options

    [.processor(WebPProcessor.default), .cacheSerializer(WebPSerializer.default)]

   ```swift
   self.kf.setImage(with: URL(string: string),
                    placeholder: placeholder,
                    options: [.processor(WebPProcessor.default), .cacheSerializer(WebPSerializer.default)]) { (img, err, cacheType, imgUrl) in
   
   }
   ```

   