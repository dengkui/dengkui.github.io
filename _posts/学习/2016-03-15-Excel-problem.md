---
layout: post
title:  Excel处理数据格式
category: 学习
tags:  数据处理        
keywords: Excel 
description: Excel处理数据的一些常见问题
---

在用Excel处理借贷人信息数据时，发现一些变量的数据既包含中文又包含数字，一些数据采用千分位表示，这样的数据都是不规范的数据，在进行进一步的数据分析前，必须对这些数据格式进行处理，使其成为规整的数据，便于处理。

以下是解决方案：

1. 提取包含中文的单元格数据中的数字
  
对于这种情况，我们可以结合LEFT/RIGHT,LEN,LENB函数进行处理。LEFT函数的语法结构是`LEFT(text,num\_chars)`,text是截取对象，num\_chars是截取多少内容。RIGHT函数的语法类似。具体使用可以参照下面例子：


<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/ExcelExcel-1.jpg-wx"  ></div>

2. 将文本型数据转为数值型数据

采用上述方法，我们就提取出了数字。然而，对于公式提取的数字，我们一般都采用复制-选择性粘贴-粘贴数字来获取纯数字，但是这样的数字格式是文本格式的，Excel在单元格的左上方会有黄色标示，这时我们选择第一个单元格，然后ctrl+shift+下箭头,就可以选取整列数据，之后点击黄色标示，选择转为数字格式，我们就得到了数字格式。这时，我们发现有些数据是千分位格式的，我们选择单元格格式，点击自定义，选择0或0.00，0代表只选取整数，0.00代表选取为两位小数的数字。

具体例子如下：

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/ExcelExcel-3.jpg-wx"  ></div>

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/ExcelExcel-2.jpg-wx"  ></div>

虽然Excel处理这样的混合型数据也比较方便，但在运行时，发现运行速度实在太慢了，还是想想用R/Python实现吧……


## 参考资料

【1】[怎样提取Excel单元格中的数字](http://jingyan.baidu.com/article/ff411625ad0f6612e48237be.html)

【2】[文本数字转为数字格式](http://www.pep.com.cn/xxjs/jszj/jsjl/201011/t20101112_969571.htm)