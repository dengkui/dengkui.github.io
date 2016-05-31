---
layout: post
title: R data.table包学习
category: 学习
tags:  数据处理        
keywords: R data.table  大数据处理
description: R语言data.table包学习
---

虽然R是一款数据分析的利器，但在面对较大的数据量时，R还是有很多不足。仅仅读取一个大文件就足以使R卡住不动，利用传统的方法进行数据处理简直不敢想象。

为了解决数据量较大的问题，R中涌出了一系列包，本篇文章介绍data.table包，官方的介绍是快速的数据读取，快速地聚合排序，快速地分组运算，以及灵活自然的语法。data.table可以称为加强版的data.frame，因而适用于data.frame的函数也适用于data.table。

## 数据读入

data.table包采用fread函数，函数的语法格式为：`fread(input，……）`,其中input是读入的文件对象，此函数可以自动判断分隔符类型，自动判断首行是否是列标题，同时默认读入时字符型变量不会变为因子型。

    user_actions <- fread('D:/Project/data/user.csv')

## 数据处理

对于数据的处理，data.table包提供了一个非常简洁的通用格式：DT[i,j,by]，意思是:对于数据集DT，选取子集行i,通过by分组计算j。i设定数据的选取条件，j设定结果的计算方式，by设定数据的分组情况。通过这个，我们可以在一行代码中很方便地完成处理过程。

本篇文章以下部分采用`mtcars`数据作为示例说明。

先把数据集转为data.table格式：

    mtcars_dt <- data.table(mtcars)
    class(mtcars_dt)
    [1] "data.table" "data.frame"

### 筛选

1. 行筛选：直接采用逻辑语句

        head(mtcars_dt[cyl==8])
            mpg cyl  disp  hp drat   wt  qsec vs am gear carb
        1: 18.7   8 360.0 175 3.15 3.44 17.02  0  0    3    2
        2: 14.3   8 360.0 245 3.21 3.57 15.84  0  0    3    4
        ……

        head(mtcars_dt[cyl==8 & carb ==3])
            mpg cyl  disp  hp drat   wt qsec vs am gear carb
        1: 16.4   8 275.8 180 3.07 4.07 17.4  0  0    3    3
        2: 17.3   8 275.8 180 3.07 3.73 17.6  0  0    3    3
        ……

2. 列筛选：直接输入列名，注意`.()`格式

        head(mtcars_dt[,.(mpg,cyl,hp)])
        mpg cyl  hp
        1: 21.0   6 110
        2: 21.0   6 110
        ……

### 选取子集

选取子集仍然采用subeset函数，语法格式为：subset(x, subset, select)，x是data.table对象，subset是行满足条件，select是列满足条件

    subset(mtcars_dt,cyl==8,select =c('mpg','cyl','disp'))
       mpg cyl  disp
    1: 10.4   8 460.0
    2: 10.4   8 472.0
    ……

### 排序

排序采用`setorder`函数，输入待排序的列名，默认升序，降序列名前加`-`

     head(setorder(mtcars_dt,mpg,-hp))
        mpg cyl disp  hp drat    wt  qsec vs am gear carb
    1: 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4
    2: 10.4   8  472 205 2.93 5.250 17.98  0  0    3    4
    3: 13.3   8  350 245 3.73 3.840 15.41  0  0    3    4
    4: 14.3   8  360 245 3.21 3.570 15.84  0  0    3    4
    5: 14.7   8  440 230 3.23 5.345 17.42  0  0    3    4
    6: 15.0   8  301 335 3.54 3.570 14.60  0  1    5    8

### 增删变量

添加变量有三种语法格式：
  
    DT[i, LHS:=RHS, by=...]#适用单变量添加
    DT[i, c("LHS1","LHS2") := list(RHS1, RHS2), by=...]#双变量添加
    DT[i, `:=`(LHS1=RHS1,LHS2=RHS2,           ...), by=...]#多变量添加，注意`:=`

    head(mtcars_dt[,`:=`(mpg1=1/mpg,new=cyl+gear)])
        mpg cyl disp  hp drat    wt  qsec vs am gear carb       mpg1 new
    1: 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4 0.09615385  11
    2: 10.4   8  472 205 2.93 5.250 17.98  0  0    3    4 0.09615385  11
    ……

