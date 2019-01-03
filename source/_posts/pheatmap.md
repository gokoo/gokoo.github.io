title: R语言下的pheatmap绘制热图
tags:
	- R
	- heatmap
categories:
- R
date: 2018/6/6 18:15:25
---

# Mac系统下R语言下利用pheatmap绘制热图的教程

heatmap在很多领域都有应用，当然绘制heatmap也有很多种方法，MATLAB，PS，Excel以及各种脚本语言，比如Python，JavaScript，R语言。

今天我就要说一说Mac开发环境下利用R语言的pheatmap，读取excel的数据，或者生成数据绘制heatmap。

首先来看看热图长什么样。
![](http://orvzcuqdg.bkt.clouddn.com/WX20180607-091902.png)

# 准备工作
## 下载R语言的安装包
[点击进入R语言下载界面](https://www.r-project.org)

- 点击download R。[download R](https://cran.r-project.org/mirrors.html)
- 然后选择适合自己的CDN，在中国就选择清华的 [清华CDN下载界面](https://mirrors.ustc.edu.cn/CRAN/)
- 选择适合你系统的R语言安装包。[Linux](https://mirrors.ustc.edu.cn/CRAN/bin/linux/).[Windows](https://mirrors.ustc.edu.cn/CRAN/bin/windows/).[Mac](https://mirrors.ustc.edu.cn/CRAN/bin/macosx/)
- **如果上面的看不懂就点击这里查看w3c的教程**[w3c的教程](https://www.w3cschool.cn/r/r_environment_setup.html)
<!--more-->
## 分别安装成功之后运行R语言程序
![](http://orvzcuqdg.bkt.clouddn.com/WX20180607-092819.png)

![](http://orvzcuqdg.bkt.clouddn.com/WX20180607-092920.png)
### 第一步 配置工作目录
使用getwd函数来显示当前工作目录，使用setwd函数改变当前目录:

```R
> getwd()
[1] "/Users/zhangyifei"
> setwd("~/Documents")
> getwd()
[1] "/Users/zhangyifei/Documents"
```
成功把工作目录设置为 *~/Documents* 文件夹下

### 第二步 加载需要的package “pheatmap”
- Mac下，按 command + Alt + i 进入package安装界面，或者 点击menuBar packages&Data -> Package Installer 进入
![](http://orvzcuqdg.bkt.clouddn.com/WX20180607-093546.png)

- 安装成功提示
```R
下载的二进制程序包在
	/var/folders/1j/r1v7hrln35b3c5srf63wbjl80000gn/T//Rtmpn6zgWr/downloaded_packages里
```
- 尝试引入pheatmap，然后继续安装其他所需的package
```R
> library(pheatmap)
错误: package or namespace load failed for ‘pheatmap’ in loadNamespace(i, c(lib.loc, .libPaths()), versionCheck = vI[[i]]):
 不存在叫‘gtable’这个名字的程辑包
试开URL’https://mirrors.eliteu.cn/CRAN/bin/macosx/el-capitan/contrib/3.5/gtable_0.2.0.tgz'
Content type 'application/x-gzip' length 85683 bytes (83 KB)
==================================================
downloaded 83 KB
```
- 提示 缺少 **gtable** 这个安装包，继续在package Installer 搜索并安装
- 继续使用 > library(pheatmap) ，直到没有错误，完成pheatmap的安装。

### 初次使用pheatmap [pheatmap文档-百度文库](https://wenku.baidu.com/view/aa1f3b45336c1eb91a375d71.html)

- 首先运行一下代码，造出一些虚拟数据
```R
#创建数据集test测试矩阵  
test = matrix(rnorm(200), 20, 10)  
test[1:10, seq(1, 10, 2)] = test[1:10, seq(1, 10, 2)] + 3  
test[11:20, seq(2, 10, 2)] = test[11:20, seq(2, 10, 2)] + 2  
test[15:20, seq(2, 10, 2)] = test[15:20, seq(2, 10, 2)] + 4  
colnames(test) = paste("Test", 1:10, sep = "")  
rownames(test) = paste("Gene", 1:20, sep = "")
# print查看虚拟数据的表格
> print(test)
             Test1      Test2       Test3       Test4       Test5       Test6       Test7      Test8      Test9     Test10
Gene1   4.16565797 -0.5332217  2.75177432  0.09050091  2.92299796 -0.03065525  3.25369007  1.6498609  5.5140122 -0.6128477
Gene2   2.92359811  0.8752739  3.66345374 -1.50312709  4.18093373 -0.98068132  2.45325663 -0.2559521  2.6452355  0.9482075
Gene3   2.57691541  0.5655297  3.67327715 -1.16676176  2.86796233  0.84392770  1.58980797 -0.4452452  4.5591699 -0.3777792
Gene4   2.20998817 -0.6476140  1.82619478  0.20842503  5.61892487  2.72355157  2.62418152 -1.4968677  0.6739014 -0.5288066
Gene5   2.38652479  0.9312714  2.54595175  1.14883361  3.20331623 -0.09334568  3.07390285  0.3063662  3.1054117  1.2705683
Gene6   2.39478774 -1.1261661  3.03025086  1.01563976  2.52318186  0.84812055  3.27005713 -0.8367966  1.4205975 -0.1367932
Gene7   2.62131968  0.6992899  2.25204510  1.34989987  2.97693639  0.30943294  3.27633509  0.1267961  2.2810863 -1.2900047
Gene8   3.58112235  0.4807381  1.62681774 -0.33562825  4.40921772 -0.48509264  2.03981473 -1.2606898  3.6922420 -0.6446854
Gene9   0.99267891 -1.2629488  2.06609587 -0.21681098  1.96182398 -1.41969859  3.31023690  0.2180971  2.9437715 -0.3806902
Gene10  4.12715661 -0.2136322  4.62205045 -2.32553157  3.64262245  0.02360678  2.00265816  0.4206732  3.1467020  2.1769087
Gene11 -0.09215522  1.1265504  1.38731747  1.85485547  0.99736984  1.34063368  0.28087822  1.0977324 -1.0247692  2.8091086
Gene12  0.18625357  4.7773888 -0.08486060  2.58186479 -0.78182003  1.90266287  1.00209123  1.4110036  0.1093483  1.3953864
Gene13 -1.16239850  1.3965048  0.09446052  0.87578260 -1.26780043  2.82916652 -0.05420670  0.5785381  0.7610233  1.2991690
Gene14  0.43790594  2.0389307  1.25732857  3.32854010 -0.33689596  2.32263273  1.68448694  0.1837919  0.1148969  1.5774761
Gene15 -0.78805795  5.6452089  1.16313071  6.54379640 -0.22959185  5.15790036 -1.34789990  5.8514132 -0.9923669  5.2847863
Gene16  2.08792577  5.4574904  0.56437760  5.37730138 -0.68379768  4.66291839  0.52126695  6.1721846  0.5563628  7.0962682
Gene17  0.05124655  6.3967121  1.01852622  5.82298443 -0.81731000  6.35169540  0.01920213  6.0312737 -0.7635853  6.6401004
Gene18 -0.31987851  6.7682519 -0.80230549  4.38410875  0.03526136  7.48838461  1.57151778  6.5473611 -2.5019644  4.7268250
Gene19  0.68443755  6.3221438  0.55006319  5.22758579  0.11115899  6.58952720 -1.62195280  6.4833998 -0.5872461  5.5101095
Gene20  0.96231309  5.4082046  0.39004366  4.86237392  0.65172133  5.90754631  1.10989981  4.5998277 -0.3509650  5.4079388
```

- 执行>pheatmap(test),绘制以test为数据的热图
```R
> pheatmap(test)
```
- 以上就能生成文章开始的标准热图了

### 读取excel的数据，并绘制热图
- 安装 **readxl** 
- 读取工作目录下data.xlsx文件 命名为 data
```R
> data <- read_excel("data.xlsx")
```
- 调用pheatmap，绘制标准热图
```R
> pheatmap(data)
```

- 如果出现一下错误是因为图表绘制的有问题
```R
Error in hclust(d, method = method) : 
  外接函数调用时不能有NA/NaN/Inf(arg11)
此外: Warning messages:
1: In dist(mat, method = distance) : 强制改变过程中产生了NA
2: In dist(mat, method = distance) : 强制改变过程中产生了NA
```

### pheatmap的参数以及功能
|参数名|值|作用|
|-|-|-|
|cellwidth|16|方格宽度|
|cellheight|16|方格高度|
|fontsize|9|文字大小|
|color||方格的颜色|
|cluster_rows|TRUE/FALSE|是否显示行的关系树|
|cluster_cols|TRUE/FALSE|是否显示列的关系树|
|legend|TRUE/FALSE|是否显示图例|
示例代码
```R
> pheatmap(test, cellwidth=16, cellheight=16, fontsize= 9 ,  color= colorRampPalette(c("red","blue","orange"))(50), cluster_rows=FALSE,cluster_cols=FALSE)
```
生成以下热图
![](http://orvzcuqdg.bkt.clouddn.com/WX20180607-100326.png)

参考资料
- [那些年画过的热图之pheatmap美化过程](http://www.shengxin.ren/article/107)
- [R绘制热图](https://blog.csdn.net/qazplm12_3/article/details/74516312)
- [解决r - Pheatmap error Error in hclust(d, method = method) : NA/NaN/Inf in foreign function call (arg 11)](http://www.itkeyword.com/doc/5292364481458865620/pheatmap-error-error-in-hclustd-method-method-na-nan-inf-in-foreign-funct)
- [R语言绘制热图——pheatmap](https://blog.csdn.net/sinat_38163598/article/details/72770404)
- [R语言教程](https://www.w3cschool.cn/r/r_basic_syntax.html)