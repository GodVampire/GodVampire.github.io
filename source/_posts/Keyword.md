---
title: Swift中关键字的使用
categories
- Swift
tag: [关键字, 语法糖]
---

​	

##### 前言

​     Swift 和 Object-C 从使用的角度比较起来绝对是偏爱Swift的。从使用角度来说，语法简练，从开始接触的Swift2.3到现在的Swift 4见证了是如何把冗余的代码优化的。

举个栗子🌰从OC的`    [[NSUserDefaults standardUserDefaults] synchronize];`到Swift2.3的

`NSUserDefaults.standardUserDefaults().synchronize()` 再到Swift 4的`UserDefaults.standard.synchronize()`这句代码可能是是简单的变更，但是也符合变化的总趋势。emm~，Swift Module中很多去掉了NS前缀，是不再是Sun和Next制定了嘛？成Apple自己的一套，2333~ 。去掉了多余的语法解释，更加发挥enum的优势等等吧，还有安全角度，协议的发扬光大，函数化编程，泛型，语法糖，关键字等等，似乎有点跑题了...

​     那么就回归正题，来看看Swift有哪些令人爱不释手的关键字和语法糖吧~ 先推荐一个语法糖[Then](https://github.com/devxoul/Then).很有意思..

​     根据深入浅出原则，先从简单的常见的开始~

​     排名不分先后，根据使用印象程度来说

​     使用频率根据目前项目来说，从目前有500个file的工程中搜索~

### 可选类型模块

##### guard

> 使用频率

搜索了一下`guard` 结果是: 437results in 111files，很多可能来自第三方库。但是也可见其使用频率

> 常见用法

来个简单栗子，随便copy一句

```swift
 guard let axis = self.axis else { return }
```

代码作用应该是判断全局的self.axis变量是否为空，并生成局部变量axis，为空则return，不执行下面的语句。

> 作用

`guard`翻译过来有守卫，警卫的意思，也顾名思义，就是来保卫`guard`下面的代码安全，满足`guard`的条件允许通过，否则必须`return`，在处理Swift的`optional`可选择类型时，应用广泛

相似语句

##### if let

> 使用频率

搜索了一下`if let` 结果是: 822 results in 126files，很多来自model，通过init解析的model需要来处理可能为空的类型。

> 示例代码

```
 var baseUrl: String!
if let baseUrl = dictionary["baseUrl"] as? String {
    self.baself = baseUrl
}else{
    self.baself = ""
}
```

 代码的作用来解析字典的时候，判断取值，而不是强制解包，分`if else`，如果字典解析不为nil，则执行代码块的内容，否则执行else

> 作用

对可选值 来校验，来达到数据安全 ，跟`guard`相似，但是`if let`会生成代码块，不会强制要求有返回值

##### ??

> 使用频率

搜索了一下`??` 结果是: 265 results in 102 files，这个因为model中都做了`if let `判断所以对模型中强制解包的比较少

> 示例代码

```
let imageUrl = self.ztImgUrl ?? ""
```

代码是强制解包self.ztImgUrl如果其为nil，则范围""一个空字符串，或者自定义默认值

> 作用

对可选值简单的赋予默认值，继续执行，比较便捷吧

##### optional 小结

​     对属性的初始化如` var baseUrl: String!`, ` var baseUrl: String?`,` var baseUrl: String = ""`大概分这三种，`!`修饰的必须要确认值不能为nil，`?`修饰的需要自使用的时候最好通过上述三种方式去校验值是否为nil，而不通过`baseUrl!`来强制解包。第三种给默认值的话，见过有这样写的，但是会把值存在静态区，占一定的字节数，虽然很少，但使用起来可能少些麻烦。emm，还是偏好第二种吧~ `var baseUrl: String?`这种写法~

​     emm, OC中的应该是nullable 与 nonnull来对应Swift的可选类型.

​     顺便想起来个好久之前的一个小事儿，服务数据错把一个Int类型值传成字符串了，在使用Int()时傻乎乎的强制解包了，导致出错了，测试说用这个数据时有问题就扔给我了~，调试之后说后台数据格式错误，改改服务数据就行了，测试非要说我这开发的有问题，我跟她解释一通，说我先不改，这样还有类似的错误的时候也能发现，但是后来才意识到，我们客户端开发是 保证应用程序不出现问题是为第一责任，即使能发现错误数据，但是导致了异常崩溃，影响使用比一个错误数据影响跟大了，然后就赶紧灰不溜秋的改过来了。



### 闭包

  之前问一个朋友开发时回调传值用的`block`，`delegate`，`notify`哪个多一点？”代理吧“ ，”那不会写的很麻烦么？“ ， ”也没麻烦多少吧，关键是语义清晰“ 。我再一想我们这代码里充斥着各种`CallBack`,示例：

```swift
typealias CallBackS       = (String) -> Void
typealias CallBackSI      = (String,NSInteger) -> Void
typealias CallBackIndex   = (IndexPath) -> Void
typealias CallBackI       = (Int) -> Void
...
```

 朋友回了我两个字 ”喝喝“ ~      心里默念一句：MMP

##### autoclosure

```swift
func autoClosureFunction(_ closure: @autoclosure () -> Bool) {
     if closure() {
         print("True")
      }else{
         print("false")
      }
}
autoClosureFunction(1 > 2) //false
```

简单说，`@autoclosure` 做的事情就是把一句表达式自动地**封装**成一个闭包 (closure)。这样有时候在语法上看起来就会非常漂亮。`@autoclosure` 并不支持带有输入参数的写法，也就是说只有形如 `() -> T` 的参数才能使用这个特性进行简化。另外因为调用者往往很容易忽视 `@autoclosure` 这个特性，所以在写接受 `@autoclosure` 的方法时还请特别小心，如果在容易产生歧义或者误解，更推荐使用`typealias`配 `var callBackS:CallBackIS!`定义使用。

##### @escaping

```swift
    // 简单的alter
    func setupAlter(title:String,message:String,sureBlock:@escaping CallBack){
        let alert = UIAlertController.init(title: title, message: message, preferredStyle: UIAlertControllerStyle.alert)
        let action = UIAlertAction.init(title: "确定", style: UIAlertActionStyle.default){ (action) in
           sureBlock()
        }
        alert.addAction(action)
        self.present(alert, animated: true, completion: nil)
    }
```

   先解释一下定义：@escaping，逃逸闭包，也就是如果这个闭包是在函数执行完后才被调用，调用的地方超过了这函数的范围，叫逃逸闭包。同理如果这个闭包是在这个函数结束前内被调用，就是非逃逸的即noescape。

   这个的话一般会自动提示，emm，也不怎么好解释，[详情戳这里](https://github.com/apple/swift-evolution/blob/master/proposals/0103-make-noescape-default.md).

##### 闭包小结

​    用了block，就难免会出现循环引用问题。其中Swift有两种weak化的方式`weak`,`unowned`，两种的区别就不多解释了，通过[**ios-good-practices**](https://github.com/futurice/ios-good-practices),[**swift-style-guide**](https://github.com/raywenderlich/swift-style-guide)，我还是推荐这种写法

```
    API.repos(username: self.username) { [weak self] result in
      guard let `self` = self else { return }
      self.tableView.reloadData()
    }
```



### 输入控制

#### _

emm, 没错是个下划线，还是从代码开始

```swift
 func saveAge(age: Int){}
 func saveName(_ name: String){}
    
 saveAge(age: 10)
 saveName("Vampire")
```

作用也显而易见，省略方法调用时的形参，避免额外的语义描述，让上下文更清晰，好吧，我编不下去了...

##### 默认值

```swift
 func savePersonData(age: Int, name: String = "Vampire"){}
     
 savePersonData(age: 10) // 可以这样
 savePersonData(age: 10, name: "Vampire")// 也可以这样
```

Swift 这点还是蛮像python的，当方法都给上默认值的时候可以控制传入参数的个数，不用再像OC那样根据不同的参数个数写一大堆嵌套方法，难看死了。这点目前发现`Alamofire`做的比较好。

##### inout

```swift
var value = 50
print(value)  // 此时value值为50
func increment(_ value: inout Int, length: Int = 10) {
    value += length
}
increment(&value)// 会取地址, 即改变原有的值
print(value)  // 此时value值为60，成功改变了函数外部变量value的值
// 所以慎重的 调用 inout 修饰的函数,因为可以改变 原有的值
```

emm，Swift中将数组，字典等很多都改成了值类型，`Struct`结构，不再是值引用。就是赋予另一个变量，改变它，并不会影响原来的值，这样应该不用多解释了吧~ ，而用`inout`修饰的词反到恰恰相反，用来该变原有的值，所以调用用`inout`修饰的方法时，注意其内部可能把你当前的变量给改变了...

##### 反获取枚举类型

这个名儿叫的，唉。看示例吧

```swift
enum QuestionType{
      case DoHomework(pt:paperTpye)    //做作业详情
}
enum paperTpye :String{
    //试卷类型 1 例题 2 练习 3 考试 4 评测
    case ptDefult = ""
    case ptExample = "1"
    case ptExercise = "2"
    case ptExamination = "3"
    case ptEffect = "4"
}

// 调用确认环境变量的类型只需要，传入值就行
QuestionType.DoHomework(pt: paperTpye(rawValue:"2")!)

// 枚举类型时:
case .DoHomework(let pt): //做作业详情
```

这个在处理模块的n多枚举情况的时候，可以只根据服务的值来确认当前的枚举值。极大便利了使用，灵感来自`Alamofire`的Error处理，后来发现斯坦福的白胡子讲师也讲了(捂脸 .



emm~ 先到这里吧，公司的事儿可真够头疼的，也见识到了有些人真的~



