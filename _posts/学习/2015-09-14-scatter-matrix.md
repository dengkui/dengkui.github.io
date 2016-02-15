---
layout: post
title: R语言 散点图矩阵的演变
category: 学习
tags: 可视化
keywords: R，散点图矩阵
description: 
---

在观测多组数值型变量之间的数量关系时，散点图矩阵很有用。

印象中的散点图矩阵应该是这样的（数据来自mtcars数据集）：

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/smSM.png-wx"  ></div>

    pairs(~mpg+disp+drat+wt,data=mtcars,main="BasicScatter Plot Matrix")

有人表示，这样的散点图矩阵太粗糙了，完全满足不了要求嘛，我要对角线展示变量的分布，对称的散点图太浪费了，我要上三角展示相关系数，最好各散点图还能展示拟合直线（曲线），譬如下图：

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/smMerge2%20-%20副本.png-wx"  ></div>

要实现这样的效果，我们可以通过设置pairs函数参数来实现。
设置对角线展示变量直方图，我们可以编一个直方图的函数，目的在于使对角线面板呈现直方图，代码如下

    panel.hist <- function(x, ...)
    {
      usr <- par("usr"); on.exit(par(usr))
      par(usr = c(usr[1:2], 0, 1.5) )
      h <- hist(x, plot = FALSE)
      breaks <- h$breaks; nB <- length(breaks)
      y <- h$counts; y <- y/max(y)
      rect(breaks[-nB], 0, breaks[-1], y, col = "darkred", ...)
    }

之后修改参数diag.panel默认设置就可以实现：

    pairs(~mpg+disp+drat+wt,data=mtcars,main="Basic Scatter Plot Matrix",diag.panel=panel.hist)#diag.panel参数控制对角线展示内容

接下来，编造显示线性相关系数的小函数，代码如下:

    panel.cor <-function(x,y,...)
    {
       par(usr=c(0,1,0,1))
       txt <- as.character(format(cor(x,y),digits=2))
       text(0.5,0.5,txt,cex=2,font=2)
     }

之后，修改参数upper.panel的设置即可

    pairs(~mpg+disp+drat+wt,data=mtcars,main="Basic Scatter Plot Matrix",diag.panel=panel.hist,upper.panel=panel.cor)

如果我们想通过字体大小来显示线性相关程度的强弱，可以通过修改panel.cor函数中的cex设置来实现，代码如下：

    panel.cor <- function(x, y, prefix = "", cex.cor, ...)
    {
     usr <- par("usr"); on.exit(par(usr))
     par(usr = c(0, 1, 0, 1))
     absr <- abs(cor(x, y))
     r <- cor(x,y)
     txt <- as.character(format(r,  digits = 2))
     atxt <- as.character(format(absr,digits=2))
     if(missing(cex.cor)) cex.cor <- 0.8/strwidth(atxt)
     text(0.5, 0.5, txt, cex = cex.cor * absr)
    }
    pairs(~mpg+disp+drat+wt,data=mtcars,main="Basic Scatter Plot Matrix",upper.panel=panel.cor,diag.panel=panel.hist)

散点图添加线性拟合直线的小函数如下：

    panel.lm <- function(x,y,...)
    {
     fm=lm(y~x)
     points(x,y)
     abline(fm,col="darkred")
    }

之后，设置下三角参数lower.panel即可实现散点图添加线性拟合直线。

    pairs(~mpg+disp+drat+wt,data=mtcars,main="Basic Scatter Plot Matrix",diag.panel=panel.hist,upper.panel=panel.cor,lower.panel=panel.lm)

同样的设置lower.panel=panel.smooth（panel.smooth是基础函数）即可实现散点图添加平滑拟合曲线。

如果要在一幅图中同时添加线性拟合直线和平滑拟合曲线，我们可以编一个小函数，代码如下：

    panel.ls <- function (x, y, col = par("col"), bg = NA, pch = par("pch"), cex = 1, col.smooth = "red", span = 2/ 3, iter = 3, ...) 
    {
    points(x, y, pch = pch, col = col, bg = bg, cex = cex)
    ok <- is.finite(x) & is.finite(y)
    if (any(ok)) 
        lines(stats::lowess(x[ok], y[ok], f = span, iter = iter), 
            col = col.smooth, ...)
    fm=lm(y~x)
    abline(fm,col="darkred")
    }

之后设置lower.panel=panel.ls就可以了！

有人表示上面的代码太麻烦了，完全看不下去，难道就没有简单的方法？有!car包的scatterplotMatrix（）函数就是为了解救芸芸众生的。这个函数可以绘制以某个条件的散点图矩阵，同时允许在主对角线上放置箱线图，密度图，直方图，同时散点图还可以拟合线性回归直线和平滑曲线，一个缺点就是不能绘制线性相关系数(有可能是我不知道)。
  
    比如：
    library(car)	
    scatterplotMatrix(~mpg+disp+drat+wt,data=mtcars,spread=F,main="Scatter Plot Matrix via car Package",smooth=F,reg.line=F,diagonal="histogram",col="darkred")

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/smHIS.png-wx"  ></div>

一个命令就在对角线添加了直方图，有木有很方便？

    添加线性回归线
    scatterplotMatrix(~mpg+disp+drat+wt,data=mtcars,spread=F,main="Scatter Plot Matrix via car Package",smooth=F,diagonal="histogram",col="darkred")

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/smLM.png-wx"  ></div>

    添加平滑拟合曲线
    scatterplotMatrix(~mpg+disp+drat+wt,data=mtcars,spread=F,main="Scatter Plot Matrix via car Package",reg.line=F,diagonal="histogram",col="darkred")

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/smSL.png-wx"  ></div>

    同时添加线性回归线和平滑拟合曲线，并采用不同的颜色表示
    scatterplotMatrix(~mpg+disp+drat+wt,data=mtcars,spread=F,main="Scatter Plot Matrix via car Package",diag="histogram",col=c("darkred","skyblue","orange"))

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/smSL.png-wx"  ></div>

函数中的参数reg.line控制是否绘制线性回归线，而smooth控制是否绘制平滑拟合曲线，对角线参数diagonal控制对角线绘制图形类型，c("density", "boxplot", "histogram", "oned", "qqplot", "none")六种选择。更多细节请`？scatterplotMatrix()`，请事先加载car包。

当然lattice包中的splom函数也能实现很多功能，大家有兴趣，可以自己摸索一下。

## 参考资料

【1】 R语言自带文档