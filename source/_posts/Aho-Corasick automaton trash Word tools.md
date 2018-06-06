title: Aho-Corasick automaton trash Word tools
tags:
	- iOS
	- 算法
categories:
- iOS
date: 2018/5/28 16:40:25
---

# 基于trie树的AC自动机实现脏字过滤

## 需求分析
之前有一个用户发言的功能，然后需要进行敏感词的筛选，因为实时弹幕的量比较大，放到服务端来做数据太多处理起来耗时，所以决定在客户端来做一下。

## 什么是Trie [wikipedia](https://zh.wikipedia.org/wiki/Trie)
trie，又称前缀树或字典树，是一种有序树，用于保存关联数组，其中的键通常是字符串。与二叉查找树不同，键不是直接保存在节点中，而是由节点在树中的位置决定。一个节点的所有子孙都有相同的前缀，也就是这个节点对应的字符串，而根节点对应空字符串。一般情况下，不是所有的节点都有对应的值，只有叶子节点和部分内部节点所对应的键才有相关的值。

trie树常用于搜索提示。如当输入一个网址，可以自动搜索出可能的选择。当没有完全匹配的搜索结果，可以返回前缀最相似的可能。


![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/be/Trie_example.svg/250px-Trie_example.svg.png)


## 什么是AC自动机算法 [wikipedia](https://zh.wikipedia.org/wiki/AC自动机算法)
在计算机科学中，Aho–Corasick算法是由Alfred V. Aho和Margaret J.Corasick 发明的字符串搜索算法，用于在输入的一串字符串中匹配*有限组“字典”中的子串*。它与普通字符串匹配的不同点在于同时与所有字典串进行匹配。算法均摊情况下具有近似于线性的时间复杂度，约为字符串的长度加所有匹配的数量。然而由于需要找到所有匹配数，如果每个子串互相匹配（如字典为a，aa，aaa，aaaa，输入的字符串为aaaa），算法的时间复杂度会近似于匹配的二次函数。
该算法主要依靠构造一个有限状态机（类似于在一个trie树中添加失配指针）来实现。这些额外的失配指针允许在查找字符串失败时进行回退（例如设Trie树的单词cat匹配失败，但是在Trie树中存在另一个单词cart，失配指针就会指向前缀ca），转向某前缀的其他分支，免于重复匹配前缀，提高算法效率。
<!--more-->
## 例
设一个字典中有如下单词：{a,ab,bab,bc,bca,c,caa}.
下方的图是用AC自动机算法由该词典构造而成的一棵Trie树，其中每个节点都有一条从根节点到它的唯一路径，代表一个单词。
在这种数据结构中，字符串的每一个前缀都有一个节点来表示（详见Trie）。所以如果（bca）在字典中，则会存在（bca），（bc），（b）和（）对应的节点。如果该节点表示的字符串在字典中存在，则该节点为一个蓝色节点，否则为一个灰色节点。
树中的黑色有向边代表起点是终点的“父亲”（即起点对应字符串增加一个字符可得终点对应字符串），例如从（bc）有一条指向（bca）的黑色有向边。
树中的蓝色有向边（后缀节点）代表终点对应字符串是起点对应字符串的最大严格后缀。例如对于一个节点（caa），它的严格后缀为（aa），（a）和（），其中在图中且最长的为（a），所以（caa）有一条指向（a）的蓝色有向边。一个节点的蓝色有向边可以在线性时间内通过重复遍历节点父亲节点的蓝色有向边直到横移节点（the traversing node）有一个属于目标节点前缀的孩子。
树中的绿色有向边（字典后缀节点）代表终点是起点经过蓝色有向边到达的第一个蓝色节点（即字典中存在终点对应字符串）。例如（bca）有一条绿色边连向（a），因为（a）是（bca）通过蓝色有向边到达的第一个蓝色节点，路径为（bca）→（ca）→（a）。绿色有向边也可以在线性时间内通过遍历蓝色有向边直到找到一个蓝色节点，并用记忆化的方法计算。
***
字典 {a, ab, bab, bc, bca, c, caa}

|节点|是否在字典中|后缀节点(蓝色有向边)|字典后缀节点(绿色有向边)|
|-|-|-|-|
|()|-|||
|(a)|+|()|
|(ab)|+|(b)|
|(b)|	–	|()|	
|(ba)|	–	|(a)|	(a)|
|(bab)|	+	|(ab)|	(ab)|
|(bc)	|+|	(c)	|(c)|
|(bca)|	+	|(ca)|	(a)|
|(c)|	+|	()	|
|(ca)|	–	|(a)|	(a)|
|(caa)|	+	|(a)|	(a)|

