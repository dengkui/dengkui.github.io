---
layout: post
title:  转换Flash阅读文件为PDF文件
category: 学习
tags:  软件学习
keywords: 
description: 
---

工作学习中，有时我们会遇到想要下载的文件是flash格式，只能阅读，却无法下载。针对这种情况，本篇文档记录如何下载网页内嵌PDF并转换为可用版PDF格式。

总体的解决思路是将Flash文件通过虚拟打印的方式转换为PDF文件。
具体解决步骤分三步：

1. 找到flash文件的网页地址。如果打开的flash文件就是独立网页，则跳过此步。通过查看源代码，在源代码中搜寻".swf"，找到对应的flash文件地址。<img src="http://7xo51k.com1.z0.glb.clouddn.com/1%E6%9F%A5%E6%89%BEswf%E6%96%87%E4%BB%B6%E5%9C%B0%E5%9D%80.jpg"  >
1. 使用浏览器打开flash文件网页地址，加载单独的flash阅读文件。<img src="http://7xo51k.com1.z0.glb.clouddn.com/2%E5%8D%95%E7%8B%AC%E7%9A%84PDF%E6%B5%8F%E8%A7%88%E9%A1%B5%E9%9D%A2.jpg"  >
1. 点击鼠标右键，选择”打印”，在打印选项中，选择一个虚拟打印机，页面范围选择全部，即可转换SWF文件为PDF文件。<img src="http://7xo51k.com1.z0.glb.clouddn.com/3%E8%99%9A%E6%8B%9F%E6%89%93%E5%8D%B0%E6%9C%BA.jpg"  >

如果不想通过查找源代码的方式打开flash文件，我们还可以在缓存文件夹中搜寻swf文件的方式来查找浏览过的swf文件，具体方式是浏览器-工具-Internet选项中-浏览历史记录-设置-查看文件来找到缓存文件夹。

通过以上做法，我们可以将Flash文件转换为可阅读的PDF文件，并可以进行后续的一系列转换操作。

虽然这种做法可以达到目的，但也存在以下问题：

1.转换后PDF清晰度不是很高。

2.大批量的PDF转换操作不容易

3.仅适用于可找到flash文件或网址的情况，部分网站的PDF阅读采用FileOpen，无法使用该方法。

对应的解决办法，对于问题1，如果文件全是文字，可以对转换后的PDF转换为Word。对于问题2，应该可以通过批处理的方式进行，虽然我没试过。问题三暂时无解。

