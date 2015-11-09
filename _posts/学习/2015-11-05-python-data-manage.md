---
layout: post
title: Python 数据处理
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

###**移除重复数据**
在数据清理阶段，经常需要移除重复数据，在R中并没有直接的移除函数，不过我们可以沿着这么一个思路来实现目的，即首先判断是否重复（返回布尔值），接着利用布尔值取不重复部分。

在R中判断是否重复的函数是duplicated,即重复的意思，它可以对向量和数据框做运算，返回一个布尔值向量。在Python中也有类似的函数，不过更为强大，它直接提供了移除重复值的功能。

python中duplicated同R中一致，返回一个布尔类型series，而drop_duplicates方法直接返回一个移除了重复值的数据框，同时，你还可以指定部分列进行重复值判断，是不是很灵活呢？更多内容请data.drop_duplicates?
示例：

    data = DataFrame({'k1':['one']*3+['two']*4 , 'k2':[1,1,2,3,3,4,4]})
    data
    Out[22]
        k1  k2
    0  one   1
    1  one   1
    2  one   2
    3  two   3
    4  two   3
    5  two   4
    6  two   4
    data.drop_duplicates()  #删除重复行
    Out[23]: 
        k1  k2
    0  one   1
    2  one   2
    3  two   3
    5  two   4
    data['v1'] = range(7)
    data.drop_duplicates(['k1','k2'] , take_last = True) #指定k1,k2列判断，并保留最后一个组合
    Out[30]: 
        k1  k2  v1
    1  one   1   1
    2  one   2   2
    4  two   3   4
    6  two   4   6

###利用函数或映射进行函数判断
有时我们需要对数据进行映射，就是配对，不同的x对应不同的y，在R中可以利用merge函数实现，虽然merge函数并不是主要做这个的，python中就更简单了，直接使用map函数，它可以接受一个含有映射关系的字典，字典的对应不就是映射关系吗？map的一般用法是 map ( function ,sequence ),更多关于map的使用方法请map?

示例：

    data = DataFrame({ 'food' :['bacon' , 'pulled pork' ,'bacon' ,'pastrami' ,
    'corned beef' , 'bacon' ,'pastrami','honey ham','nova lox' ],
    'ounces' : [4,3,12,6,7.5,8,3,5,6]})
    meat_to_animal = {'bacon' : 'pig','pulled pork' : 'pig','pastrami' : 'cow',
    'honey ham' : 'pig','corned beef' : 'cow','nova lox' : 'salmon'}
    data['animal'] = map(meat_to_animal,data['food'])
    data['animal'] = data['food'].map(meat_to_animal)
    data
    Out[55]: 
              food  ounces  animal
    0        bacon     4.0     pig
    1  pulled pork     3.0     pig
    2        bacon    12.0     pig
    3     pastrami     6.0     cow
    4  corned beef     7.5     cow
    5        bacon     8.0     pig
    6     pastrami     3.0     cow
    7    honey ham     5.0     pig
    8     nova lox     6.0  salmon



##参考资料
【1】利用python进行数据分析

【2】[python 中的map,filter,reduce 函数](http://blog.sina.com.cn/s/blog_45ac0d0a010191rb.html)