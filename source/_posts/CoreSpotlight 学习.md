title: CoreSpotlight 学习
tags:
	- iOS
	- CoreSpotlight
categories:
- iOS
date: 2016/5/13 19:46:25
---

# 简介
&lt;CoreSpotlight&gt;是 **iOS9 / Xcode7** 提供的一组新的API来帮助你建立起你的应用中的索引。CoreSpotlight是用来处理用户数据
要使用CoreSpotlight首先要在工程->Build Phases->Link Binary With Libraries->搜索CoreSpotlight引入Framework
# 搜索结果的初始化以及添加
>**CSSearchableItemAttributeSet** ：声明CSSearchableItem包含的元数据。

#### CSSearchableItemAttributeSet 方法 [点击此处查看效果](http://e.hiphotos.baidu.com/image/pic/item/42166d224f4a20a42adfcc5c99529822730ed06c.jpg)

```objc
/*应用内搜索object，想搜索到多少种界面就要创建多少个set ，每个set都要对应一个item*/
    CSSearchableItemAttributeSet *firstSet = [[CSSearchableItemAttributeSet alloc] initWithItemContentType:@"firstSet"];
//标题
    firstSet.title = @"搜索结果1";
//详细描述
    firstSet.contentDescription = @"这是spotlight第一条搜索结果";
//关键字，搜索索引  经测试长的字符串spotlight可以自动模糊搜索，所以关键词
    NSArray *firstSeachKey = @[@"CoreSpotlight",@"结果",@"测试",@"搜索"];
    firstSet.contactKeywords = firstSeachKey;
//缩略图
    firstSet.thumbnailData = [NSData dataWithContentsOfURL:[NSURL URLWithString:@"http://static0.tuicool.com/profile/05fcaf20abfa_1479439428.jpg"]];
```

> **CSSearchableItem** 是CSSearchableItemAttributeSet的内容

#### CSSearchableItem 方法

```objc
//UniqueIdentifier每个搜索都有一个唯一标示，当用户点击搜索到得某个内容的时候，系统会调用代理方法，会将这个唯一标示传给你，以便让你确定是点击了哪一个，方便做页面跳转
//domainIdentifier搜索域标识，删除条目的时候调用的delegate会传过来这个值
    CSSearchableItem *firstItem = [[CSSearchableItem alloc] initWithUniqueIdentifier:@"thirdItem" domainIdentifier:@"third" attributeSet:firstSet];
```

> CSSearchableIndex 将初始好的items加入到搜索数据源里

```objc
[[CSSearchableIndex defaultSearchableIndex] indexSearchableItems:@[firstItem] completionHandler:^(NSError * _Nullable error) {
        if (error) {
            NSLog(@"设置失败%@",error);
        }else{
            NSLog(@"设置成功");
        }
    }];
```
<!--more-->
# 用户点击搜索结果的交互
> 类似remote消息，在"AppDelegate.m"里面实现以下方法

```objc
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray * _Nullable))restorationHandler{
    
    NSString *dentifier  = userActivity.userInfo[@"kCSSearchableItemActivityIdentifier"];
    //这里做一个封装好的来处理交互消息
    return YES;
}
```

# 索引的删除
>CSSearchableIndex 提供了三种方法来删除索引

```objc
[CSSearchableIndex defaultSearchableIndex]//系统SearchableIndex的单例
- (void)deleteSearchableItemsWithIdentifiers:(NSArray<NSString *> *)identifiers completionHandler:(void (^ __nullable)(NSError * __nullable error))completionHandler; 

- (void)deleteSearchableItemsWithDomainIdentifiers:(NSArray<NSString *> *)domainIdentifiers completionHandler:(void (^ __nullable)(NSError * __nullable error))completionHandler;

 - (void)deleteAllSearchableItemsWithCompletionHandler:(void (^ __nullable)(NSError * __nullable error))completionHandler; 
```

# 总结
>关于CSSearchableItemAttributeSet

1. contactKeywords的搜索索引，经过测试，**一段长文字spotlight可以自动模糊匹配**，比如将key设为“这是spotlight第一条搜索结果”输入“这是”、“zhes”、“第一条”···都会匹配搜索结果，key的长度没有进行验证
2. 经测试缩略图的添加可以采用URL的方式，不用唤醒app，应该是系统自动根据URL读取其中的data
3. 通过查看CSSearchableItemAttributeSet的头文件推测搜索结果支持国际化语言

>关于UniqueIdentifier和domainIdentifier的设定

1. 可以通过统一的前缀来设置关联，用于区分跳转以及删除和关闭某些敏感信息的搜索结果
2. 通过UniqueIdentifier携带的信息来区分类别以及执行跳转至后的动作

>关于搜索结果的插入

1. 通过把玩新浪微博以及即刻脉脉之后发现，搜索的结果应该是之前打开app加载好的数据，相当于做了一个缓存，而之前没有加载的数据是不会搜索到的，所以猜测这个搜索是不能唤起app内部执行网络请求的。比如我之前微博没有关注“谢娜”，在spotlight搜索“谢娜”没有搜索结果，当我在微博里点进去谢娜的主页之后再出来搜索结果就出现了。
2. 插入逻辑可以在数据请求成功后对不同的Model异步进行thumbData、title、description的处理

>关于搜索结果的删除和关闭app在spotlight的显示

1. 初步设想首先根据UniqueIdentifier和domainIdentifier对搜索结果进行分类，删除的时候调用CSSearchableIndex的方法统一删除，并且在插入搜索结果的工具类里进行Model屏蔽

## 参考文档
[iOS 9之应用内搜索(CoreSpotlight）API - CocoonJin的博客](http://www.cnblogs.com/CocoonJin/p/4703366.html)
[iOS 9 应用内搜索(CoreSpotlight）的使用 - 魅风追影](http://www.cnblogs.com/KingQiangzi/p/4861851.html)
[Apple Core Spotlight 文档](https://developer.apple.com/reference/corespotlight)

