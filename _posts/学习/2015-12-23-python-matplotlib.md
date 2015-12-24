---
layout: post
title:  Python 显示绘制的图形
category: 学习
tags:  可视化        
keywords: IPython Spyder IPython NoteBook
description: 利用各种Python环境进行统计图形绘制
---

利用Python进行绘图时，经常使用matplotlib包，此包经常在Pylab模式下使用，我们可以通过在命令行中输入ipython --pylab 打开Pylab模式，命令行可以是系统自带的命令行也可以是Anaconda的命令行。通过输入plot(np.arange(10))来验证是否是Pylab模式，如果出现一个图形界面，则表明模式正确，否则，不是Pylab模式。Pylab模式会自动引入一堆模块和函数，大多数的绘图需求已经可以满足。

在Pylab模式中，输入绘图代码，就会出现一个图形窗口，可以对图形进行调整，并进行保存。

如果不是在Pylab模式，绘图结果不直接显示，仅仅提示有图形生成。如果要显示绘图结果，我们可以使用魔法命令：

`%matplotlib qt`

或

`%matplotlib inline` 

如果使用qt，绘图结果将会显示在一个新的窗口，同Pylab模式一样，如果使用inline，绘图结果会直接显示在单元格下方。在Anaconda中，有Ipython，IPython NoteBook , Spyder三种环境可以使用。inline方式适用于IPython Notebook。`%matplotlib inline`在IPython中不能使用。在Spyder中，默认使用inline，可以通过上述命令修改，也可以通过设置Tools-Perferences-Ipython console-Graphics-backend来实现。

示例：

    import matplotlib.pyplot as plt
    from numpy.random import randn
    %matplotlib qt
    plt.plot(randn(50).cumsum(),'k--')

输出结果是一个独立的图形窗口，可以对图形进行调整，并进行保存。

示例：
    %matplotlib inline
    import matplotlib.pyplot as plt
    from numpy.random import randn
    plt.plot(randn(50).cumsum(),'k--')

输出结果在程序下方。

##参考资料

【1】[魔法（Magic）命令](http://hyry.dip.jp/tech/book/page/scipynew/ipython-200-notebook-magic.html)

【2】利用Python进行数据分析