---
title: Core Animation
categories: 
- 核心动画篇
tags: [Core Animation, 动画]
---

​	

本文主要内容包括

> 核心动画介绍

> 基本使用方法

> 特殊属性分析

> 相关内容介绍

> Core Animation 底层层级

> 如何提高展示提高性能

<!-- more -->

​	



​	当你抱着你媳妇儿的时候，磕着瓜子，躺在床上，看着影屏，不禁感叹科技的进步，接下来我们去了解一下Core Animation，是不是没什么联系（就要强行插入 2333

​        乔大大WWDC上介绍CoreAnimation图~在前今年这个技术还是蛮厉害的...

{% asset_img 10.png %}


{% centerquote %}

Think diffrent

Jobs

{% endcenterquote %}



好吧，开始正题

 	

#### 基础介绍

Q ：核心动画是做什么的？

A ： 官方一点de话，核心动画提供了一个通用的系统动画视图和其他视觉元素的应用程序，为应用的视图和可视对象设置动画效果。大部分更改与修改视觉对象的属性有关，将任意图形转换应用于视图或更改视图的其他视觉属性。

​	通俗一点也就是一些动画效果，一种形式，不影响原来的内容（也可以改变原有的视觉状态，但是Frame位置等不会变化）

特点：

- Core Animation可以用在Mac OS X和iOS平台。
- Core Animation的动画执行过程都是在后台操作的，不会阻塞主线程。
- Core Animation是直接作用在CALayer上的，并非UIView。

在底层运作原理上，结构图如下所示，其实还是隐藏在UIKit下面工作的：

{% asset_img 1.png %}

Q  ：如何绘制内容？

A   :  当更改触发动画时，Core Animation将图层的位图和状态信息传递给图形硬件，图形硬件完成了使用新信息渲染位图的工作，如图下图所示。

​	绘制过程图：

{% asset_img 2.png %}

​	由于它操纵静态位图，因此基于图层的绘图与传统的基于视图的绘图技术有很大不同。使用基于视图的绘图时，大多数做法是更改或者调用视图的`drawRect:`方法来重绘内容，也就是Quartz 2D的常用做法。但是用这种方式绘制会浪费很多资源，因为它在主线程上的CPU来工作的。核心动画通过在硬件中操作缓存的位图来实现相同或类似的效果，从而尽可能地避免了这种主线程的开销。而且在硬件中操作位图可以产生比在软件中(drawRect中)更快的动画。



#### 如何使用

> 继承结构图

{% asset_img 3.png %}

> CAMediaTiming 

​	CAAnimation遵循了CAMediaTiming 协议，设置一些属性比如duration、RepeatCount 可以控制动画的一些状态值

> CABasicAnimation

```swift
	// 基础动画,平移、旋转、缩放等
	let anim =  CABasicAnimation()
	anim.keyPath = "tansform.scale" //缩放  可以设置xyz方向  eg："position.y"
	anim.keyPath = "tansform.position" // 平移
	anim.keyPath = "tansform.rotation" // 旋转 
	anim.toValue = 0
	anim.repeatCount = MAXFLOAT
	anim.duration = 0.25
	anim.autoreverses = true // 自动反转
	self.layer.add(anim, forKey: nil) // 添加动画
```

> CAKeyframeAnimation

```Swift
    // 帧动画, 设置一些抖动等效果，可以设置状态值 values
    let  anim =  CAKeyframeAnimation()
    anim.keyPath = "tansform.rotation"
    anim.values = [Double.pi,-Double.pi] // 角度
    anim.repeatCount = MAXFLOAT
    anim.duration = 0.25
    self.layer.add(anim, forKey: nil) // 添加动画
        
    // Tip
    // 需要把动画效果和 事件处理在同一个代码块中 才生效
    // eg :
    imageView.image = UIImage(string:"xxx")// 设置新的image，并有上述的push效果
```

> CAAnimationGroup

```swift
    // 动画组, 可以把单个的其他对象 添加进去，一起执行
    let group = CAAnimationGroup()
    group.animations = [] // 添加 动画对象
    self.layer.add(group, forKey: nil)
        
    /*使用动画组的好处,不需要每次都去添加动画,设置动画完成时的属性.
	只需要把要执行的动画,添加到动画组的animations数组当中即可,
	最后把组动画添加到层上面,就会自动执行数组当中的动画.
	动画完成时设置的属性也只需要设置一次.*/
```

> CAPropertyAnimation

```
  	// 是一个抽象类, 没有具体的动画对象，使用CAKeyframeAnimation、CABasicAnimation
```

> CATransition

