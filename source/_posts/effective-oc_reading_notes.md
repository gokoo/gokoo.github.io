title: 52 Special Ways to Improve Your iOS Programs 读书笔记
tags:
	- iOS
	- 读书笔记
categories:
- iOS
date: 2018/5/28 10:00:25
---

本文只整理了对自己觉得有帮助的知识点

# 第一章 熟悉Objective-C

### 了解OC的起源 
- OC使用“消息结构”是有Smalltalk演化而来
- OC与函数调用的语言区别是“OC运行时代码由环境决定，函数语言由编译器决定”

### 多用类型常量，少用#define预处理指令
- #define没有指定变量类型，最好使用static const [变量类型] [变量名] [变量值]
- 如果定义的常量要在全局使用在前面加extern并且命名的时候要遵循规则，防止命名冲突

### 枚举表示状态、选项、状态码
- 枚举加switch表示不同状态，最好不要实现default方法，防止新加的switch分支漏掉实现方法


# 第二章 对象、消息、runtime


### 理解”属性这一概念“
- @property默认生成 setter和getter一对存取方法
- ABI(Application Binary Interface)：应用程序二进制接口
- @dynamic关键字告诉编译器不生成存取方法
- atomic/nonatomic 原子性/非原子性 多线程共同操作一个属性会造成取值不准确，但是iOS不用atomic，因为会造成严重的性能问题，并且没法保证安全，在iOS平台会用”锁“来处理。但是MACOSX可以，没有性能瓶颈。
- assign 用于纯量类型，简单的赋值操作
- strong 表适拥有关系
- weak 表示非拥有关系，当指定的对象dealloc的时候，属性值会被清空
- unsafa_unretained  和assign相同，适用于object，对象dealloc的时候不会被清空，和weak有区别
- copy set方法不存新值，get方法取一份copy的值
- getter=<"name"> 指定get方法名
```c
@property (nonatomic, getter=isOn) BOOL on;
```
- setter=<"name"> 指定set方法名（不常见）
<!--more-->
### 在对象内部尽量直接访问实例变量
- 直接访问实例变量比较快，但是不会触发KVO和懒加载（lazy loading）
### 理解”对象等同性“（判断两个对象是不是相同的）
- isEqual 判断两个对象指针是不是在一块内存地址，是指同一个”人“
- hash 哈希值是判断两个对象是不是一样的东西，是指”两个一样的人，比方双胞胎“
- 实现自己的equal方法思路
```
首先判断是不是一个class
判断主键
分条判断属性是否一样(判断的深度按需求决定)
```
- hash的时候应当使用计算比较快而且碰撞率比较低的算法

### 在既有类使用关联对象存放自定义数据（runtime）
|关联类型|等效的@property属性|
|-|-|
|OBJC_ASSOCIATION_ASSIGN|assign|
|OBJC_ASSOCIATION_RETAIN_NONATOMIC|noatomic,retain|
|OBJC_ASSOCIATION_COPY_NONATOMIC|noatomic,copy|
|OBJC_ASSOCIATION_RETAIN|retain|
|OBJC_ASSOCIATION_COPY|copy|

*常用方法*
```C
//管理关联对象
void objc_setAssoicatedObject(id object,void *key. id value. object_AssociationPolicy policy)
//以给定的键和策略为某对象设置值
id objc_getAssociatedObject(id object, void *key)
//依据给定的键从某对象中获取相应的关联值
void objc_AssociatedObjects(id object)
```
- 非必要情况下少使用，因为不好排查bug
### 理解objc_msgSend作用
- 给对象发送消息的函数，实际上是一个参数可变的函数，可以接受两个或者两个以上的参数，第一个代表消息的接收者，第二个参数代表方法，之后就是消息中的那些参数。
```C++
//给对象发消息
id objc_msgSend(id self, SEL cmd, ...)
//编译器转化之后的函数表示为
id  returnValue = objc_msgSend(someObject, @slector(messageName:), parameter);
```

- objc_msgSend会在接受者的“方法列表(list of methods)”里面搜寻已注册的方法，如果没找到就*沿着继承体系向上查找*，如果最终还找不到就执行"*消息转发message forwarding”

### 理解消息转发机制
- unrecognized selector sent to instance 是向对象发送了一个没法解析的方法，启动了消息转发的机制，并将消息转发给了NSObject的默认实现。
- 上面的异常是NSObject 的“doesNotRecognizeSelector”方法抛出的

当obj接收了一个没有实现的方法
- 首先是 + (BOOL)resolveInstanceMethod:(SEL)Selector（配合dynamic实现setter和getter）,如果这个方法没有处理，那么转到 - (id)forwardingTargetForSelector:(SEL)selector (备援接收者) 搜寻是否有预处理别的obj来实现此方法，如果没有则转到，- (void)forwardInvocation:(NSInvocatron:)invocation
- 这三步都有机会来处理方法，步骤越往后，处理的代价越大，最好在第一步就能处理完

### 用“方法调配技术”调试“黑盒方法”（method_swizzing）
- method_swizzing 是交换方法实现的指针
```C++
//交换方法
void method_exchangeImplementations(Method m1, Method m2)
//取selector方法
Method class_getInstanceMethod(Class aClass, SEL Selector)
```
- 可以用于调试，增加日志log，但是不建议滥用

