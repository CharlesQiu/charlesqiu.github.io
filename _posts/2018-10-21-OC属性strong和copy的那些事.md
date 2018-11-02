---
categories: iOS
layout: post
tags: 'Objective-C'
title: OC 属性 strong 和 copy 的那些事
---

-   [先创建四个属性](#先创建四个属性)
-   [然后 `NSLog`](#然后-nslog)
-   [打印输出](#打印输出)
-   [分析结果](#分析结果)
-   [得出结论](#得出结论)

一直对 `strong` 和 `copy` 不甚了解，今天决定做个小实验。

#### 先创建四个属性

``` {.objectivec}
@property (nonatomic, strong) NSArray *arrStrong;
@property (nonatomic, copy) NSArray *arrCopy;
@property (nonatomic, strong) NSMutableArray *mArrStrong;
@property (nonatomic, copy) NSMutableArray *mArrCopy;
```

#### 然后 `NSLog`

``` {.objectivec}
    NSLog(@" ==== start ====");
    
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempArr", tempArr, CFGetRetainCount((__bridge CFTypeRef)tempArr), [tempArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempMutableArr", tempMutableArr, CFGetRetainCount((__bridge CFTypeRef)tempMutableArr), [tempMutableArr componentsJoinedByString:@","]);
    
    NSLog(@"\n------------------------------");
    NSLog(@" ==== self.arrStrong start ====");

    self.arrStrong = tempArr;
    NSLog(@"\n");
    NSLog(@" ==== self.arrStrong = tempArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempArr", tempArr, CFGetRetainCount((__bridge CFTypeRef)tempArr), [tempArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _arrStrong", _arrStrong, CFGetRetainCount((__bridge CFTypeRef)_arrStrong), [_arrStrong componentsJoinedByString:@","]);

    self.arrStrong = tempMutableArr;
    NSLog(@"\n");
    NSLog(@" ==== self.arrStrong = tempMutableArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempMutableArr", tempMutableArr, CFGetRetainCount((__bridge CFTypeRef)tempMutableArr), [tempMutableArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _arrStrong", _arrStrong, CFGetRetainCount((__bridge CFTypeRef)_arrStrong), [_arrStrong componentsJoinedByString:@","]);

    NSLog(@"\n------------------------------");
    NSLog(@" ==== self.arrCopy start ====");

    self.arrCopy = tempArr;
    NSLog(@"\n");
    NSLog(@" ==== self.arrCopy = tempArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempArr", tempArr, CFGetRetainCount((__bridge CFTypeRef)tempArr), [tempArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _arrCopy", _arrCopy, CFGetRetainCount((__bridge CFTypeRef)_arrCopy), [_arrCopy componentsJoinedByString:@","]);
    
    self.arrCopy = tempMutableArr;
    NSLog(@"\n");
    NSLog(@" ==== self.arrCopy = tempMutableArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempMutableArr", tempMutableArr, CFGetRetainCount((__bridge CFTypeRef)tempMutableArr), [tempMutableArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _arrCopy", _arrCopy, CFGetRetainCount((__bridge CFTypeRef)_arrCopy), [_arrCopy componentsJoinedByString:@","]);

    NSLog(@"\n------------------------------");
    NSLog(@" ==== change tempMutableArr start ====");

    [tempMutableArr addObject:@"增加一个值"];
    NSLog(@"\n");
    NSLog(@" ==== [tempMutableArr addObject:@\"增加一个值\"]  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempMutableArr", tempMutableArr, CFGetRetainCount((__bridge CFTypeRef)tempMutableArr), [tempMutableArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _arrStrong", _arrStrong, CFGetRetainCount((__bridge CFTypeRef)_arrStrong), [_arrStrong componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _arrCopy", _arrCopy, CFGetRetainCount((__bridge CFTypeRef)_arrCopy), [_arrCopy componentsJoinedByString:@","]);

    NSLog(@"\n------------------------------");
    NSLog(@" ==== self.mArrStrong start ====");

    self.mArrStrong = tempArr;
    NSLog(@"\n");
    NSLog(@" ==== self.mArrStrong = tempArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempArr", tempArr, CFGetRetainCount((__bridge CFTypeRef)tempArr), [tempArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrStrong", _mArrStrong, CFGetRetainCount((__bridge CFTypeRef)_mArrStrong), [_mArrStrong componentsJoinedByString:@","]);
//    [self.mArrStrong addObject:@"增加一个值"]; // crash
//    NSLog(@" ==== [self.mArrStrong addObject:@\"增加一个值\"]  ====");
//    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrStrong", _mArrStrong, CFGetRetainCount((__bridge CFTypeRef)_mArrStrong), [_mArrStrong componentsJoinedByString:@","]);

    self.mArrStrong = tempMutableArr;
    NSLog(@"\n");
    NSLog(@" ==== self.mArrStrong = tempMutableArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempMutableArr", tempMutableArr, CFGetRetainCount((__bridge CFTypeRef)tempMutableArr), [tempMutableArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrStrong", _mArrStrong, CFGetRetainCount((__bridge CFTypeRef)_mArrStrong), [_mArrStrong componentsJoinedByString:@","]);
    [self.mArrStrong addObject:@"增加一个值"];
    NSLog(@" ==== [self.mArrStrong addObject:@\"增加一个值\"]  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrStrong", _mArrStrong, CFGetRetainCount((__bridge CFTypeRef)_mArrStrong), [_mArrStrong componentsJoinedByString:@","]);

    NSLog(@"\n------------------------------");
    NSLog(@" ==== self.mArrCopy start ====");
    self.mArrCopy = tempArr;
    NSLog(@"\n");
    NSLog(@" ==== self.mArrCopy = tempArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempArr", tempArr, CFGetRetainCount((__bridge CFTypeRef)tempArr), [tempArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrCopy", _mArrCopy, CFGetRetainCount((__bridge CFTypeRef)_mArrCopy), [_mArrCopy componentsJoinedByString:@","]);
//    [self.mArrCopy addObject:@"增加一个值"]; // crash
//    NSLog(@" ==== [self.mArrCopy addObject:@\"增加一个值\"]  ====");
//    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrCopy", _mArrCopy, CFGetRetainCount((__bridge CFTypeRef)_mArrCopy), [_mArrCopy componentsJoinedByString:@","]);
    
    self.mArrCopy = tempMutableArr;
    NSLog(@"\n");
    NSLog(@" ==== self.mArrCopy = tempMutableArr  ====");
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === tempMutableArr", tempMutableArr, CFGetRetainCount((__bridge CFTypeRef)tempMutableArr), [tempMutableArr componentsJoinedByString:@","]);
    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrCopy", _mArrCopy, CFGetRetainCount((__bridge CFTypeRef)_mArrCopy), [_mArrCopy componentsJoinedByString:@","]);
//    [self.mArrCopy addObject:@"增加一个值"]; // crash
//    NSLog(@" ==== [self.mArrCopy addObject:@\"增加一个值\"]  ====");
//    NSLog(@"内存地址 = %p, retainCount = %li, 值 = %@, === _mArrCopy", _mArrCopy, CFGetRetainCount((__bridge CFTypeRef)_mArrCopy), [_mArrCopy componentsJoinedByString:@","]); 
```

#### 打印输出

``` {.shell}
 ==== start ====
内存地址 = 0x6000038bb680, retainCount = 1, 值 = temp arr, === tempArr
内存地址 = 0x6000034d9260, retainCount = 1, 值 = temp mutable arr, === tempMutableArr

------------------------------
 ==== self.arrStrong start ====


 ==== self.arrStrong = tempArr  ====
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === tempArr
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === _arrStrong


 ==== self.arrStrong = tempMutableArr  ====
内存地址 = 0x6000034d9260, retainCount = 2, 值 = temp mutable arr, === tempMutableArr
内存地址 = 0x6000034d9260, retainCount = 2, 值 = temp mutable arr, === _arrStrong

------------------------------
 ==== self.arrCopy start ====


 ==== self.arrCopy = tempArr  ====
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === tempArr
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === _arrCopy


 ==== self.arrCopy = tempMutableArr  ====
内存地址 = 0x6000034d9260, retainCount = 2, 值 = temp mutable arr, === tempMutableArr
内存地址 = 0x6000038bb770, retainCount = 1, 值 = temp mutable arr, === _arrCopy

------------------------------
 ==== change tempMutableArr start ====


 ==== [tempMutableArr addObject:@"增加一个值"]  ====
内存地址 = 0x6000034d9260, retainCount = 2, 值 = temp mutable arr,增加一个值, === tempMutableArr
内存地址 = 0x6000034d9260, retainCount = 2, 值 = temp mutable arr,增加一个值, === _arrStrong
内存地址 = 0x6000038bb770, retainCount = 1, 值 = temp mutable arr, === _arrCopy

------------------------------
 ==== self.mArrStrong start ====


 ==== self.mArrStrong = tempArr  ====
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === tempArr
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === _mArrStrong


 ==== self.mArrStrong = tempMutableArr  ====
内存地址 = 0x6000034d9260, retainCount = 3, 值 = temp mutable arr,增加一个值, === tempMutableArr
内存地址 = 0x6000034d9260, retainCount = 3, 值 = temp mutable arr,增加一个值, === _mArrStrong
 ==== [self.mArrStrong addObject:@"增加一个值"]  ====
内存地址 = 0x6000034d9260, retainCount = 3, 值 = temp mutable arr,增加一个值,增加一个值, === _mArrStrong

------------------------------
 ==== self.mArrCopy start ====


 ==== self.mArrCopy = tempArr  ====
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === tempArr
内存地址 = 0x6000038bb680, retainCount = 2, 值 = temp arr, === _mArrCopy


 ==== self.mArrCopy = tempMutableArr  ====
内存地址 = 0x6000034d9260, retainCount = 3, 值 = temp mutable arr,增加一个值,增加一个值, === tempMutableArr
内存地址 = 0x6000034cff30, retainCount = 1, 值 = temp mutable arr,增加一个值,增加一个值, === _mArrCopy
```

#### 分析结果

  -------------------------------------------------------------------------------
                          NSArray retainCount = 1 NSMutableArray retainCount = 1
  ----------------------- ----------------------- -------------------------------
  NSArray strong          地址不变 retainCount =  地址不变，retainCount =
                          2                       2，操作原地址数据会改变当前值

  NSArray copy            地址不变 retainCount =  地址变化，retainCount = 1
                          2                       

  NSMutableArray strong   地址不变 retainCount =  地址不变，retainCount = 2
                          2，此时操作属性会 crash 

  NSMutableArray copy     地址不变 retainCount =  地址变化，retainCount =
                          2，此时操作属性会 crash 1，操作属性 crash
  -------------------------------------------------------------------------------

#### 得出结论

-   `NSArray` 最好使用 `copy` 修饰
-   `NSMutableArray` 最好用 `strong` 修饰
-   给 `NSMutableArray` 赋 `NSArray` 值需要使用
    `[NSMutableArray arrayWithArray:tempArr]` 方式