```swift
    // 过渡动画, 类似
    let anim = CATransition()
    anim.type = "push" // 等等其他很多效果 "pageCurl" // 翻页
    anim.subtype = kCATransitionFromRight // 转场的方向
    anim.startProgress = 0.5 // 开始位置
    self.layer.add(anim, forKey: nil) // 添加动画
    //转场动画必须要和转场代码一起.
    //把它们分开的话, 就没有这个转场效果了.
```

{% asset_img 4.png %}

> 强制保留动画的最终状态

```swift
	// 不要删除动画
	anim.isRemovedOnCompletion = false
	// 动画始终保持最前面
	anim.fillMode = kCAFillModeBackwards // k _ CA _ fillMode _ type
```



#### 属性分析

> Mask  Layer

​	我们设置圆角的时候经常用到

```swift
  self.layer.cornerRadius = 10
  self.layer.masksToBounds = true
```

那么会是以怎么一个方式来达到切去边角的效果呢，cornerRadius是角度，应该是个依赖数据，masksToBounds效果应该是切除超出范围的视图，那是不是跟mask有关呢？

那么观察一下 `self.layer.mask` 到底是怎么个属性，它返回的是一个Layer

```swift
open var mask: CALayer? 
```

google一下别人的解释：	

**mask图层定义了父图层的部分可见区域。**mask真正厉害的地方在于蒙版图层不局限于静态图。任何有图层构成的都可以作为mask属性，这意味着你的蒙版可以通过代码甚至是动画实时生成！
​	效果如下：

{% asset_img 5.png %}

​	运作源原理也就是这样色儿的：  

{% asset_img 6.png %}



>  接下来我们去看看如何自定义一个Layer

​	自定义Layer 示例代码：

```swift
	let lay = CALayer()
	lay.frame = self.bounds
	self.layer.addSublayer(lay)
    //设置layer的内容
	lay.contents = UIImage.init(named: "xxx")?.cgImage // 需要 cgImage 类似 cgCorlor
```

这里提一下 cg 开头的一些属性

> cgCorlor cgImage

```swift
   // Layer QuartzCore  跨平台
   // CGImageRef  cgCorlorRef  CoreGraphics 跨平台
   // UIColror UIimage  UIKit   ios
   // 保持跨平台型 不允许 使用 UIColror UIimage 需要转成 CGImageRef  cgCorlorRef
   // 将 CoreGraphics 和 UIKit 桥接起来  Access the underlying CGColor or CIColor.
```



#### 隐式动画

> 什么是隐式动画？

​	根层:UIView内部自动关联着的那个layer我们称它是根层.
​	非根层:自己手动创建的层,称为非根层.

​	隐式动画就是当对非根层的部分属性进行修改时, 它会自动的产生一些动画的效果.称这个默认产生的动画为隐式动画.

> 运作方式

```swift
    // 动画 底层 都是将操作 包装成一个 事务
    // 很多操作都绑定在一起，当操作都全部执行完毕，它才运行下一步操作
    CATransaction.begin() // Tip：区别一下CATransition(过渡动画)
    // 事务区间
    // eg ： 改变layer Frame（缩放平移） bgcorlor（渐变）
    CATransaction.setDisableActions(true) // 设置事务没有动画
   	CATransaction.setAnimationDuration(0.5) // 时长
    CATransaction.commit() // 提交事务
```

#### CADisplayLink

- CADisplayLink是一种以屏幕刷新频率触发的时钟机制，每秒钟执行大约60次左右

- CADisplayLink是一个计时器，可以使绘图代码与视图的刷新频率保持同步，而NSTimer无法确保计时器实际被触发的准确时间

- 使用方法：

- - 定义CADisplayLink并制定触发调用方法
  - 将显示链接添加到主运行循环队列

#### UIView 动画

​	示例代码：

```
	UIView.animate(withDuration: 0.5) {
        // 旋转 那个周有值 代表会 按照哪个轴的方向 旋转
        // 多个值 会按照向量计算
        self.layer.transform = CATransform3DMakeRotation( CGFloat.pi, 1, 0, 0)
        self.layer.setValue(CGFloat.pi, forKey: "transform.rotation")
    }
```

{% asset_img 7.png %}

#### 使用 View Animation 还是使用 Core Animation

​	还是强调一下： CoreAnimation 只是个假象,只作用在layer上

1. 不会改变控件的位置、大小 
2. 点击事件响应位置还是会 在原来位置，不会再看到的位置 
3. 通过强制保留动画的最终状态也不会影响上述两条

> 使用 View Animation

需要交互的时候， 因为UIView 继承 UIResponder(事件响应) ，来处理用户响应，效果比较少

> 使用 Core Animation

即操作CALayer，根据使用场景需要有：转场动画、帧动画、动画组等 种多种效果，而且CALayer的性能会高一些，因为它少了事件处理的功能，更加轻量级



#### 实战

~



#### Core Animation 底层层级

