---
title: View是怎么展示内容的
categories: 
- 原理篇
tags: [基础学习]
---



> 本篇文章要讲述些什么

- UIView 展示出来的底层运作

<!-- more -->



### 图形绘制是怎么回事

> 界面是怎么展示出来的

​	界面效果，即你媳妇儿看到手机屏幕上的一些文字，列表，以及图片等等。

​	能呈现出来的物理原理是：

​	CRT 电子枪(手机硬件相关的)从手机屏幕由上到下一行行扫描，扫描完成后得到一帧画面，显示器就会这一帧画面一帧一帧的连续起来(正常不丢帧的情况下是每秒60帧)，就是我们看到的手机界面呈现出来的动画等流畅顺滑效果,了解丝滑流程的点击[这里](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)。看完记得回来~~~招手~👻

> 开发常用的展示手段

​	从程序启动的`UIWidow`，再到第一个`UIViewController`，或者是添加的`UILabel` `UIButton` `UIScorllView`等UI相关的控件不难发现他们都和`UIView`相关,那么就来分析一下为什么使用继承自或者包含`UIView`的控件能使图像展示到屏幕上。



> UIView 展示出来的底层运作

​	View 之所以能显示,是因为内部的Layer ,delegate Layer通过渲染内容来 生成跟View相关的上下文,并使View 中的内容呈现

```swift
 open var layer: CALayer { get } // returns view's layer. Will always return a non-nil value. view is layer's delegate
```

可以通过`UIView`的API发现一些与draw相关的内容

那么可以通过draw相关的方法得到

```swift
    override init(frame: CGRect) {
        super.init(frame: frame)
        print(#function)
        self.backgroundColor = UIColor.brown
    }   


    override func draw(_ rect: CGRect) {
        print(#function)
    }
    override func drawHierarchy(in rect: CGRect, afterScreenUpdates afterUpdates: Bool) -> Bool {
        print(#function)
        return true
    }
    override func draw(_ rect: CGRect, for formatter: UIViewPrintFormatter) {
        print(#function)
    }
    override func draw(_ layer: CALayer, in ctx: CGContext) {
        print(#function)
    }
   //以及ViewController中的ViewwillAppear,ViewDidApper
```

调用结果为：

{% asset_img 3.jpg %}

```
/* If defined, called by the default implementation of -drawInContext: */
如果定义,称为-drawInContext默认实现的:
```

好吧,我还是解释一下 ”而我们通常会在`  override func draw(_ rect: CGRect) `中绘制的一些基本图形(也是自动调用的)“这个是怎么一回事。

官方文档给的是：

{% asset_img 4.jpg %}

翻译为:

```
视图绘制需要的基础上发生。视图是第一次显示时,或者当全部或部分变得可见由于布局变化,系统询问该视图画它的内容。视图包含使用UIKit定制内容或核心图形、系统调用视图的draw(_:)方法。该方法的实现负责绘制视图的内容到当前图形上下文,这是系统设置的自动调用该方法之前。这将创建一个静态视图的可视化表示的内容可以显示在屏幕上。
```

其实我是没找到为什么会覆盖`func draw(_ rect: CGRect)`,搜索了一番:[原文](http://blog.csdn.net/tongwang123456/article/details/17881291).

```
当UIView需要显示时，它内部的层会准备好一个CGContextRef(图形上下文)
然后调用delegate(这里就是UIView)的drawLayer:inContext:方法，并且传入已经准备好的CGContextRef对象。
而UIView在drawLayer:inContext:方法中又会调用自己的drawRect:方法.
(估计是不再实现子类的drawRect:方法了)
```

言归正传,那么先观察一下接下来需要的`override func draw(_ rect: CGRect)`的方法调用顺序

```swift
    override init(frame: CGRect) {
        super.init(frame: frame)
        print(#function)
        self.backgroundColor = UIColor.brown
    }
    override func draw(_ rect: CGRect) {
        print(#function)
    }
   //以及ViewController中的ViewwillAppear,ViewDidApper
```

结果是：

{% asset_img 2.jpg %}

`  override func draw(_ rect: CGRect) `也是如上面说的自己调用的,然后我们接下来需要绘制的一些基本图形就将会在这方法里调用实现

> Next Step

还是没有说怎么展示的，好伐好伐

**下图就是从CPU到GPU的过程**

{% asset_img 1.jpg %}

kuminasa，剩下的我也不怎么会,起飞链接[iOS图形渲染分析](https://www.jianshu.com/p/77d0cb17cfad).



### 结尾

> 总结

开篇的就讲这么多吧,主要是View展示的时候的代码底层调用,图形渲染分析

> Next Step

接下来陆续会有 Quartz2D的一些实例,以及CoreAnimation 核心动画相关的总结