![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/62/Ahocorasick.svg/220px-Ahocorasick.svg.png) 图例


在每一步中，算法先查找当前节点的“孩子节点”，如果没有找到匹配，查找它的后缀节点(suffix)的孩子，如果仍然没有，接着查找后缀节点的后缀节点的孩子, 如此循环, 直到根结点，如果到达根节点仍没有找到匹配则结束。
当算法查找到一个节点，则输出所有结束在当前位置的字典项。输出步骤为首先找到该节点的字典后缀，然后用递归的方式一直执行到节点没有字典前缀为止。同时，如果该节点为一个字典节点，则输出该节点本身。
## 实现思路
首先拿到屏蔽词词库，是一个txt文档，想办法把词库包装成为自己需要的显示格式，因为是开发iOS版本的，所以第一就想到的是plist文件。所以包含以下几步
- 1、拿到txt，读出字符串
- 2、正则出去不相关的符号，分隔成一个一个的词组
- 3、把词组包装成有序trie树的结构
- 4、本地保存trie树结构
- 5、实现判断用户输入词组的检索方法
- 6、词库的更新

### 第一、二步实现的方法很多，简单的字符串操作以及文件读取操作
### 第三步 单步操作
这里为了方面后面的判断，每一个词组插入之后会在尾端加一个end的flag，表示这个单词索引是否结束
```c
//添加单个String词到被操作的字典里
-(void)insertStringTo:(NSMutableDictionary *)Dic withString:(NSString *)string
{
    //取出字符
    if (!string.length) {
        return;
    }
    NSString *singleStr = [string substringWithRange:NSMakeRange(0, 1)];
    //判断当前字典是不是包含这个key
    if ([Dic.allKeys containsObject:singleStr]) {
        NSMutableDictionary *operationDic = Dic[singleStr];
        [self insertStringTo:operationDic withString:[string substringFromIndex:1]];
    }
    else{
        NSMutableDictionary *newDic = [NSMutableDictionary dictionary];
        //如果当前敏感词检索到最后一位，为当前的字典加一个end标识符
        if (string.length == 1) {
            [newDic setObject:[NSNumber numberWithBool:YES] forKey:@"isEnd"];
        }
        if (string.length > 0) {
            [self insertStringTo:newDic withString:[string substringFromIndex:1]];
        }
        [Dic setObject:newDic forKey:singleStr];
    }
}
```

### 第四步 本地储存
```c
//把词库以及版本的字典包装好，写入到文件里
-(void)updateTrieplistWithDic:(NSMutableDictionary *)dataDic withVerisionString:(NSString *)verisionString;
{
    //最后要进行读写操作的字典
    NSMutableDictionary *finalDic = [NSMutableDictionary dictionary];
    //获取plist文件的路径
    NSString *path=[[NSBundle mainBundle]pathForResource:@"Trie" ofType:@"plist"];
    //添加版本字段
    if (verisionString) {
        [finalDic setValue:verisionString forKey:@"verision"];
    }else{
        [finalDic setValue:self.verision forKey:@"verision"];
    }
    [finalDic setValue:dataDic forKey:@"trieDic"];
    //最后完整的字典写到plist文件里
    [finalDic  writeToFile:path atomically:YES];
}
```
### 第五步 实现判断用户输入词组的检索方法
- 首先除去用户的噪音数据，比如说“*（……&%￥#”这样的符号，以及空格(正则方法)

