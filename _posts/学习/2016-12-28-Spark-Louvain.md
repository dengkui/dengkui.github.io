---
layout: post
title:  在Spark上运行社区发现Louvain算法
category: 学习
tags:  模型安装	        
keywords: Spark  Louvain
description: 在Spark上运行Scala语言编写的Louvain算法
---

Louvain是一种社区发现算法，本篇文章主要讲述如何对算法代码进行打包并放置到服务器上通过命令行方式传入参数运行并输出计算结果。如果想了解Louvain算法的细节，请移步[Louvain社区发现算法](http://www.ithao123.cn/content-6027541.html)，并查看相应的论文。

## 软件环境

本篇文章利用Spark实现Louvain算法,通过本地访问服务器的方式在服务器上运行算法。

本地需要安装：

1. [Scala](http://www.scala-lang.org/)：我安装的是2.10.4版本，安装成功后，在CMD中运行`scala`会有版本信息提示
2. Scala IDE：我使用的是[Scala IDE for Eclipse](http://scala-ide.org/)
3. Spark 包 [Download Apache Spark™](http://spark.apache.org/downloads.html)：根据scala版本选择相应版本，我安装的是spark-1.6.0-bin-hadoop2.6.tgz
4. SSH远程登录软件：我安装的是[Mobaxterm](http://mobaxterm.mobatek.net/download-home-edition.html)，有便携版和安装版。如果是本地运行Spark jar包可以不用安装此软件。

服务器上需要安装Spark
 
## 算法打包

Louvain算法在github上有许多实现，我采用的是[这篇文章的版本](http://www.jianshu.com/p/4ebe42dfa8ec)（感谢作者的无私分享），并对其代码做了一些修改，使打包后的代码可以实现输入三个参数：输入文件地址，输出文件地址，Master运行方式。

打开**Eclipse**，点击左上角的File-New-Scala Project,输入项目名称，选择项目地址，点击下方Next，选择Libraries,点击Add External JARS，选择本地安装3中压缩包下的**spark-assembly-1.6.2-hadoop2.6.0.jar**，点击Finish完成项目建立，这时如果项目名称下有一个红叉，并提示error为More than one scala library found in the build path ……，右击项目名称，选择Scala-Scala Installation Choice，选择Fixed Scala Installation:2.10.6(bundled)，这时error就应该解决了，到此，我们建立了一个项目。


<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/louvain-Spark-jar%E5%8C%85%E9%80%89%E6%8B%A9jpg.jpg"  ></div>

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/louvain-sparkscala-vision.jpg"  ></div>

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/louvain-sparkscala-vision2.jpg"  ></div>

接下来右击src-new-packages，创建一个包，之后右击创建的包，然后点击new-Scala Object，创建一个scala文件，这就是我们编写scala代码的地方，我们创建了三个文件，名字分别为**CommUtil.scala,GraphUtil.scala,Louvain.scala**，对应着原作者的三篇代码，注意文件名与代码中的对象名一致。

代码有两处进行了修改：

1. CommUtil.scala中添加了文件输出路径，并去掉了打印迭代运行结果。

2. Louvain.scala中添加了命令参数方式，使jar包可以接受指定参数运行。

代码文件过长不在此展示，有兴趣的可以下载查看：[src.zip]({{site.url}}/assets/src.zip)

scala代码文件写好后，如果没有提示错误，我们就可以打包成jar包运行，打包过程参见参考资料6，值得注意的是，scala文件打包并不选择main函数入口。

## 上传运行

将打包好的jar包和其依赖的jar包一并上传到服务器，通过spark-submit命令运行打包，运行程序如下：

    spark-submit --class "Louvain" --driver-class-path spark-assembly-1.6.2-hadoop2.6.0.jar Louvain.jar "/tmp/club.txt" "/tmp/test1.txt" local[2]

其中，`dirver-class-path`是依赖jar包的地址，`Louvain.jar`是程序包，`"/tmp/club.txt"`是输入文件地址，`"/tmp/test1.txt"`是输出文件地址，`local[2]`是Master设定方式。Spark-Submit命令的详细资料查阅参考资料2。

我们通过hdfs命令查看运行结果，spark的运行结果的文件数与设置的Master有关，如果设置为2，则输出结果保存在两个文件中。

        hadoop fs -cat /tmp/test1.txt/part-00000
        hadoop fs -cat /tmp/test1.txt/part-00001

## 参考资料

【1】[社区发现算法-Louvain](http://www.jianshu.com/p/4ebe42dfa8ec)

【2】[spark-submit提交参数设置](http://www.cnblogs.com/gnool/p/5643595.html)

【3】[hadoop HDFS常用文件操作命令](https://segmentfault.com/a/1190000002672666)

【4】[Louvain社区发现算法](http://www.mamicode.com/info-detail-400396.html)

【5】[Spark源码分析：多种部署方式之间的区别与联系](http://www.tuicool.com/articles/jAZZZf/)

【6】 [Eclipse jar打包详解](http://jingyan.baidu.com/article/219f4bf7d0ef87de442d3820.html)