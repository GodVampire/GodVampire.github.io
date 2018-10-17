---
title: what in iOS
categories: 
- 结构
tags: [手机应用结构层, 基础学习]
---



### 手机应用结构层

> 总览

​	从上到下依次为:Cocoa Touch （触摸UI层）、Media（媒体层）、Core Services （核心服务层）、Core OS （核心OS层）。

​	低层次框架提供IOS的基本服务和技术，高层次框架建立在低层次框架之上用来提供更加复杂的服务和技术，较高级的框架向较低级的结构提供面向对象的抽象。

<!-- more -->

{% asset_img 总览.png %}

#### Cocoa Touch 层

> Cocoa Touch 层

​	主要包含 Multi-touch , Alerts , Core motion , Web View , View hierarchy , Map Kit , Localization , Image Picker , Controls , Camera.

{% asset_img CocoaTouch.png %}

#### Media 层

> Media 层

​	主要包含Core Audio , JPEG,PNG,TIFF, OpenAL , PDF , Audio Mixing ,QuarTz (2D) , Audio ReCording , Core Animation , Video Playback , OpenGL ES 

{% asset_img Media.png %}

#### Core Services

> Core Services

​	主要包含 Collections , Core Location , Address Book , Net Servies , Networking , Threading , File Access ,

Preferences , SQlite , URL Utilities

{% asset_img CoreServices.png %}

#### Core OS

> Core OS

​	主要包含 OSX Kernel , Power Management , Mach 3.0 , Keychain Access , BDS , Certificates , Sockets , File System , Security , Bonjour

{% asset_img CoreOS.png %}
