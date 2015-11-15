---
layout: post
title: kronecker积运算	
category: 学习
tags: 数学统计运算
keywords: 叉积 kronecker
description: 
---
##kronecker积运算性质

- <div> <img src="http://latex.codecogs.com/gif.latex?\ 0\bigotimes A =A \bigotimes 0 = 0 " title="\0 \bigotimes A" /> </div>

- <div><img src="http://latex.codecogs.com/gif.latex?\ (A_1 +A_2) \bigotimes B = (A_1 \bigotimes B) + (A_2 \bigotimes B)" title = "(A_1 + A_2) \bigotimes B" /></div>

- <div><img src="http://latex.codecogs.com/gif.latex?\ (\alpha A) \bigotimes ( \beta B) = \alpha \beta(A \bigotimes B) " title = "(\alpha A) \bigotimes ( \beta B) = \alpha \beta(A \bigotimes B)" /></div>

- <div><img src="http://latex.codecogs.com/gif.latex?\ (A_1 \bigotimes B_1)(A_2 \bigotimes B_2) = (A_1 A_2)\bigotimes (B_1 B_2) " title = "(A_1 \bigotimes B_1)(A_2 \bigotimes B_2) = (A_1 A_2)\bigotimes (B_1 B_2)" /></div>

- <div><img src="http://latex.codecogs.com/gif.latex?\ (A \bigotimes B) ^ {'} =A^{'} \bigotimes B ^{'} " title = " (A \bigotimes B) ^ {'} =A^{'} \bigotimes B ^{'}" /></div> 
 
- <div>如果<img src="http://latex.codecogs.com/gif.latex?\ A^{-1} " title = "A^{-1}" />
,<img src="http://latex.codecogs.com/gif.latex?\ B^{-1} " title = "B^{-1}" />存在，则 <img src="http://latex.codecogs.com/gif.latex?\(A \bigotimes B) ^ {-1} = A  ^{-1} \bigotimes B^{-1}" title = "A \bigotimes B) ^ {-1} = A  ^{-1} \bigotimes B^{-1}" /></div>

- <div><img src="http://latex.codecogs.com/gif.latex?\tr(A \bigotimes B) = tr(A) tr(B) " title = "tr(A \bigotimes B) = tr(A)tr(B)" /></div>

##软件运算方法
- R

    kronecker(A,B)

- Python

    import numpy as np
    np.kron(x,y)


##参考资料
【1】[博客园中应用LaTex进行公式编写](http://www.xuebuyuan.com/393838.html)

【2】[在线latex公式编辑器](http://www.codecogs.com/latex/eqneditor.php)

【3】多元统计分析引论



