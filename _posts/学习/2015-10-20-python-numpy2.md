---
layout: post
title: Python numpy数组元素属性及访问	
category: 学习
tags: 数据处理
keywords: numpy 
description: 
---

数组对象建立后，我们经常需要查看对象的一些属性特征以及访问或更改元素赋值。

首先我们建立一个数组：

    import numpy as np
    data1 = np.array([[1,2,3],[4,5,6],[7,8,9]])
    data1
    Out[4]: 
    array([[1, 2, 3],
       [4, 5, 6],
       [7, 8, 9]])
  
##查看属性：数组的属性主要有5种：
    
1.查看轴数： ndarray.ndim
     
在NumPy中维度(dimensions)叫做轴(axes)，轴的个数叫做秩(rank)。

 一个3×3的数组，从左到右的数字对应表示由表及里的维度，也就是轴，按照索引给轴编号依次为“轴0“，“轴1”。它的秩为2。

<img src="http://7xo51k.com1.z0.glb.clouddn.com/numpy轴1.png-wx" align=center />

类似的，一个3×3×2的数组，按照索引给轴编号依次为“轴0”，“轴1”，“轴2“。它的秩为3。
    
    data1.ndim
    Out[7]: 2
    data1是一个二维数组，秩为2

2.查看数组大小：ndarray.shape

    data1.shape
    Out[6]: (3, 3)
    data1是一个3行3列的数组
    
3.查看数组元素个数：ndarray.size

    data1.size
    Out[8]: 9
    
4.查看数组中元素类型：ndarray.dtype
    
    data1.dtype
    Out[9]: dtype('int32')
    
5.查看数组中元素的字节大小：ndarray.itemsize
    
    data1.itemsize
    Out[11]: 4
    
一个元素类型为int32的数组itemsiz属性值为8(=32/8)
    
##访问并更改数组元素：索引和切片
    
基本索引和切片：根据轴的编号确定索引位置。注意Python从0开始。

<img src="http://7xo51k.com1.z0.glb.clouddn.com/numpy轴2.png-wx" align=center />

（1）访问元素

    data1[2] #查看第三行
    Out[12]: array([7, 8, 9])
    data1[:,2] #查看第三列，冒号表示选取整个轴，请data1[:]
    Out[14]: array([3, 6, 9])
    data1[2,:2] #查看第三行一二列
    Out[26]: array([7, 8])
    data1[2,2] #查看第三行第三列
    Out[16]: 9

（2）更改元素值：可以将标量和数组赋给数组

    arr1 = np.array((11,12,13)) #建立一维数组
    arr1
    Out[19]: array([11, 12, 13])
    data1[2]=1 #标量赋值，将第3行元素都变为1
    data1
    Out[21]: 
    array([[1, 2, 3],
           [4, 5, 6],
           [1, 1, 1]])
    data1[2]=arr1#数组赋值
    data1
    Out[23]: 
    array([[ 1,  2,  3],
           [ 4,  5,  6],
           [11, 12, 13]])
    
2.布尔型索引：利用布尔数组访问并赋值，不能使用布尔列表。

    data2 = np.arange(1,6,1)
    data2
    Out[30]: array([1, 2, 3, 4, 5])
    boo = np.array((True,False,True,False,False))
    data2[boo]
    Out[34]: array([1, 3])
    data2[np.array([True,False])]#布尔数组不够时，不够部分都为Fasle
    Out[36]: array([1])
    data2[[True,False,True]]#如果采用布尔列表，则True为1,False为0,采用整数序列方式获取元素
    Out[37]: array([2, 1, 2])

3.花式索引：利用整数数组或列表进行索引。

    data3 = np.array((0,1,2,1))#整数数组
    data1[data3]#整数数组索引
    Out[46]: 
    array([[1, 2, 3],
           [4, 5, 6],
           [7, 8, 9],
           [4, 5, 6]])
    data1[[1,2,0]] #整数列表索引
    Out[48]: 
    array([[4, 5, 6],
           [7, 8, 9],
           [1, 2, 3]])
    data1[[1,2,0],[0,1,2]]#查看（1,0），（2,1），（0,2）位置上的数。
    Out[49]: array([4, 8, 3])
  
以上就是基本的数组属性及访问赋值方法了。

##参考文献
 
【1】[Python 数据分析基础包：Numpy ](http://my.oschina.net/lionets/blog/276574)

【2】[NumPy-快速处理数据](http://old.sebug.net/paper/books/scipydoc/numpy_intro.html)
    
【3】Python for Data Analysis（利用Python进行数据分析 ）

【4】[ndarray对象](http://hyry.dip.jp/tech/book/page/scipy/numpy_ndarray.html)

