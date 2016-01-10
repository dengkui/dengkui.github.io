---
layout: post
title: Anaconda简介及安装教程
category: 学习
tags:  软件
keywords: Anaconda Python
description: 
---
Python虽然是一门优秀的程序语言，但其拥有出色的数据处理能力，尤其是在数据量巨大的时候，因而也吸引了不少数据分析人员的关注和使用。

Python的数据处理能力主要依赖于NumPy,SciPy,Matplotlib,Pandas这4个库，其中NumPy提供了矩阵运算的功能，SciPy则在NumPy的基础上添加了许多科学计算的函数库，而这两个库就使Python具有和Matlab一样的数据处理能力了。Matplotlib库提供了绘图，可以实现数据的可视化，pandas是基于NumPy的一种工具，该库提供了高效地操作大型数据集所需的工具。而这四个库都需要我们进行单独安装，Python自身并不具备这些库。

一般的Python数据分析教程并不直接在Python shell中运行代码，而是选择了IPython，IPython 是一个 python 的交互式 shell，比传统的Python shell 好用得多，支持变量自动补全，自动缩进，支持 bash shell 命令，内置了许多很有用的功能和函数。总而言之，IPython就是各种方便，各种好用！让你自从用了IPython就会嫌弃用Python，就像用了RStudio就不再想用R GUI。

而IPyhon的安装较为麻烦和复杂，一般人很难安装成功，幸好有大神将科学计算所需要的模块以及IPython打包供用户使用，Anaconda就是其中较好的一个。简言之，安装了Anaconda，你就安装了Python+NumPy+SciPy+Matplotlib+IPython+IPython Notebook。所以，我们仅仅安装Anaconda就可以了！[Anaconda下载地址](http://continuum.io/downloads)。页面如下：

<img src="http://7xo51k.com1.z0.glb.clouddn.com/anacondaAnaconda%E4%B8%8B%E8%BD%BD%E7%95%8C%E9%9D%A2.jpg-wx" align=center />

根据自己的系统选择相应版本进行下载（下载速度可能会有点慢），下载之后点击运行就可以安装了，和一般软件安装毫无二致，无需编译。安装成功后的效果如下：

<img src="http://7xo51k.com1.z0.glb.clouddn.com/anaconda%E5%AE%89%E8%A3%85%E5%90%8E%E7%9A%84%E7%95%8C%E9%9D%A2.png-wx" align=center />

在这里有Anaconda管理器（Anaconda Command Prompt），IPython Notebook,IPython QT,IPython,Spyder。点击IPython,就进入了IPython的界面。

<img src="http://7xo51k.com1.z0.glb.clouddn.com/anacondaIPython%E7%95%8C%E9%9D%A2.png-wx" align=center />

到此为止，IPython就算安装成功了，是不是很方便，很简单！之后，我们可以在命令行（也就是cmd）中输入pip list 或conda list或者在Anaconda管理器中输入conda list来查看已经安装的库，效果如下：

<img src="http://7xo51k.com1.z0.glb.clouddn.com/anacondacmd%20Anaconda.png-wx" align=center />

从这些库中我们可以发现文章开始的4个库，说明已经安装成功了!

虽然IPython是极好的，但是，IPython Notebook则更进了一步，允许我们在浏览器上进行编程并进行演示，效果非常好！我们已经安装了IPython Notebook了，直接点击进入，这时浏览器会自动打开网页，这是home主页，如下图，然后点击右上角的new，创建新的python文本就可以了（可能遇到小故障，请按其提示操作）！

<img src="http://7xo51k.com1.z0.glb.clouddn.com/anacondaIPNBHome.jpg-wx" align=center />

<img src="http://7xo51k.com1.z0.glb.clouddn.com/anacondaIPNB.jpg-wx" align=center />


现在，小伙伴们就可以愉快地进行学习了，而不必为了安装而心烦意乱！

##**参考资料**

[1][Python科学计算发行版—Anaconda ](http://seisman.info/anaconda-scientific-python-distribution.html)

[2][windows下安装python和依赖包的利器——Anaconda](http://www.th7.cn/system/win/201502/93336.shtml)

[3][IPython Notebook 简介](http://my.oschina.net/lionets/blog/274760)

[4][IPython Notebook简介1](http://hyry.dip.jp/tech/slice/slice.html/35)





