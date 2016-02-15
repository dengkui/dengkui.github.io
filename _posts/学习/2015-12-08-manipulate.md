---
layout: post
title:  manipulate学习
category: 学习
tags:  可视化        
keywords: 交互式图形
description: 利用R绘制交互式图形
---

R是一款可视化的利器，利用R可以做出很优雅的可视化图形。不过，大多数可视化图形都是静态图形，如果做出的图形可以通过交互来展现，无疑会很吸引人，而且在某些方面，也会更清楚地说明问题。

利用R语言做出可交互图形，有很多包可以选择，比如**ggvis，manipulate,rCharts**。

对于manipulate包，它的函数简单，操作方便，便于上手，而且效果也不错。它对图形提供了4个控件，用来选择图形绘制中的参数的变化范围，这样我们就可以通过这些控件来对图形参数进行选择，从而使图形产生交互效果。

manipulate包必须依赖于RStudio，所以必须安装RStudio，之后再下载安装加载manipulate包即可，加载完成后，可以通过输入isAvailable()函数来判断当前环境是否可以运行manipulate函数，可以运行返回TRUE，否则为FALSE。

manipulate包的主要函数为manipulate函数,此函数的用法为：manipulate(\`_ expr`, ...),其中_ expr指绘图函数，...指具体的控件设置，其中控件有四个：slider(滑动条),picker(选择器），checkbox（选择框），button(按钮）。

对于绘图函数，我们既可以使用基础函数，比如plot,barplot之类，也可以使用ggplot2包中的绘图函数。

slider的语法为slider(min, max, initial = min,label = NULL, step = NULL, ticks = TRUE)，min,max分别设置最小最大值，initial设置初始值，默认为最小值，label为显示标签名，ticks为是否显示刻度，默认显示。

picker的语法为picker(..., initial = NULL, label = NULL)，...用来设置待选的参数，initial用来设置初始值，默认为第一个待选参数，label为显示的名称。

checkbox的语法为checkbox(initial = FALSE, label = NULL)，initial为选择框的初始值，默认为FALSE，这个参数是逻辑值，label设置checkbox的名称。

button的语法为button(label)，label为按钮的名称。button实际上是一个逻辑值，运行时点击按钮，则button的值为TRUE，执行相应的操作。详情请？button()


manipulate包给了几个基础绘图函数的示例，比如下面这个示例，

    manipulate(
    plot(cars, xlim = c(x.min, x.max), type = type,
       axes = axes, ann = label),
    x.min = slider(0,15),
    x.max = slider(15,30, initial = 25),
    type = picker("p", "l", "b", "c", "o", "h", "s", "S", "n"),
    axes = checkbox(TRUE, "Draw Axes"),
    label = checkbox(FALSE, "Draw Labels"))

这个示例是对cars数据集绘制散点图，通过slider来控制x轴的数据范围，通过picker控制散点图的类型，通过checkbox来控制是否显示坐标轴以及横纵轴标签。

当然我们也可以采用ggplot2中的高级函数，下面这个示例用来展示直方图的不同的位置参数效果:stack（堆叠），fill(填充），dodge（并列）。

    library(ggplot2)
    manipulate(
     ggplot(diamonds,aes(clarity))+geom_bar(aes(fill = cut),position = type),
     type = picker("stack" = "stack","fill" = "fill","dodge" = "dodge",initial = "dodge")
              )

通过这些示例，我们可以理解manipulate函数的运行方式和原理，进而制作出一些可交互的图形，不过,manipulate包只能运行在RStudio环境中，而且动态的图形不能保存。如果要保存并在其他环境中展示交互统计图，我们还需要学习其他的R包。

## 参考资料

【1】[manipulate 简介](http://mooc.guokr.com/note/12762/)

【2】manipulate包自带文档