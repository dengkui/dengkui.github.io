---
layout: post
title:  R/Python  答疑帖
category: 学习
tags:  软件学习        
keywords: R 
description: 常见问题答疑
---

这篇文章用以记录遇到的较为简单的问题，并做一答复记录。

## R

    1. 查询修改当前工作目录

　　getwd()来获取当前的工作目录，如果不加以指定，一般R文件的保存都在这个目录中。set()指定当前工作目录，注意路径使用正斜杠/

    2. 保存当前绘图结果
   
　　在RGui中，点击图片，之后点击菜单栏file中的save as选择合适的格式进行保存。如果在Rstudio中可以直接点击Plot中的Export中save as image，选择合适的格式进行保存。
    
    3. 读取当前目录下所有文件名称
   
　　采用list.files(files)获取files文件夹下所有文件名称，如`list.files('R')`

    4. 时间序列数据的滞后运算
   
　　lag(x,k)，x是滞后对象，k是滞后阶数。注意此函数仅仅是对时期做了改动，数据并没有变化。采用ts.intersect()函数可以获取到两组时间序列数据中交集部分。      

    5. 去除向量和数据框中的重复值
    
   duplicated(x)#获取向量和数据框中的每一行是否重复的逻辑向量
   data[!duplicated(data),] #获取非重复值

    6. 当前工作环境去除某个已经加载的包
   
   detach('package:dplyr',unload = T)

    7. 查看某个函数或方法

   trace('lm',edit=T)

    8. 添加Latex公式

   使用latex2exp包显示Latex公式，具体见https://github.com/stefano-meschiari/latex2exp，另外plotly中添加希腊字母之类的可以使用html格式，比如"&mu;"

## Python
    1.pip命令安装包出错，提示`error:Microsoft Visual C++ 9.0 is required` 

   安装一个[Micorsoft Visual C++ Compiler for Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266)的包。具体方法参考[Windows下pip安装包报错：Microsoft Visual C++ 9.0 is required Unable to find vcvarsall.bat](http://www.cnblogs.com/ldm1989/p/4210743.html)

----------
未完待续