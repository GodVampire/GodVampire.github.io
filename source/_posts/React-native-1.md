---
title: React-Native
categories:
- React-Native
tags: [程序,RN]
---

​	不知不觉也用了RN三个月了，从零基础开始，到现在快要上线了，一路也是坎坎坷坷。现在有些时间来总结一下吧。

​	相信有不少是从ios 或者 android 转过来的，或者有一定的前端基础的。那么我就把这三个月的过程再来重新捋一捋，希望能帮到你。
<!-- more -->
> 基础储备

* 什么是React-native，算了 自己查吧~

* 环境搭建，相信难不倒你，谷歌度娘任你选，推荐用最新的[React Native Versions](https://facebook.github.io/react-native/versions).
* 合适的[文档](https://reactnative.cn/docs/0.51/getting-started.html)你能帮助你节省很多时间.

* 熟悉一下 js 语言特性，现在我用的js好像是[ES6](http://es6.ruanyifeng.com/#README)。原来用的OC和swift，直接转过来一时真的有点不适应弱类型语言，不过用起来是真的舒服。在开始，对布局呀，线程呀，还有像 [promise](http://es6.ruanyifeng.com/#docs/promise)，then，await一些关键字糊里也都糊涂的，相信过一段时间总会学好的。

  如果你已经决定开始使用了，和掌握一定的基础知识之后，那就接下来一起探索React-Native的世界吧

> 项目搭建

​	开始搭建项目的时候，一般会确立一下的项目的基本结构，以及大概技术选型。

​	项目的基本机构的话，通常都是Tabbar+Navigation，或者是draw也就是抽屉效果，技术选型的话，我是用的[Redux](https://cn.redux.js.org/)体系(其实是被推荐这么用的)+[react-native-navigation](https://www.npmjs.com/package/react-native-navigation)然后就开始动工了。

​	恩，咦，啊？怎么弄，好吧，新手最重要的就要学会找教程，如何抱大腿，[**这篇React-Native从零搭建App**](https://juejin.im/post/5a9f93d96fb9a028d2077c19)，绝对适合你去搭建起来React-Native App，（其实我就是按照这个来的 2333

​	如果你按照[这个](https://juejin.im/post/5a9f93d96fb9a028d2077c19)搞定了，那么就可以在界面上先练习[练习布局UI](https://reactnative.cn/docs/0.51/layout-with-flexbox.html#content)，先体验体验。建议先看完[入门基础](https://reactnative.cn/docs/0.51/layout-with-flexbox.html#content).

​	如果你不想使用Redux，还有类似的[**mobx-react**](https://github.com/mobxjs/mobx-react),可以参考[**react-native-iShiWuPai**](https://github.com/ljunb/react-native-iShiWuPai)，来帮你快速实现，不过个人比较推荐用Redux，因为在此基础之上我用到的有redux-logger,redux-persist,redux-reset,redux-saga,这些会帮你做很多东西，让让复杂的东西更加简单化.这里就不一一介绍了。上面的”React-Native从零搭建App“已经说的很清楚了。

> 项目进阶

​	完成一个完整的App，用到的东西真的会很多，这时候就需要学会如何找库，合理的运用github,能节省你不少时间。（这里有几个小细节要提醒大家一下，因为React-native版本更新的快，在快速迭代中肯定很多库都会出现不兼容的情况，所以尽量找start多的，更新频繁的，就像2 years ago的库，就尽量别用了。

​	废话少说，[**react-native-guide**](https://github.com/reactnativecn/react-native-guide#%E5%9B%BE%E4%B9%A6)，想要什么搜一搜，没事可以看一看，真的很多，很满yo~还有这老哥的[ReactNativeMaterials](https://github.com/LeoMobileDeveloper/ReactNativeMaterials).

​	什么？知道库不知道怎么装？乖乖的[拿去](https://reactnative.cn/docs/0.51/linking-libraries-ios.html#content)，这里要说一下，在install 过程中，有些自动 link的是会各种报错，就自己有问题，怎么办，试试手动link，（其实自动link的就是代替了手动过程

​	呃，什么？这些还不够用，那么试一试[dva](https://github.com/dvajs/dva/issues/1), Why is it called dva? From [OverWatch](http://ow.blizzard.cn/heroes/dva).

​	这时候你肯定左手枪，右手炮了。但是怎么用，怎么把利器发挥出来，就是coder你的使命了。

> 项目codeing

​	看到这，其实还是什么都不会是不是，遇到各种问题的话，还是从基础的来，推文[航歌](http://www.hangge.com/blog/cache/category_76_1.html),有很多界面上零散的知识。

​	还有这个，有很多js高级用法[MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout).多利用搜索，找到你想要的。

​	努力复制粘贴吧，这是什么话，努力ctrlC + ctrlV 吧，骚年。

> 项目尾声

​	如果你的界面和业务都处理的差不多了，这里几个小细节希望能帮到你

 	1.在RN项目中会有白屏现象的话，而且你用的是[react-native-navigation](https://www.npmjs.com/package/react-native-navigation),也就是要处理android的Splash Screen，推荐看[这个](https://github.com/wix/react-native-navigation/issues/2760)，别瞎跑了，我搞了好几天.

​       2.[javaScipt 浮点数陷阱及解法](https://github.com/camsong/blog/issues/9)

​	3.逆向传值，也就是原生和RN交互，如果你有rn解决不了的问题，为何不试试原生的写，但是要有两套代码哦~ 还要注意交互的开销。

> 结束

​	如果你上述的都搞定了，我也基本上没什么可教的了. 或者接下来就是去探索React-native的原理了吧

​	难点，理解RN的[state](https://reactnative.cn/docs/0.51/state.html#content),也就是Redux 中的，action，reducer, 还有怎么在saga中去处理component中的业务逻辑。让各个类各行其事~

> 福利时间

​	如果你封装的网络请求不怎么好使的话，[拿去](https://github.com/GodVampire/React-Native-fetch).

​	



 



​	