删除变量，变量:=NULL，即可

    head(mtcars_dt[,mpg1:=NULL])
        mpg cyl disp  hp drat    wt  qsec vs am gear carb new
    1: 10.4   8  460 215 3.00 5.424 17.82  0  0    3    4  11
    2: 10.4   8  472 205 2.93 5.250 17.98  0  0    3    4  11
    ……

### 分组汇总

分组汇总只需在`by`指定分组变量，在`j`指定计算函数即可

    head(mtcars_dt[,.(mean_mpg = mean(mpg),num=length(mpg),sum_disp = sum(disp)),by=.(cyl,vs)])#统计分组下的mpg均值，disp的总和,分组数据个数num
       cyl vs mean_mpg num sum_disp
    1:   8  0 15.10000  14   4943.4
    2:   6  1 19.12500   4    818.2
    3:   6  0 20.56667   3    465.0
    4:   4  1 26.73000  10   1036.2
    5:   4  0 26.00000   1    120.3

data.table有一个特殊的变量`.N`可以直接计算分组的观测值个数。

    head(mtcars_dt[,.N,by=.(cyl,vs)])
        cyl vs  N
    1:   8  0 14
    2:   6  1  4
    3:   6  0  3
    4:   4  1 10
    5:   4  0  1

### 数据合并

数据合并仍然采用`merge`函数，只是合并对象必须是data.table类型，这样才能发挥出data.table的威力!

    merge(x, y, by = NULL, by.x = NULL, by.y = NULL,all = FALSE, all.x = all, all.y = all, sort = TRUE, suffixes = c(".x", ".y"),allow.cartesian=getOption("datatable.allow.cartesian"), ...)

函数使用同`merge`一样，不再赘述。

### 管道操作

data.table可以像dplyr包一样使用`%>%`操作符

    library(dplyr)
    mtcars_dt[(disp > 120 & mpg > 20),.(mdisp = mean(disp),mmpg = mean(mpg)),by=.(cyl,vs,am)]%>%subset(am==1&mmpg>22)
       cyl vs am mdisp mmpg
    1:   4  0  1 120.3   26

    mtcars_dt[(disp > 120 & mpg > 20),.(mdisp = mean(disp),mmpg = mean(mpg)),by=.(cyl,vs,am)]%>%subset(select=c('cyl','mmpg'))
    cyl mmpg
    1:   6 21.0
    2:   6 21.4
    3:   4 21.4
    4:   4 22.9
    5:   4 26.0


### 其他操作

快速查找某一区间： %between%

    mtcars_dt[disp %between% c(150,200)]
        mpg cyl  disp  hp drat    wt  qsec vs am gear carb new
    1: 17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4  10
    2: 19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4  10
    3: 21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4  10
    4: 21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4  10

判断两个数据框是否相同:all.equal,返回值为‘true'或两个数据框不同的地方

    dt1 <- data.table(A = letters[1:10], X = 1:10, key = "A")
    dt2 <- data.table(A = letters[5:14], Y = 1:10, key = "A")
    identical(all.equal(dt1, dt1), TRUE)

判断向量是否重复：duplicated。返回逻辑向量，有主键时，对主键列做是否重复判断，无主键时，对所有列做判断。利用这个函数，我们就可以轻松地对数据去重。

    DT <- data.table(A = rep(1:3, each=4), B = rep(1:4, each=3), C = rep(1:2, 6), key = "A,B")
    duplicated(DT, by="B")#返回是否重复的逻辑向量

设置主键:setkey(DT, x)，DT是数据框，x是作为主键的列名称。
显示已有数据信息，tables()
返回数据框不同的行数：uniqueN(DT)

除了上面的函数方法，data.table还提供了reshape2包中的`melt`,`dcast`函数。

## 后记

对于一般的数据，R中自带的函数完全够用，如果想简便点可以采用`dplyr`包和`data.table`包，当数据较大时，就必须使用`data.table`包了，而且`data.table`包使用起来也较为简单方便，一行代码就可以搞定很多事！实在是太方便了！向发明这些包的大神致敬！


## 参考资料

【1】 [R高效数据处理包dplyr和data.table，你选哪个？](http://www.xueqing.tv/cms/article/213)

【2】 [Learning Packages_data.table](http://rstudio-pubs-static.s3.amazonaws.com/84200_1007768f9e0a44929db84461db4859f7.html)

【3】[R语言-data.table包](http://blog.csdn.net/qq_27755195/article/details/50919442)

【4】[R语言中的数据操作](http://westerly-lzh.github.io/cn/2014/05/Using-Data-Table/)
