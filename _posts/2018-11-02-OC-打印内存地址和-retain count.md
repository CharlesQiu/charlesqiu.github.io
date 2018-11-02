---
title: 打印内存地址、指针地址和 retain count
layout: post
tags: iOS,调试,Objective-C
categories: iOS
---

#### 打印指针所指向对象的地址

​	`NSLog(@"aStr指针所指向对象的地址：%p",aStr);`

#### 打印指针的地址

​	`NSLog(@"aStr指针内存地址：%x",&aStr);`

#### 打印 retain count

​	`CFGetRetainCount((__bridge CFTypeRef)self` 