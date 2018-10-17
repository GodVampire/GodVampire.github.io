---
title: Quartz 2D
categories: 
- 核心动画篇
tags: [Quartz2D, 动画]
---



文本主要包括

Quartz2D 概述

>  Quartz2D 介绍

>  图形上下文介绍

>  Quartz2D的内存管理

> CALayer 介绍

> 怎么自定义View

图形上下文的使用

> 使用主要流程

> 截屏  、水印儿、圆角等常见功能



<!-- more -->

#### Quartz2D 概述

那么 接触Quartz2D之前  需要先了解一些相关的知识储备

##### Quartz2D 介绍

```
Q: 什么是Quartz2D
A: 1.是一个二维的绘图引擎,同时支持iOS和Mac系统
   2.可以使用Quartz 2D应用程序编程接口（API）访问基于路径的绘图，
   包括透明绘画，着色，绘制阴影，透明度图层，颜色管理，消除锯齿渲染，
   以及PDF文档生成和PDF元数据访问

Q: Quartz2D的常见使用方式
A: 画基本线条,绘制文字,图片,截图,自定义UIView等

Q: Quartz2D在开发中的价值
A: 当我们的控件样式极其复杂时,可以把控件内部的结构给画出画,就是自定义控件.
```

##### 图形上下文介绍

```
Q: 什么是图形上下文？
A: 上下文是用来保存绘制的内容状态,并决定绘制到哪个地方的.
   可以把绘制好的内容先保存到图形上下文,然后根据选择的图形上下文的方式不同,
   绘制的内容显示到地方也不相同,即输出目标也不相同.
   
Q: 图形上下文的类型有那些?
A:  1.Bitmap Graphics Context(位图上下文)
    2.PDF Graphics Context
	3.Window Graphics Context 
	4.Layer Graphics Context(图层上下文,自定义UIView取得上下文就是图层上下文,
	UIView之所以能够显示就是因为他内部有一个图层)
	5.Printer Graphics Context(打印机)
```

#####  Quartz2D的内存管理

```
Q: Quartz 2D 使用过程中会有引用计数嘛？
A: Quartz使用Core Foundation内存管理模型，其中的对象是引用计数的。
   创建时，Core Foundation对象以引用计数1开始。
   可以通过调用保留该对象的函数来增加引用计数，并通过调用释放该对象的函数来减少引用计数。
   当引用计数递减到0时，该对象被释放。

Q: 有什么需要规则么
A: 1. 一般情况下，如果您从名称中使用“创建”或“复制”这个单词的函数中获得一个对象，
   则必须在完成时释放该对象。否则，会导致内存泄漏。
   2. 如果您从名称中不包含单词“创建”或“复制”的函数获取对象，则不拥有对该对象的引用，并且不需要释放该对象。
   3.例如，如果收到对CGColorspace对象的引用，则可以使用这些函数CGColorSpaceRetain并CGColorSpaceRelease根据需要保留和释放该对象。
   还可以使用的核心基础功能CFRetain和CFRelease，不需要把对象置为nil或者NULL。
```

##### CALayer 介绍

```swift
Q: 什么是CALayer
A: 直接上UIView中的属性说明 open var layer: CALayer { get }
// returns view's layer. Will always return a non-nil value. view is layer's delegate
A: 补充CALayer继承自NSObject,但是CALayer不能响应任何用户事件,
（UIView中的响应事件都是UIResponder处理的）可以认为UIView注重于处理用户事件，而CALayer则注重于显示效果。
CALayer是绘制内容的,不处理事件响应,与UIView是相互依赖的,依赖于UIView来显示绘制内容,
UIView依赖于CALayer来提供内容。

```

```
Q: Layer有位置大小吗？
A: 每个UIView都包含一个CALayer在背后提供内容的绘制和显示
   layer.bounds 来设定 WH
   layer.position 来设置layer在父层 中的位置，以父层的左上角为原点，position为终点的位置  得到 一个矩形大小A
   layer.anchorPoint( CGPoint 锚点  (0,0) -> (1,1) )
(默认值是(0.5,0.5)可能因为这个值影响正常显示) 根据bounds 和 坐标 得到一个 点 B(在layer中的) 
且 移动使 position 和 anchorPoint 重合 来 达到 确定一个新的frame 的目的, 
其实这个 有点绕,同过自定义一个Layer,多设个几个值,体验体验就明了了。
```

```
Q: Layer内部是怎么实现的呢
A: layer内部维护着三分layer tree，分别是
presentLayer Tree(动画树)，
modeLayer Tree(模型树),
Render Tree(渲染树)，
在做 iOS动画的时候，我们修改动画的属性，在动画的其实是Layer的presentLayer的属性值,
而最终展示在界面上的其实是提供View的modelLayer。
```

##### 怎么自定义View

