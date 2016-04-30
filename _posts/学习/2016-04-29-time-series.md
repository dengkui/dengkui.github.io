---
layout: post
title:  时间序列数据格式处理
category: 学习
tags:  数据处理        
keywords: 时间序列  time-series R Python 
description: 用R和Python处理时间序列格式数据的总结
---

最近一段时间，和时间序列打上了交道，当然，原始的数据免不了**脏乱差**，是不能直接使用的，必须经过一番清洗整理，才能用于最终的分析。

对于时间序列数据，**R**和**Python**都有一整套的处理方法，从原始数据到建模预测，这里仅仅对两个软件中的时间序列数据格式处理做一总结，以供后续翻看之用。

## **R**

R中用来表示日期和时间的对象有三类：

1. Date　　　 仅仅表示日期
2. POSIXct　　表示时间与1970年1月1日中午12：00的时间间隔换算为秒后得到的数据
3. POSIXlt　　POSIXlt对象将日期和时间独立存储在不同的向量中。向量包括秒(0-61),分(0-59),时(0-23),月日(1-31),月(0-11),年(自1900起经过的年份)，周日(0-6),年日(0-365)和isdst(表明是否采用夏时令）

基于这三种时间表示方式，我们就可以对时间变量进行一系列的方便操作。

通常数据中的时间变量不是字符型就是数值型，而这两种类型都不便于我们对时间进行相关操作，我们要做的第一步就是将字符型或数值型变量转换为以上三种类别，以上三种类别中我们经常使用的是Date和POSIXlt两种。

* 变量转为POSIXct和POSIXlt类型

  as.POSIXlt(x, tz = "", format, ...)，适用字符型变量
  
        as.POSIXlt('20150430',format='%Y%m%d')
        [1] "2015-04-30 CST"
        as.POSIXlt('20150430',format='%Y%m%d')-24*3600
        [1] "2015-04-29 CST"
        as.POSIXlt('20150430',format='%Y%m%d',tz='UTC')-1
        [1] "2015-04-29 23:59:59 UTC"

  as.POSIXlt(x, tz = "", origin, ...)，适用数值型变量，origin是指起始时间，注意指定时区，否则返回的是CST时区的时间，要转换时间戳需要将origin设定为`1970-01-01 00:00:00`

        as.POSIXlt(24*3600,origin = "2016-04-28 13:00:00 UTC",tz='UTC')
        [1] "2016-04-29 13:00:00 UTC"


* 变量转为时间变量

  strptime(x,format)，将字符型变量转为POSIXlt格式的时间格式

  x是要转换的字符型变量，format是转换后的时间格式。表现形式类似于%y %m %d,默认的是%Y-%m-%d %H:%M:%S，利用strptime函数，我们可以轻松把字符型变量转成我们需要的时间格式。format类型的具体解释如下：

        %a  缩写的weekday
        %A  全称的weekday
        %b  缩写的月份
        %B  全称的月份
        %c  日期和时间。输出格式为  "%a %b %e %H:%M:%S %Y"  `format(Sys.time(), "%c")`
        %C  世纪（00-99），年份/100的整数部分
        %d  月份中的日期，格式为01–31
        %D  %m/%d/%y形式的日期格式，比如："04/29/16"%e
        %e  月份中的日期，格式为1-31
        %F  ISO 8601的时间格式： %Y-%m-%d ，比如："2016-04-29"
        %g  The last two digits of the week-based year (see %V). (Accepted but ignored on input
        %G  The week-based year (see %V) as a decimal number. (Accepted but ignored on input.)
        %h 同%b.
        %H 小时，24小时制表达形式：00-23 a special exception strings such as 24:00:00 are accepted for input, since ISO 8601 allows these.
        %I 小时，12小时制表达形式为 (01–12)
        %j 一年中第几天，表达形式为：001–366
        %m 月份，表达形式为：01–12
        %M 分钟，表达形式为：00–59
        %p AM/PM 指示，同%I使用，不可以同%H使用
        %r 12小时制时间，如"11:21:14 PM"
        %R 同%H:%M
        %S 秒数(00–61)
        %T 等同于%H:%M:%S.
        %u 1-7形式的星期表达，星期一是1
        %U 一年中第几个星期，采用周日作为第一天
        %V 采用ISO 8601定义的一年中的第几个星期，(01-53),如果包含1月1号的星期有至少4天，那么定义为本年的第一个星期，否则上一年的最后一个星期
        %w 0-6的星期表达，星期天为0
        %W 一年中的第几个星期，采用星期一作为一周第一天，英国的惯例
        %x 日期表达，形式为："%y/%m/%d"
        %X 时间，表达形式为"%H:%M:%S"
        %y 去除年份中的世纪标识的年份表达式，比如：‘16’
        %Y 完整年份表达
        %z Signed offset in hours and minutes from UTC, so -0800 is 8 hours behind UTC. Values up to +1400 are accepted as from R 3.1.1: previous versions only accepted up to +1200. (Standard only for output.)
        %Z 时区缩写

* 变量转为日期变量
 
我们可以采用as.Date()函数将变量转为日期变量，转换后的日期变量可以直接做日期的加减运算，很方便。

as.Date(x, format, ...)，适用于字符型

        as.Date('20160430','%Y%m%d') #注意format形式一定要和字符串一样
        [1] "2016-04-30"

as.Date(x, origin, ...)，适用于数值型

        as.Date(1, origin = "1904-01-01") # 1998-07-05
        [1] "1904-01-02"

as.Date(x, tz = "UTC", ...)，适用于POSIXlt类型

        as.Date(as.POSIXlt(Sys.time(), "GMT"))
        [1] "2016-04-30"

        as.Date('20160430','%Y%m%d')-10  
        [1] "2016-04-20"

* 时间变量转为字符型

时间变量转为字符串的函数有三个format,strftime,as.character

format(x,format)，x是时间变量，format是要转换的形式

        format(Sys.time(),'%Y-%m-%d')
        [1] "2016-04-30"
        format(Sys.time(),'%Y')
        [1] "2016"

as.character(x，format)，将时间变量转为字符串

       as.character(Sys.time(),'%X')
       [1] "08:51:27"

strftime(x,format,tz='')，x是待转换的对象，format是转换的形式

      strftime(Sys.time())
      [1] "2016-04-30 08:48:30"
      strftime(Sys.time(),'%x')
      [1] "30/04/2016"

* 提取月份，星期，季度

  strptime提供了从字符型变量中提取月份，星期的方法，R中提供了从Date,POSIXt类型中提取的函数，注意必须是Date,POSIXt类型

     weekdays(x, abbreviate = FALSE)
     months(x, abbreviate = FALSE) 
     quarters(x, ...)

* 创造时间序列对象ts()
 
ts(data = NA, start = 1, end = numeric(), frequency = 1)

data 是待转换的变量，可以是向量或矩阵，strat是起始时间，可以是单个数值或者包含两个整数的向量，end是结束时间，frequency指采集频率，1代表年度间隔，4代表季度间隔，7代表星期间隔，12代表月度间隔。

     ts(1:10, frequency = 1, start = c(1959, 1)) # 2nd Quarter of 1959
     Time Series:
     Start = 1959 
     End = 1968 
     Frequency = 1 
    [1]  1  2  3  4  5  6  7  8  9 10
    ts(1:10, frequency = 4, start = c(1959, 2)) # 2nd Quarter of 1959
            Qtr1 Qtr2 Qtr3 Qtr4
     1959         1    2    3
     1960    4    5    6    7
     1961    8    9   10    
    ts(1:10, frequency = 12, start = c(1959, 1)) # 2nd Quarter of 1959
          Jan Feb Mar Apr May Jun Jul Aug Sep Oct
    1959   1   2   3   4   5   6   7   8   9  10

* 时序数据运算
  
   我们可以用ts.intersect()，ts.union提取两组或多组时间序列数据的时间交集或并集数据。

     ts.intersect(..., dframe = FALSE)
     ts.union(..., dframe = FALSE)#缺少的时间序列部分用NA代替

dframe设定是否返回数据框形式

    a <- ts(1:5, frequency = 1, start = 1) 
    b <- ts(1:5, frequency = 1, start = 3)

    ts.intersect(a,b)
    Time Series:
    Start = 3 
    End = 5 
    Frequency = 1 
      a b
    3 3 1
    4 4 2
    5 5 3
    ts.union(a,b)
    Time Series:
    Start = 1 
    End = 7 
    Frequency = 1 
       a  b
    1  1 NA
    2  2 NA
    3  3  1
    4  4  2
    5  5  3
    6 NA  4
    7 NA  5 

我们可以用window()提取ts对象的某段时间的数值

window(x, start, end, frequency, deltat, ...)

    window(a,2,4)
    Time Series:
    Start = 2 
    End = 4 
    Frequency = 1 

使用以上这些函数就可以实现对时间序列数据的基础处理了，当然，R中也有开发的专门对时间序列处理的包`zoo`，有人已经总结的很详细了，可以查看这篇博文[R语言时间序列基础库zoo](http://blog.fens.me/r-zoo/)。

## Python

Python中也提供了完整的时间序列处理方式，详见《利用Python 做数据处理》中时间序列一章。

鉴于有人已经对Python中的时间序列总结的很详细了，详见博文[pandas 时间序列操作](http://my.oschina.net/lionets/blog/280855#OSC_h3_12)，在此不再赘述。

## 后记

后续用R和Python做时间序列的其他方法会陆陆续续补上。

## 参考资料

【1】 [时间格式对照](http://127.0.0.1:14653/library/base/html/strptime.html)

【2】[可扩展的时间序列xts](http://blog.fens.me/r-xts/)

【3】 [CRAN Task View: Time Series Analysis](https://cran.r-project.org/web/views/TimeSeries.html)

【4】R语言核心技术手册

【5】[R语言时间序列基础库zoo](http://blog.fens.me/r-zoo/)