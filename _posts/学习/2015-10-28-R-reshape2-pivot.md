---
layout: post
title: R 利用reshape2实现数据透视表功能
category: 学习
tags: 数据处理
keywords: R 数据透视表
description: 利用R玩转数据透视表
---

这几天处理数据，经常需要把原始数据进行变形以方便运算，比如长长的数据变成宽宽的数据，宽宽的数据变成长长的数据，总而言之，就是要对数据做一个变形整合。如果一个人要变形，那他应该去韩国，如果一个数据要变形，那它应该来reshape2包。虽然R自身带有reshape函数，但自从有了reshape包，reshape函数倒不引人关注了，再后来，reshape2包替代了reshape包，所以我们现在用的都是reshape2包了。

reshape2包还是那位鼎鼎大名的Hadley Wickham 写的，包的质量就不用说了，业界良心啊。这个包对数据的变形有自己的一套思想（流程），甭管你是什么格式的数据（array,list,dataframe）,我先把你揉碎了（melt），之后再把你捏成（cast）各种形状，简直就像用橡皮泥捏东西——捏啥是啥！

废话不多说，reshape2包用到的基本就是melt和 * cast两个函数，melt负责揉数据，*cast负责捏数据。

## 揉数据（melt函数）
   
### 数组
    
对于n维数组（array），melt函数转换后变为n+1列，前n列代表数据的位置信息，第n列代表原始数据中的第n维，最后一列是对应的数值。
    
    a <- array(c(1:23, NA), c(2,3,4))
    a
    , , 1
         [,1] [,2] [,3]
    [1,]    1    3    5
    [2,]    2    4    6
    , , 2
         [,1] [,2] [,3]
    [1,]    7    9   11
    [2,]    8   10   12
    , , 3
         [,1] [,2] [,3]
    [1,]   13   15   17
    [2,]   14   16   18
    , , 4
         [,1] [,2] [,3]
    [1,]   19   21   23
    [2,]   20   22   NA

    head(melt(a,varnames = c(1,2,3) ,value.name = "value"),5)
    1 2 3 value
    1  1 1 1     1
    2  2 1 1     2
    3  1 2 1     3
    4  2 2 1     4
    5  1 3 1     5

注意到原始数据中的NA值也被完整体现出来了，如果想要除去NA值，可以设置na.rm = T.

    melt(a,varnames = c(1,2,3) ,value.name = "value",na.rm = T)

### 列表

melt函数将列表转为两列，第一列是列表元素，第二列是列表元素名称。如果是嵌套列表，第一列仍然是列表元素，其他列仍然是列表元素名称，列表元素越高级越靠后。
   
    melt(list(list(1:3), 1, list(as.list(3:4), as.list(1:2))))
      value L2 L3 L1
    1     1  1 NA  1
    2     2  1 NA  1
    3     3  1 NA  1
    4     1 NA NA  2
    5     3  1  1  3
    6     4  1  2  3
    7     1  2  1  3
    8     2  2  2  3
    
### 数据框

对于数据框，melt函数的调用格式如下
    
    melt(data, id.vars, measure.vars,  variable.name = "variable", ..., na.rm = FALSE, value.name = "value",  factorsAsStrings = TRUE)
   
data是要变换的数据框,id.vars是每个观测值的标识id变量，mearsure.vars是观测值的变量，变量名和数值各占一列。id.vars也可以写作id，mearsure.vars也可以写作mearsure。这两个参数可以只指定其中一个，剩余的列被当成另外一个参数的值；如果两个都省略，数值型的列被看成观测值，其他的被当成id。variable.name指定mearsure变量名列的名称，value.name指定数值列的名称。
    
先借用一张《R语言实战》中的melt示意图吧!

<img src="http://7xo51k.com1.z0.glb.clouddn.com/melt.png-wx" align=center />

我们要揉的原始数据是这样的：

    head(airquality，5)
      ozone solar.r wind temp month day
    1    41     190  7.4   67     5   1
    2    36     118  8.0   72     5   2
    3    12     149 12.6   74     5   3
    4    18     313 11.5   62     5   4
    5    NA      NA 14.3   56     5   5

对它melt后的数据是这样的：

    head(melt(airquality, id=c("month", "day"),variable.name = "type",value.name = "val"))
      month day  type val
    1     5   1 ozone  41
    2     5   2 ozone  36
    3     5   3 ozone  12
    4     5   4 ozone  18
    5     5   5 ozone  NA
    6     5   6 ozone  28

查看一下它的结构，发现type变量里面包含四个水平，也就是原始数据中的ozone，solar.r，wind，temp变量。

    str(melt(airquality, id=c("month", "day"),variable.name = "type",value.name = "val"))
    'data.frame':	612 obs. of  4 variables:
    $ month: int  5 5 5 5 5 5 5 5 5 5 ...
    $ day  : int  1 2 3 4 5 6 7 8 9 10 ...
    $ type : Factor w/ 4 levels "ozone","solar.r",..: 1 1 1 1 1 1 1 1 1 1 ...
    $ val  : num  41 36 12 18 NA 28 23 19 8 NA ...