​	使用Core Animation的应用程序有三组图层对象。每一组图层对象在使应用程序的内容出现在屏幕上都有不同的作用：

- *layer tree*中的对象（或简称为“图层树”）是您的应用程序与其交互最多的对象。此树中的对象是存储任何动画的目标值的模型对象。每当更改图层的属性时，都使用这些对象之一。
- *presentation tree* 中的对象包含任何正在运行的动画的正在进行的值。尽管图层树对象包含动画的目标值，但是呈现树中的对象反映了屏幕上显示的当前值。你不应该修改这个树中的对象。而是使用这些对象来读取当前的动画值，也许从这些值开始创建一个新的动画。
- *render tree*中的对象执行实际的动画，并且是Core Animation专用的。

{% asset_img 8.png %}



​	对于layer tree中的每个对象，在presentation tree和render tree中都有一个匹配的对象，如图下图所示。如前所述，应用程序主要处理ayer tree中的对象，但有时可能访问presentation tree中的对象。具体来说，访问presentation Layer层树中对象的属性返回layer tree中的对应对象。

{% asset_img 9.png %}

> Tip 

​	 只有在动画正在执行时，才能访问presentation tree 中的对象。在动画进行过程中，presentation tree 包含当前在屏幕上出现的图层的状态值，即进行中的状态值。但是这个值和layer tree中的不同，layer tree总是反映代码设置的最后一个值，并等同于动画的最终状态。





#### 提高性能

都是官方解释了~~~ 

有几种方法可以使您的图层实现更高效。然而，与任何这样的优化一样，在尝试优化之前，您应该始终测量代码的当前性能。这为您提供了一个可用于确定优化是否正常工作的基线。

> 尽可能使用不透明层

设置`opaque`图层的属性`YES`让Core Animation知道它不需要维护图层的Alpha通道。没有alpha通道意味着合成器不需要将图层的内容与背景内容混合，这样可以节省渲染时间。但是，此属性主要与作为层支持视图一部分的图层或Core Animation创建底层图位图的情况有关。如果将图像直接分配给图层的`contents`属性，则无论`opaque`属性中的值如何，都会保留该图像的Alpha通道。

> 为CAShapeLayer对象使用更简单的路径

在`CAShapeLayer`类创建通过在合成时使你提供为位图图像路径的内容。其优点是，图层始终以尽可能好的分辨率绘制路径，但这样做的好处是需要花费额外的渲染时间。如果您提供的路径非常复杂，则对该路径进行栅格化可能会过于昂贵。如果图层大小频繁变化（因此必须经常重绘），绘图花费的时间可能会累加起来，成为性能瓶颈。

减少形状图层绘制时间的一种方法是将复杂的形状分解成更简单的形状。`CAShapeLayer`在合成器中使用更简单的路径并将多个对象叠加到另一个之上可能比绘制一条较大的复杂路径要快得多。这是因为绘图操作发生在CPU上，而合成发生在GPU上。就像这种性质的任何简化一样，潜在的性能收益取决于你的内容。因此，在优化之前测量代码的性能尤为重要，以便您有比较基准。

> 为相同的图层显式设置图层内容

如果您在多个图层对象中使用相同的图像，请自行加载图像并将其直接分配给`contents`这些图层对象的属性。将`contents`属性分配给该属性将防止该层为后备存储分配内存。相反，该图层使用您提供的图像作为其后备存储。当多个图层使用相同的图像时，这意味着所有这些图层共享相同的内存，而不是为自己分配图像副本。

> 始终将图层大小设置为积分值

为了获得最佳效果，请始终将图层对象的宽度和高度设置为整数值。虽然使用浮点数指定图层边界的宽度和高度，但是图层边界最终将用于创建位图图像。指定宽度和高度的整数值简化了Core Animation必须执行的工作，以创建和管理后备存储和其他图层信息。

> 根据需要使用异步图层呈现

您在委托`drawLayer:inContext:`方法或视图`drawRect:`方法中执行的任何绘图通常在应用的主线程上同步发生。但是，在某些情况下，同步绘制内容可能无法提供最佳性能。如果您注意到您的动画效果不佳，则可以尝试启用`drawsAsynchronously`图层上的属性以将这些操作移至后台线程。如果你这样做，确保你的绘图代码是线程安全的。和往常一样，在把它放入生产代码之前，应该总是先测量绘图的性能。

> 为图层添加阴影时指定阴影路径

让核心动画决定阴影的形状可能会很昂贵，并影响您的应用程序的性能。而不是让核心动画确定阴影的形状，而是使用`shadowPath`属性显式指定阴影形状`CALayer`。当您为此属性指定路径对象时，Core Animation将使用该形状来绘制和缓存阴影效果。对于形状永远不会改变或很少改变的图层，这可以通过减少Core Animation的渲染量来大大提高性能。





