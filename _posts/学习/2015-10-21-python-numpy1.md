---
layout: post
title: Python numpy随机数生成和数学统计运算	
category: 学习
tags: 数学统计运算
keywords: numpy 
description: 
---

## 随机数生成

如果要做模拟，经常需要生成大量的随机数，虽然Python中有内置的随机数生成函数，但效率远逊于Nympy。Numpy中的随机数函数在random模块中，以下就是一些常用的随机数生成方法。

1.seed：设定随机数生成器种子
    
    示例：np.random.seed(123)

2.rand：生成（0,1）区间上的均匀分布随机数
  
语法：rand(d0, d1, ..., dn)

参数：d0,d1,…,dn指生成数据的形状

    示例：np.random.rand(2,3)
    
3.randn：生成标准正态分布的随机数
       
语法：randn(d0, d1, ..., dn)
       
参数：d0,d1,…dn指生成数据的形状
    
      示例：np.random.randn(2,2)
    
4.  normal: 生成正态分布
       
语法：normal(loc=0.0, scale=1.0, size=None)
       
参数：loc设定均值，scale设定标准差，size设定生成数据的形状，可以是元组或单值。默认标准正态分布。
     
    示例：np.random.normal(1,2,(2,3))
    
5.  multivariate_normal: 生成多元正态分布
       
语法：multivariate_normal(mean, cov[, size])
       
参数：mean设定均值向量，cov设定协方差矩阵，size设定生成数据的形状
       
    示例：mean = (1,2)
         cov = [[1,0],[1,0]]
         np.random.multivariate_normal(mean,cov,(3,3))
    
6.uniform：生成均匀分布
       
语法：uniform(low=0.0, high=1.0, size=None)
       
参数：low设定均匀分布的区间下界，high设定均匀分布的区间上界，size设定生成数据的形状，可以是元组或单值。默认生成0-1区间的均匀分布
       
    示例：np.random.uniform(1,10,10)
     
7.chisquare: 生成卡方分布的随机数
       
语法：chisquare(df, size=None)
       
参数：df设定自由度，size设定生成数据的形状
    
    示例：np.random.chisquare(7,(2,3))
     
8.gamma： 生成Gamma分布的随机数
       
语法：gamma(shape, scale=1.0, size=None)
       
参数：shape设定分布的形状参数，scale设定分布的尺度参数，size设定生成数据的形状。
       
    示例：np.random.gamma(1,1,5)
    
9.binomial：生成二项分布的随机数
       
语法：binomial(n, p, size=None)
       
参数：n设定试验次数，p设定成功概率，size设定生成数据的形状
       
    示例：np.random.binomial(10,0.2,5)
  
10.randint： 返回给定上下界范围内的随机整数
       
语法: randint(low, high=None, size=None)
       
参数：low设定下界，high设定上界，size设定生成数据的形状。返回的区间是半闭区间 [low , high )
       
    示例：np.random.randint(1,10,5)
    
11.shuffle: 对一个序列进行随机排列
       
语法：shuffle(x)
       
参数：x指列表或数组
       
    示例：x = np.arange(10)
         np.random.shuffle(x)
    
12.permutation：返回一个序列的随机排列或返回一个随机排列的范围
      
语法：permutation(x)
      
参数：如果x是一个标量数值，则返回对np.arange(x)的随机排列结果；如果x是一个序列，则复制序列并对其进行随机排序，原序列不变
      
    示例：np.random.permutation(x)
         np.random.permutation(10)
    
numpy.random还可以生成很多其他分布的随机数，详细请查阅参考资料2。

## 统计方法
    
Numpy可以利用统计函数或数学函数对整个数组或某个轴向上的数据进行运算，类似于R中的apply家族，只是apply中是对行或列运算，而numpy中是对轴运算。
    
基本的统计方法：
    
1.sum：对数组或某个轴向上的数据进行求和
        
语法：np.sum(a, axis=None, dtype=None, out=None, keepdims=False)
        
参数：a指数组，axis设定轴方向，如果不设定则对全部数据求和，可以是单值，也可以是元组
        
    示例：np.sum([[0, 1], [0, 5]], axis=0)
         np.sum([[0, 1], [0, 5]], axis=1)
         np.sum([[0, 1], [0, 5]])
    
2.mean:算术平均数
       
语法，参数设定同上
       
    示例：np.mean([[0, 1], [0, 5]], axis=0)
         np.mean([[0, 1], [0, 5]], axis=1)
         np.mean([[0, 1], [0, 5]])
    
3.std. var 计算标准差，方差
       
语法，参数设定同上
       
    示例：np.std([[0, 1], [0, 5]], axis=0)
         np.std([[0, 1], [0, 5]], axis=1)
         np.std([[0, 1], [0, 5]])
    
4.min,max 求最小值，最大值
       
语法，参数设定同上
      
    示例：np.min([[0, 1], [0, 5]], axis=0)
         np.min([[0, 1], [0, 5]], axis=1)
         np.min([[0, 1], [0, 5]])
    
5.argmin,argmax 求最大元素和最小元素的索引
       
语法：np.argmin(a, axis=None)
       
参数：a指数组，axis设定轴
      
    示例：np.argmin([[0, 1], [2, 5]])
         np.argmin([[0, 1], [2, 5]],axis = 0)
         np.argmin([[0, 1], [2, 5]],axis = 1)
   
6.cumsum. Cumprod 计算所有元素的累积和和累计积
       
语法：np.cumsum(a, axis=None, dtype=None, out=None)
     
np.cumprod(a, axis=None, dtype=None, out=None)
     
参数： a指数组，axis设定轴方向
      
    示例：np.cumsum([[1,2,3],[4,5,6]])
         np.cumsum([[1,2,3],[4,5,6]],axis = 0)

##数组的集合运算

Numpy提供了一些用于一维数组的基本集合运算，涉及集合的交并差，这部分函数与R的dplyr包中相同功能的函数类似，比如set系列函数。

<img src="http://7xo51k.com1.z0.glb.clouddn.com/numpy集合运算.png-wx" align=center />

到此为止，numpy库中的基本函数及用法都介绍完了。

##参考资料

【1】利用python做数据分析
    
【2】[numpy的random模块](http://www.mamicode.com/info-detail-507676.html)