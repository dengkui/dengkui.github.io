---
layout: post
title: Python 数据合并
category: 学习
tags: 数据处理
keywords: python，数据合并
description: python数据处理优于R的部分
---

其实，R在数据处理上做的已经很出色了，尤其是reshape2包和dplyr包，已经可以解决绝大多数问题了。这几天翻看python的数据处理部分，发现大部分的功能和R是一样的，甚至处理数据的哲学思想都是一致的，即拆--计算--合并。但是作为工程上经常用到的软件，某些部分同R还是不一样的，甚至比R更为强大。

###**数据合并**
一提到数据合并，我们就想起了merge函数，没错，就是它，这个函数在python中的语法同R种基本一致，我就不写了。来看看python在数据合并上更灵活的地方吧。对于两个数据框，通用的做法是根据某个列变量进行合并，但是如果两个数据框共用的那个变量处于索引位置怎么办？如果在R中，我们可以把行名称赋给一个新列名，然后按照传统merge就可以了。在Python中，针对这一问题，提供了按索引合并功能，只需在merge函数中指定left_ index = True 或right_ index = True ，就可以实现根据索引进行合并。

示例：

    from pandas import Series,DataFrame
    import pandas as pd
    import numpy as np
    left = DataFrame({'key':['a','b','c'],'value':range(3)})
    right = DataFrame({'group_val':[3.5,7]},index = ['a','b'])
    pd.merge(left , right , left_on = "key" , right_index = True) #默认交集

对于层次化索引的数据（也就是有多个索引的数据），python也可以做到合并，不过是指定用于合并的多个列。

示例：
 
    lefth = DataFrame({'key1':['ohio','ohio','ohio','nervada','nervda'],
                  'key2':[2000,2001,2002,2003,2001],
                  'data':np.arange(5)})
    righth = DataFrame(np.arange(12).reshape((6,2)),
                  index = [['nevada','nevada','ohio','ohio','ohio','ohio'],
                          [2001,2000,2000,2000,2001,2002]],
                  columns = ['event1','event2'])
    pd.merge(lefth , righth , left_on = ['key1' , 'key2'] , right_index = True)#列表指定合并的列及对应的顺序
    
对于这种索引合并，有一种更为简便的join方法，它可以对拥有相同和相似索引的多个数据框进行合并，对于上面的索引合并我们就可以采用join方法，这种方法默认的是**外连接**。

    left.join(right , on = "key" , how = 'inner')
    lefth.join(righth , on = ['key1' , 'key2'] , how = "inner")



##参考资料
【1】利用python进行数据分析