```C
/**
 *  去除用户输入的噪音数据
 *
 *  @param noMutestring   需要匹配的字符串(非可变类型)
 *
 *  @return 取出之后的结果字符串
 */
- (NSString *)ResultString:(NSString *)noMutestring;
{
    NSMutableString *string = [NSMutableString stringWithFormat:@"%@",noMutestring];
    NSString *regexStr = @"[/\\s+/g~`:@#$%^&\\【】：、》《“.！。￥*……·()_\\+{}\\|<>\\/\\\[\\]]";
    
    //生成正则匹配方法
    NSRegularExpression *regex = [NSRegularExpression regularExpressionWithPattern:regexStr options:NSRegularExpressionCaseInsensitive error:nil];
    //根据正则获取可变字符串的匹配结果数组(NSTextCheckingResult *)
    NSArray * matches = [regex matchesInString:string options:0 range:NSMakeRange(0, [string length])];
    //遍历string 根据匹配结果删除噪音数据
    for (int i = 0; i < matches.count; i ++) {
        //获取匹配结果
        NSTextCheckingResult *checkResult = matches[i];
        //获取range
        NSRange tempRange = checkResult.range;
        //利用可变数组按照range删除的方法删除噪音数据
        [string deleteCharactersInRange:NSMakeRange(tempRange.location - i, tempRange.length)];
    }
    //返回结果字符
    return [NSString stringWithString:string];
}
```

- 然后把干净的字符串与包装好的trie树进行对比
```C
//①.附属方法->仅仅判断是否包含敏感词的方法
-(BOOL)checkDic:(NSDictionary *)Dic IsTrashWordsInString:(NSString *)InputString
{
    NSString *noNoiseWordsString = [self ResultString:InputString];
    if (noNoiseWordsString.length == 0) { //输入!号时崩溃！！！！！
        self.isResult = NO;
        return self.isResult;
    }
    
    NSRange range = NSMakeRange(0, 1);
    if (InputString.length > 0) {
        NSString *beignCharacter = [noNoiseWordsString substringWithRange:range];
        //说明检索到了
        if ([Dic.allKeys containsObject:beignCharacter]) {
            //拿字典
            NSDictionary *resutDic = Dic[beignCharacter];
            //如果检索到完成标识符，返回YES
            if ([resutDic.allKeys containsObject:@"isEnd"]) {
                self.isResult = YES;
            }
            //否则取出字典，截取字符串之后继续判断
            else{
                [self checkDic:resutDic IsTrashWordsInString:[noNoiseWordsString substringFromIndex:1]];
            }
        }
        //否则从头开始截取字符串之后继续判断
        else{
            _originString = [_originString substringFromIndex:1];
            if (_originString.length == 0) { //输入减号崩溃！！！
                return NO;
            }
            [self checkDic:self.trieDictionary IsTrashWordsInString:[_originString substringFromIndex:1]];
        }
        
    }
    //默认返回NO(没判断当然不知道包含不包含||检索完毕没有一个YES就是不包含)
    
    return self.isResult;
}
```
### 词库的更新
更新的实现，在本地化之前在里面写上词库版本号，后端分离一个接口用于检查版本和新词库的添加
```C
-(void)checkAndUpdate
{
    NSDictionary *params = @{@"mask_version":self.verision};
    
    [HRRequest requestAppServer:URL_Check_mask_Verision
                        parameter:params
                          success:^(NSDictionary *dic) {
                              if ([dic.allKeys containsObject:@"new_mask_word"]) {
                                  NSArray *newMaskWordsArr = dic[@"new_mask_word"];
                                      //不管怎么样都把返回数组进行遍历然后添加到trie字典然后写入文件
                                  for (NSString *temp in newMaskWordsArr) {
                                      //对字典进行操作，添加新的词
                                      [self insertStringTo:self.resultDic withString:temp];
                                  }
                                  //forin循环完毕，写入到plist文件并且把服务器获取到的版本号进行赋值更新
                                  if ([dic.allKeys containsObject:@"new_version"]) {
                                      NSString *newVerision = dic[@"new_version"];
                                      if ([newVerision integerValue] != 0) {
                                          [self updateTrieplistWithDic:self.resultDic withVerisionString:newVerision];
                                          //替换缓存内的词库
                                          self.trieDictionary = self.resultDic;
                                          //替换版本
                                          self.verision = newVerision;
                                      }
                                  }
                              }
                          } failure:^(NSError *error) {
                              NSLog(@"%@",error.userInfo);
                              
                          }];
}
```
参考来源
- Black, Paul E. trie. Dictionary of Algorithms and Data Structures. 国家标准技术研究所. 2009-11-16. 
- 2.0 2.1 Franklin Mark Liang. Word Hy-phen-a-tion By Com-put-er (PDF) (Doctor of Philosophy论文). Stanford University. 1983 [2010-03-28]. （原始内容 (PDF)存档于2010-05-19）.
- Knuth, Donald. 6.3: Digital Searching. The Art of Computer Programming Volume 3: Sorting and Searching 2nd. Addison-Wesley. 1997: 492. ISBN 0-201-89685-0.
- An Implementation of Double-Array Trie
- Aho, Alfred V.; Corasick, Margaret J. Efficient string matching: An aid to bibliographic search. Communications of the ACM. June 1975, 18 (6): 333–340. MR 0371172. doi:10.1145/360825.360855.
- 敏感词过滤算法之Aho-Corasick算法 https://www.cnblogs.com/zyguo/p/4705270.html