我们也可以选定两个测量变量

    head(melt(airquality, id=c("month", "day"),measure = c("ozone","wind"),variable.name = "type",value.name = "val"))#选定测量变量为ozone、wind
      month day  type val
    1     5   1 ozone  41
    2     5   2 ozone  36
    3     5   3 ozone  12
    4     5   4 ozone  18
    5     5   5 ozone  NA
    6     5   6 ozone  28

    str(melt(airquality, id=c("month", "day"),measure = c("ozone","wind"),variable.name = "type",value.name = "val")))  #选定测量变量为ozone、wind
    'data.frame':	6 obs. of  4 variables:
    $ month: int  5 5 5 5 5 5
    $ day  : int  1 2 3 4 5 6
    $ type : Factor w/ 2 levels "ozone","wind": 1 1 1 1 1 1
    $ val  : num  41 36 12 18 NA 28

## 捏数据（*cast函数）

数据揉好后，就可以用来捏了。*cast函数分为两种，输出结果为array的为acast函数，输出结果为data.frame的为dcast函数，因为主要用到的是数据框，所以我就主要介绍一下dcast。
   
dcast的调用格式为：dcast(data, formula, fun.aggregate = NULL)，data指melt后的数据，formula指的是重新组合的公式，fun.aggregate指要进行计算的函数。dcast调用结果是公式左边有几个变量就有几列，公式右边的变量为因子型，每个水平当做一列。
    
先试试把揉碎的数据捏成原来的形状吧!

    names(airquality) <- tolower(names(airquality))
    aqm <- melt(airquality, id=c("month", "day"), na.rm=TRUE)
    raw <- dcast(aqm , month + day ~variable)
    View(raw)

raw数据和原始数据airquality一样，数据复原了!
    
其实我们要捏什么形状关键在于公式，公式的组合变化无穷，大家可以发挥自己的想象力！

如果我们要对数据进行函数运算，可以指定fun函数。比如计算每个月里变量的均值，可以采用mean函数。

    monmean <- dcast(aqm , month  ~variable ,mean)
    monmean
        month    ozone  solar.r      wind     temp
    1     5 23.61538 181.2963 11.622581 65.54839
    2     6 29.44444 190.1667 10.266667 79.10000
    3     7 59.11538 216.4839  8.941935 83.90323
    4     8 59.96154 171.8571  8.793548 83.96774
    5     9 31.44828 167.4333 10.180000 76.90000

fun函数我们可以自己编写，比如我们编写一个查看缺失值数量的函数，并将其应用到dcast中。

    nna <- function(x){return(sum(is.na(x)))}#查看缺失值数量
    monmean <- dcast(aqm , month  ~variable ,nna)
    monmean
        month ozone solar.r wind temp
    1     5     0       0    0    0
    2     6     0       0    0    0
    3     7     0       0    0    0
    4     8     0       0    0    0
    5     9     0       0    0    0

结果显示没有缺失数据，因为缺失数据在最初的melt阶段就给移除掉了。如果不移除缺失数据，再捏一遍的效果如下：

     aqm1 <- melt(airquality, id=c("month", "day"))
     monmean <- dcast(aqm1 , month  ~variable ,nna)
     monmean
         month ozone solar.r wind temp
    1     5     5       4    0    0
    2     6    21       0    0    0
    3     7     5       0    0    0
    4     8     5       3    0    0
    5     9     1       0    0    0
    
最后，还有一个recast函数，可以一步到位直接输出你最终需要的效果，比如统计每个月份里各变量的缺失值数量。代码如下
    
    recast(airquality , month ~ variable , id.var = c('month','day') , fun = nna)
        month ozone solar.r wind temp
    1     5     5       4    0    0
    2     6    21       0    0    0
    3     7     5       0    0    0
    4     8     5       3    0    0
    5     9     1       0    0    0
    
reshape2包中的这些函数提供的功能和Excel中的数据透视表功能类似，但是更为灵活，更强大，可以输出你想要的数据形状，并且计算你想要的统计量，而不限于它提供的那些。我想，这就是编程的乐趣之一吧，可以给予你更大的自由度。

## 参考资料

【1】R语言实战

【2】[R语言进阶之4：数据整形（reshape）](http://developer.51cto.com/art/201305/396615.htm)
    
【3】[探索R包reshape2：揉数据的最佳伴侣](http://www.loyhome.com/%E6%8E%A2%E7%B4%A2r%E5%8C%85reshape2%EF%BC%9A%E6%8F%89%E6%95%B0%E6%8D%AE%E7%9A%84%E6%9C%80%E4%BD%B3%E4%BC%B4%E4%BE%A3/)

【4】reshape2包自带文档