```
首先得要有上下文,有了上下文才能决定把绘制的东西显示到哪个地方去.
其次就是这个上下文必须得和View相关联.才能将内容绘制到View上面.
	
步骤:
1.要先自定定UIView
2.实现DrawRect方法
3.在DrawRect方法中取得跟View相关联的上下文.
4.绘制路径(描述路径长什么样).
5.把描述好的路径保存到上下文(即:添加路径到上下文)
6.把上下文的内容渲染到View
```

####图形上下文的使用

##### 绘制心形

> 示例

​	{% asset_img exampleHeart.png %}

> 示例代码

```swift
    override func draw(_ rect: CGRect) {
        // 1.取得跟View相关连的上下文
        if  let ctx = UIGraphicsGetCurrentContext(){
            // 2.描述路径
            let path = UIBezierPath()
            // 2.1 设置起点
            path.move(to: CGPoint(x: 20, y: 80))
            // 2.2 移动到某一个点
            path.addLine(to: CGPoint(x: 100, y: 180))
            // 一个路径可以画多条线,以上条线为起点
            path.addLine(to: CGPoint(x: 180, y: 80))
            // 添加贝塞尔曲线 to:终点   controlPoint控制点  offsetx来控制弯曲的位置   offsetY来控制区线的弯曲程度
            path.addQuadCurve(to: CGPoint(x: 100, y: 070), controlPoint: CGPoint(x: 140, y: -10))
            path.addQuadCurve(to: CGPoint(x: 20, y: 80), controlPoint: CGPoint(x: 60, y: -10))
            
            // 设置上下文状态  都以 set 开头
            ctx.setLineWidth(10) // 线宽
            ctx.setLineJoin(.round) // 连接样式
            ctx.setLineCap(.butt) // 顶角样式
            ctx.setStrokeColor(UIColor.red.cgColor) // 线的颜色
            
            // 需要 在添加到上下文之前 配置
            //                ctx.translateBy(x: <#T##CGFloat#>, y: <#T##CGFloat#>)// 平移
            //                ctx.rotate(by: <#T##CGFloat#>)// 旋转
            //                ctx.scaleBy(x: <#T##CGFloat#>, y: <#T##CGFloat#>) // 缩放
            ctx.saveGState() // 保存住上下文中的状态   --- 即:把状态固定住，即使改变了，不影响下次的值（默认是共用一个全局变量）
            ctx.restoreGState() // 恢复
            
            // 3. 把路径添加到上下文
            ctx.addPath(path.cgPath)
            // 4. 把上下文渲染出来
            ctx.strokePath()
        }
    }
```

##### 主要流程

* 设置图形上下文关联
* 绘制路径 (贝塞尔曲线怎么用自行Google)
* 空间转换（平移，旋转，缩放）
* 填充  即颜色，宽度，样式等状态值
* 设置渐变
* 设置阴影
* 设置透明度 
* 数据管理 即saveGState



##### 使用 Quartz2D绘制文字，图片

> 示例

{% asset_img exampleText.png %}

> 绘制文字示例代码

```swift
    override func draw(_ rect: CGRect) {
        let str:NSString = "Vampire_时代"
        
        var dict = [NSAttributedStringKey : Any]()
        dict[NSAttributedStringKey.font] = UIFont.systemFont(ofSize: 20)
        dict[NSAttributedStringKey.backgroundColor] = UIColor.white // 背景色
        
        dict[NSAttributedStringKey.foregroundColor] = UIColor.red // 字体色
        dict[NSAttributedStringKey.strokeWidth] = 3//描边宽度
        
        let shadow = NSShadow()
        shadow.shadowOffset = CGSize(width: 1, height: 1) //阴影偏移量
        shadow.shadowColor = UIColor.yellow
        shadow.shadowBlurRadius = 1 // 模糊程度
        dict[NSAttributedStringKey.shadow] = shadow

        str.draw(at: CGPoint.zero, withAttributes: dict)
    }
```

> 绘制图片示例代码

```swift
    override func draw(_ rect: CGRect) {
        if let image = UIImage.init(named: "xxx"){
            //way 1
            image.draw(at: CGPoint.zero) //保持原来图片
            //way 2
            UIRectClip(CGRect(x: 0, y: 0, width: 50, height: 50)) // 添加裁剪区域
            image.drawAsPattern(in: self.bounds)
        }
     }
```

##### 使用 Quartz2D 添加水印,裁圆角

> 示例代码

```
    // 裁圆
    func clipCrile(image: UIImage?) -> UIImage? {
        if let tempImage = image{
            UIGraphicsBeginImageContextWithOptions(tempImage.size, false, 0.0)
            let path = UIBezierPath(ovalIn: CGRect(origin: CGPoint.init(x: 0, y: 0), size: tempImage.size))
            path.addClip()
            tempImage.draw(at: CGPoint.zero)
            // 获取 到图片
            let newImage = UIGraphicsGetImageFromCurrentImageContext()
            // 关闭
            UIGraphicsEndImageContext()
            return newImage
        }
        return nil
    }
```



##### 获取截屏信息

##### 简单的画板功能

[见Demo](https://github.com/GodVampire/Quartz2D-Demo)





