---
layout: post
title:  逻辑回归
category: 学习
tags:  模型学习	        
keywords: 逻辑回归  logistic regression R Python  
description: 逻辑回归的原理及软件实现
---

逻辑回归，也称Logistic Regression，属于回归分析的一种，用于预测分类变量，其中主要是二分类变量。

本篇文章依次从目的、原理、参数求解、模型评价、模型优化和软件实现共6个方面依次论述。

如无特殊说明，log均指以自然数为底。

## **目的**

现实生活中存在大量的分类变量预测问题，比如：一部影片是否能获奖，一个客户是否会违约，一个用户是否会流失……，机器学习中有很多解决分类问题的算法，逻辑回归既是其中的一种，逻辑回归可以解决分类问题，主要应用是二分类问题的解决。

## **原理**

我们需要预测一件事情发生的概率，如果用一般线性回归去做预测，即因变量为P(Y=1)，自变量为影响因变量的特征，会出现一个问题，P(Y=1)的取值范围是[0,1]，而线性回归的预测范围却是R，因此，我们有必要通过考虑某种变换，将响应变量作为P(Y=1)的一个函数变换结果，同时将取值范围扩展到R，为此，我们考虑采用logit函数：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=logit(p)=log(\frac{p}{1-p})" target="_blank"><img src="http://latex.codecogs.com/png.latex?logit(p)=log(\frac{p}{1-p})" title="logit(p)=log(\frac{p}{1-p})" /></a>
</div>

之后，我们结合一般线性回归的思路，我们可以得到：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=log(\frac{p}{1-p})=\theta&space;^{T}X" target="_blank"><img src="http://latex.codecogs.com/gif.latex?log(\frac{p}{1-p})=\theta&space;^{T}X" title="log(\frac{p}{1-p})=\theta ^{T}X" /></a></div>

简单推导，得：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=P(Y=1)=\frac{1}{1&plus;e^{-\theta&space;^{T}X}}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?P(Y=1)=\frac{1}{1&plus;e^{-\theta&space;^{T}X}}" title="P(Y=1)=\frac{1}{1+e^{-\theta ^{T}X}}" /></a></div>

这个函数实际上也就是[Sigmoid_function](https://en.wikipedia.org/wiki/Sigmoid_function)，函数表达式为：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=g(x)&space;=&space;\frac{1}{1&plus;e^{-x}}" target="_blank"><img src="http://latex.codecogs.com/png.latex?g(x)&space;=&space;\frac{1}{1&plus;e^{-x}}" title="g(x) = \frac{1}{1+e^{-x}}" /></a></div>

函数图形为（图片引用自[维基百科](https://en.wikipedia.org/wiki/Logistic_regression)）：

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/320px-Logistic-curve.svg.png"></div>

一般情况下，当预测概率P(Y=1)>0.5，我们预估Y=1，反之，预估Y=0,具体阈值可以根据预测结果做相应调整。

现在我们得到了逻辑回归的数学表达式：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=h_\theta(x)&space;=&space;\frac{1}{1&plus;e^{-\theta^Tx}}" target="_blank"><img src="http://latex.codecogs.com/png.latex?h_\theta(x)&space;=&space;\frac{1}{1&plus;e^{-\theta^Tx}}" title="h_\theta(x) = \frac{1}{1+e^{-\theta^Tx}}" /></a></div>

其中θ表示带估计参数,<a href="http://www.codecogs.com/eqnedit.php?latex=h_\theta(x)" target="_blank"><img src="http://latex.codecogs.com/png.latex?h_\theta(x)" title="h_\theta(x)" /></a>表示Y=1的概率，即：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=h_\theta(x)&space;=&space;P(y=1|&space;x;\theta)" target="_blank"><img src="http://latex.codecogs.com/png.latex?h_\theta(x)&space;=&space;P(y=1|&space;x;\theta)" title="h_\theta(x) = P(y=1| x;\theta)" /></a></div>

因此，当预测变量是2分类变量时，我们可以得到Y=0的概率表达形式：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=P(y=0|x;\theta)&space;=&space;1&space;-&space;P(y=1|x;\theta)&space;=&space;1-&space;h_\theta(x)" target="_blank"><img src="http://latex.codecogs.com/gif.latex?P(y=0|x;\theta)&space;=&space;1&space;-&space;P(y=1|x;\theta)&space;=&space;1-&space;h_\theta(x)" title="P(y=0|x;\theta) = 1 - P(y=1|x;\theta) = 1- h_\theta(x)" /></a></div>

进一步，我们可以将Y的概率分布表达为一个式子：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=P(y|x;\theta)&space;=&space;(h_\theta(x))^y(1-h_\theta(x))^{1-y}" target="_blank"><img src="http://latex.codecogs.com/gif.latex?P(y|x;\theta)&space;=&space;(h_\theta(x))^y(1-h_\theta(x))^{1-y}" title="P(y|x;\theta) = (h_\theta(x))^y(1-h_\theta(x))^{1-y}" /></a></div>

有了概率表达式，我们就可以写出样本的联合分布，采用极大似然的思想，通过求解MLE，我们便可以得到一个逻辑回归的解。

以上是从一般的思路推导出来的，从广义线性回归模型推导sigmod函数请参阅参考资料【1】

## **参数求解**

结合概率表达式和极大似然的思想，我们很自然的就写出似然函数表达式：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=L(\theta)&space;=&space;\prod_{i=1}^{m}P(y^{(i)}|x^{(i)};\theta)&space;=\prod_{i=1}^{m}(h_\theta(x^{(i)}))^{y(i)}(1-(h_\theta(x^{(i)}))^{1-y(i)}" target="_blank"><img src="http://latex.codecogs.com/png.latex?L(\theta)&space;=&space;\prod_{i=1}^{m}P(y^{(i)}|x^{(i)};\theta)&space;=\prod_{i=1}^{m}(h_\theta(x^{(i)}))^{y(i)}(1-(h_\theta(x^{(i)}))^{1-y(i)}" title="L(\theta) = \prod_{i=1}^{m}P(y^{(i)}|x^{(i)};\theta) =\prod_{i=1}^{m}(h_\theta(x^{(i)}))^{y(i)}(1-(h_\theta(x^{(i)}))^{1-y(i)}" /></a></div>

取对数，得：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=l(\theta)&space;=&space;\sum_{i=1}^m&space;(y^{(i)}&space;log(h_\theta(x^{(i)}))&space;&plus;&space;(1&space;-&space;y^{(i)})&space;log(1&space;-&space;h_\theta(x^{(i)})))" target="_blank"><img src="http://latex.codecogs.com/png.latex?l(\theta)&space;=&space;\sum_{i=1}^m&space;(y^{(i)}&space;log(h_\theta(x^{(i)}))&space;&plus;&space;(1&space;-&space;y^{(i)})&space;log(1&space;-&space;h_\theta(x^{(i)})))" title="l(\theta) = \sum_{i=1}^m (y^{(i)} log(h_\theta(x^{(i)})) + (1 - y^{(i)}) log(1 - h_\theta(x^{(i)})))" /></a></div>

接下来，我们求解参数θ的MLE

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=\frac{\alpha&space;h_\theta(X)}{\alpha\theta}=-(\frac{1}{1&space;&plus;&space;e^{-\theta^T&space;X}})^{2}e^{-\theta^T&space;X}\frac{\alpha(-\theta^T&space;X)}{\alpha\theta}&space;\\=-(\frac{1}{1&space;&plus;&space;e^{-\theta^T&space;X}})^{2}e^{-\theta^T&space;X}&space;(-X)&space;\\=h_\theta(X)(1-h_\theta(X))X" target="_blank"><img src="http://latex.codecogs.com/png.latex?\frac{\alpha&space;h_\theta(X)}{\alpha\theta}=-(\frac{1}{1&space;&plus;&space;e^{-\theta^T&space;X}})^{2}e^{-\theta^T&space;X}\frac{\alpha(-\theta^T&space;X)}{\alpha\theta}&space;\\=-(\frac{1}{1&space;&plus;&space;e^{-\theta^T&space;X}})^{2}e^{-\theta^T&space;X}&space;(-X)&space;\\=h_\theta(X)(1-h_\theta(X))X" title="\frac{\alpha h_\theta(X)}{\alpha\theta}=-(\frac{1}{1 + e^{-\theta^T X}})^{2}e^{-\theta^T X}\frac{\alpha(-\theta^T X)}{\alpha\theta} \\=-(\frac{1}{1 + e^{-\theta^T X}})^{2}e^{-\theta^T X} (-X) \\=h_\theta(X)(1-h_\theta(X))X" /></a></div>

其中，<a href="http://www.codecogs.com/eqnedit.php?latex=h_\theta(X)" target="_blank"><img src="http://latex.codecogs.com/png.latex?h_\theta(X)" title="h_\theta(X)" /></a>中X为解释变量，为列向量，θ为参数向量，为列向量。

为了便于程序计算，我们将对数似然函数<a href="http://www.codecogs.com/eqnedit.php?latex=l_\theta" target="_blank"><img src="http://latex.codecogs.com/png.latex?l_\theta" title="l_\theta" /></a>写成矩阵表达形式:

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=l_\theta&space;=\begin{pmatrix}&space;log(h_\theta(X_{(1)}))&space;&&space;log(1-h_\theta(X_{(1)}))&space;&&space;log(h_\theta(X_{(2)}))&space;&log(1-h_\theta(X_{(2)}))&space;&&space;......&space;&&space;log(h_\theta(X_{(m)}))&space;&&space;log(1-h_\theta(X_{(m)}))&space;\end{pmatrix}\begin{pmatrix}&space;\\&space;y_{(1)}&space;\\&space;1-y_{(1)}&space;\\&space;y_{(2)}&space;\\&space;1-y_{(2)}&space;\\&space;......&space;\\&space;y_{(m)}&space;\\&space;1-y_{(m)}&space;\end{pmatrix}" target="_blank"><img src="http://latex.codecogs.com/png.latex?l_\theta&space;=\begin{pmatrix}&space;log(h_\theta(X_{(1)}))&space;&&space;log(1-h_\theta(X_{(1)}))&space;&&space;log(h_\theta(X_{(2)}))&space;&log(1-h_\theta(X_{(2)}))&space;&&space;......&space;&&space;log(h_\theta(X_{(m)}))&space;&&space;log(1-h_\theta(X_{(m)}))&space;\end{pmatrix}\begin{pmatrix}&space;\\&space;y_{(1)}&space;\\&space;1-y_{(1)}&space;\\&space;y_{(2)}&space;\\&space;1-y_{(2)}&space;\\&space;......&space;\\&space;y_{(m)}&space;\\&space;1-y_{(m)}&space;\end{pmatrix}" title="l_\theta =\begin{pmatrix} log(h_\theta(X_{(1)})) & log(1-h_\theta(X_{(1)})) & log(h_\theta(X_{(2)})) &log(1-h_\theta(X_{(2)})) & ...... & log(h_\theta(X_{(m)})) & log(1-h_\theta(X_{(m)})) \end{pmatrix}\begin{pmatrix} \\ y_{(1)} \\ 1-y_{(1)} \\ y_{(2)} \\ 1-y_{(2)} \\ ...... \\ y_{(m)} \\ 1-y_{(m)} \end{pmatrix}" /></a></div>

对其求导，我们可以得到：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=\frac{\alpha&space;l_\theta}{\alpha&space;_\theta}&space;=\begin{pmatrix}&space;(1-h_\theta(X_{(1)}))X_1&space;&&space;-h_\theta(X_{(1)})X_1&space;&&space;(1-h_\theta(X_{(2)}))X_2&space;&&space;-h_\theta(X_{(2)})X_2&space;&&space;......&space;&&space;(1-h_\theta(X_{(m)}))X_1&space;&&space;-h_\theta(X_{(1)})X_m&space;\end{pmatrix}\begin{pmatrix}&space;\\&space;y_{(1)}&space;\\&space;1-y_{(1)}&space;\\&space;y_{(2)}&space;\\&space;1-y_{(2)}&space;\\&space;......&space;\\&space;y_{(m)}&space;\\&space;1-y_{(m)}&space;\end{pmatrix}" target="_blank"><img src="http://latex.codecogs.com/png.latex?\frac{\alpha&space;l_\theta}{\alpha&space;_\theta}&space;=\begin{pmatrix}&space;(1-h_\theta(X_{(1)}))X_1&space;&&space;-h_\theta(X_{(1)})X_1&space;&&space;(1-h_\theta(X_{(2)}))X_2&space;&&space;-h_\theta(X_{(2)})X_2&space;&&space;......&space;&&space;(1-h_\theta(X_{(m)}))X_1&space;&&space;-h_\theta(X_{(1)})X_m&space;\end{pmatrix}\begin{pmatrix}&space;\\&space;y_{(1)}&space;\\&space;1-y_{(1)}&space;\\&space;y_{(2)}&space;\\&space;1-y_{(2)}&space;\\&space;......&space;\\&space;y_{(m)}&space;\\&space;1-y_{(m)}&space;\end{pmatrix}" title="\frac{\alpha l_\theta}{\alpha _\theta} =\begin{pmatrix} (1-h_\theta(X_{(1)}))X_1 & -h_\theta(X_{(1)})X_1 & (1-h_\theta(X_{(2)}))X_2 & -h_\theta(X_{(2)})X_2 & ...... & (1-h_\theta(X_{(m)}))X_1 & -h_\theta(X_{(1)})X_m \end{pmatrix}\begin{pmatrix} \\ y_{(1)} \\ 1-y_{(1)} \\ y_{(2)} \\ 1-y_{(2)} \\ ...... \\ y_{(m)} \\ 1-y_{(m)} \end{pmatrix}" /></a></div>

整理：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=y_{1}(1-h_\theta(X_{(1)}))X_{1}-(1-y_{1})h_\theta(X_{(1)})X_{1}&space;\\&space;=(y-{1}-h_\theta(X_{(1)}))X_{1}" target="_blank"><img src="http://latex.codecogs.com/png.latex?y_{1}(1-h_\theta(X_{(1)}))X_{1}-(1-y_{1})h_\theta(X_{(1)})X_{1}&space;\\&space;=(y-{1}-h_\theta(X_{(1)}))X_{1}" title="y_{1}(1-h_\theta(X_{(1)}))X_{1}-(1-y_{1})h_\theta(X_{(1)})X_{1} \\ =(y-{1}-h_\theta(X_{(1)}))X_{1}" /></a></div>

进而

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=\frac{\alpha&space;l_\theta}{\alpha&space;_\theta}&space;=&space;\begin{pmatrix}&space;X_{1}(y_{1}-h_\theta{(X_{1})})&space;&&space;X_{2}(y_{2}-h_\theta{(X_{2})})&space;&&space;......&space;&X_{m}(y_{m}-h_\theta{(X_{m})})&space;\end{pmatrix}\\&space;=\begin{pmatrix}&space;X_{1}&space;&&space;X_{2}&space;&&space;......&space;&&space;X_{m}&space;\end{pmatrix}&space;\begin{pmatrix}(y_{1}-h_\theta{(X_{1})}&space;\\&space;(y_{2}-h_\theta{(X_{2})}&space;\\&space;......&space;\\&space;(y_{m}-h_\theta{(X_{m})}&space;\end{pmatrix}&space;\\=X(Y-h_{\theta}(X))" target="_blank"><img src="http://latex.codecogs.com/png.latex?\frac{\alpha&space;l_\theta}{\alpha&space;_\theta}&space;=&space;\begin{pmatrix}&space;X_{1}(y_{1}-h_\theta{(X_{1})})&space;&&space;X_{2}(y_{2}-h_\theta{(X_{2})})&space;&&space;......&space;&X_{m}(y_{m}-h_\theta{(X_{m})})&space;\end{pmatrix}\\&space;=\begin{pmatrix}&space;X_{1}&space;&&space;X_{2}&space;&&space;......&space;&&space;X_{m}&space;\end{pmatrix}&space;\begin{pmatrix}(y_{1}-h_\theta{(X_{1})}&space;\\&space;(y_{2}-h_\theta{(X_{2})}&space;\\&space;......&space;\\&space;(y_{m}-h_\theta{(X_{m})}&space;\end{pmatrix}&space;\\=X(Y-h_{\theta}(X))" title="\frac{\alpha l_\theta}{\alpha _\theta} = \begin{pmatrix} X_{1}(y_{1}-h_\theta{(X_{1})}) & X_{2}(y_{2}-h_\theta{(X_{2})}) & ...... &X_{m}(y_{m}-h_\theta{(X_{m})}) \end{pmatrix}\\ =\begin{pmatrix} X_{1} & X_{2} & ...... & X_{m} \end{pmatrix} \begin{pmatrix}(y_{1}-h_\theta{(X_{1})} \\ (y_{2}-h_\theta{(X_{2})} \\ ...... \\ (y_{m}-h_\theta{(X_{m})} \end{pmatrix} \\=X(Y-h_{\theta}(X))" /></a></div>

其中<a href="http://www.codecogs.com/eqnedit.php?latex=h_{\theta}(X)" target="_blank"><img src="http://latex.codecogs.com/png.latex?h_{\theta}(X)" title="h_{\theta}(X)" /></a>代表对X中每一行元素<a href="http://www.codecogs.com/eqnedit.php?latex=X_{i}" target="_blank"><img src="http://latex.codecogs.com/png.latex?X_{i}" title="X_{i}" /></a>运用<a href="http://www.codecogs.com/eqnedit.php?latex=h_{\theta}(X_{i})" target="_blank"><img src="http://latex.codecogs.com/png.latex?h_{\theta}(X_{i})" title="h_{\theta}(X_{i})" /></a>

MLE并不像OLS估计那样，直接可以得到解的表现形式，因此，我们采用其它的办法来求解参数，常用的是梯度上升法，即给定步长（step size），迭代次数或者精度，通过迭代来求解一个局部最优解。

由上面的推导，我们可以得出梯度上升法的一般迭代公式，γ是步长：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=\theta=\theta&plus;\gamma&space;X(Y-h_{\theta}(X))" target="_blank"><img src="http://latex.codecogs.com/png.latex?\theta=\theta&plus;\gamma&space;X(Y-h_{\theta}(X))" title="\theta=\theta+\gamma X(Y-h_{\theta}(X))" /></a></div>

上面的推导是由最大似然求解出发，我们也可以考虑从cost function出发，逻辑回归中损失函数采用的是对数损失函数，表达形式如下：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=Cost(h_\theta(x),&space;y)&space;=&space;\left\{\begin{matrix}&space;-log(h_\theta(x))&&space;y&space;=&space;1&space;\\&space;-log(1&space;-&space;h_\theta(x))&&space;y&space;=&space;0&space;\end{matrix}\right." target="_blank"><img src="http://latex.codecogs.com/png.latex?Cost(h_\theta(x),&space;y)&space;=&space;\left\{\begin{matrix}&space;-log(h_\theta(x))&&space;y&space;=&space;1&space;\\&space;-log(1&space;-&space;h_\theta(x))&&space;y&space;=&space;0&space;\end{matrix}\right." title="Cost(h_\theta(x), y) = \left\{\begin{matrix} -log(h_\theta(x))& y = 1 \\ -log(1 - h_\theta(x))& y = 0 \end{matrix}\right." /></a></div>

我们可以得到损失函数为：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=C(\theta)=\frac{1}{m}\sum_{i=1}^{m}Cost(h_\theta(x^{(i)}),y^{(i)})&space;\\=-\frac{1}{m}&space;\left[&space;\sum_{i=1}^m&space;y^{(i)}&space;log(h_\theta(x^{(i)}))&space;&plus;&space;(1&space;-&space;y^{(i)})&space;log(1&space;-&space;h_\theta(x^{(i)}))&space;\right]" target="_blank"><img src="http://latex.codecogs.com/png.latex?C(\theta)=\frac{1}{m}\sum_{i=1}^{m}Cost(h_\theta(x^{(i)}),y^{(i)})&space;\\=-\frac{1}{m}&space;\left[&space;\sum_{i=1}^m&space;y^{(i)}&space;log(h_\theta(x^{(i)}))&space;&plus;&space;(1&space;-&space;y^{(i)})&space;log(1&space;-&space;h_\theta(x^{(i)}))&space;\right]" title="C(\theta)=\frac{1}{m}\sum_{i=1}^{m}Cost(h_\theta(x^{(i)}),y^{(i)}) \\=-\frac{1}{m} \left[ \sum_{i=1}^m y^{(i)} log(h_\theta(x^{(i)})) + (1 - y^{(i)}) log(1 - h_\theta(x^{(i)})) \right]" /></a></div>

与似然函数<a href="http://www.codecogs.com/eqnedit.php?latex=l(\theta)" target="_blank"><img src="http://latex.codecogs.com/png.latex?l(\theta)" title="l(\theta)" /></a>相比，只有常数项和符号的不同，我们的目的是让损失函数最小，因此，我们可以采用梯度下降法来求解参数，实质上，利用梯度下降法来求解损失函数和梯度上升法求解似然函数是一样的，模型的最终迭代公式都是

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=\theta=\theta&plus;\gamma&space;X(Y-h_{\theta}(X))" target="_blank"><img src="http://latex.codecogs.com/png.latex?\theta=\theta&plus;\gamma&space;X(Y-h_{\theta}(X))" title="\theta=\theta+\gamma X(Y-h_{\theta}(X))" /></a></div>

除了梯度上升法，还有其他的求解方法，如：共轭梯度法，牛顿法，详情参阅参考资料【11】

## **模型评价**

评价一个模型好坏的出发点是模型的预测结果与真实结果之间的差异，对于分类结果，我们可以采用准确度来评价模型的优劣，简单来讲，模型的准确度(accuracy)=模型预测正确结果数在所有结果中的占比。模型的预测结果，可以细分为四类：

* 正确的分到正确（TP）
* 正确的分到不正确（FN)
* 不正确的分到不正确(TN)
* 不正确的分到正确(FP)

准确度的形式为：

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=accuracy&space;=&space;\frac{TP&plus;TN}{TP&plus;FP&plus;TN&plus;FN}" target="_blank"><img src="http://latex.codecogs.com/png.latex?accuracy&space;=&space;\frac{TP&plus;TN}{TP&plus;FP&plus;TN&plus;FN}" title="accuracy = \frac{TP+TN}{TP+FP+TN+FN}" /></a></div>

我们还可以用AUC进行评价，AUC的得分从0.5（无预测价值的分类器）到1.0（完美的分类器），逻辑回归中AUC的计算可以通过计算正样本得分大于负样本得分的样本对占所有样本对的比例来得到。假设有M个正样本，N个负样本，则正负样本配对数为MN，计算预测结果中，正样本得分大于负样本得分的样本对数量，然后得到AUC值。



> <font size='3' face='Georgia'> **比如，我们有四组数据，正负样本情况为:y1=+1, y2=+1, y3=-1, y4=-1。** </font>

> <font size='3' face='Georgia'> **模型1的预测为 y1=0.9, y2=0.5, y3=0.2, y4=0.6**</font>

> <font size='3' face='Georgia'> **模型2的预测为 y1=0.1, y2=0.9, y3=0.8, y4=0.2** </font>

> <font size='3' face='Georgia'> **模型1： 正样本score大于负样本的pair包括(y1, y3), (y1, y4), (y2, y3)，auc为3/4=0.75**</font>

> <font size='3' face='Georgia'> **模型2： 正样本score大于负样本的pair包括(y2, y3),(y2, y4)，auc为2/4=0.5**</font>

> <font size='3' face='Georgia'> **在AUC准则下，模型1要优于模型2。**</font>
 
## **模型优化**

实际应用中，模型的拟合结果会出现三种结果：欠拟合，拟合良好，过拟合。三种的表现形式可以用下图来表示（引用自斯坦福机器学习公开课）：

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/1405590306_6011.png"></div>

如果模型是欠拟合，我们就要考虑添加特征变量

如果是过拟合，我们通常考虑减少特征数量或者保留所有特征，但使θ尽量小，即正则化。

我们通过在似然函数或cost function 中添加正则化项来实现正则化，正则化的详细介绍请参阅参考资料 【8】、【7】

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=C(\theta)=-\frac{1}{m}&space;\left[&space;\sum_{i=1}^m&space;y^{(i)}&space;log(h_\theta(x^{(i)}))&space;&plus;&space;(1&space;-&space;y^{(i)})&space;log(1&space;-&space;h_\theta(x^{(i)})&plus;&space;\lambda&space;\sum_{j=1}^n&space;\theta_j^2))&space;\right]" target="_blank"><img src="http://latex.codecogs.com/gif.latex?C(\theta)=-\frac{1}{m}&space;\left[&space;\sum_{i=1}^m&space;y^{(i)}&space;log(h_\theta(x^{(i)}))&space;&plus;&space;(1&space;-&space;y^{(i)})&space;log(1&space;-&space;h_\theta(x^{(i)})&plus;&space;\lambda&space;\sum_{j=1}^n&space;\theta_j^2))&space;\right]" title="C(\theta)=-\frac{1}{m} \left[ \sum_{i=1}^m y^{(i)} log(h_\theta(x^{(i)})) + (1 - y^{(i)}) log(1 - h_\theta(x^{(i)})+ \lambda \sum_{j=1}^n \theta_j^2)) \right]" /></a></div>

其中λ是正则化参数，决定了参数正则化的影响大小，如果λ过大，可能会使模型出现欠拟合问题。

正则化后的迭代公式为

<div align="center"><a href="http://www.codecogs.com/eqnedit.php?latex=\theta=\theta&plus;\gamma&space;(X(Y-h_{\theta}(X))&plus;&space;\lambda&space;\theta)" target="_blank"><img src="http://latex.codecogs.com/gif.latex?\theta=\theta&plus;\gamma&space;(X(Y-h_{\theta}(X))&plus;&space;\lambda&space;\theta)" title="\theta=\theta+\gamma (X(Y-h_{\theta}(X))+ \lambda \theta)" /></a></div>

## **软件实现**

纸上得来终觉浅，绝知此事要躬行，原理清楚之后，关键是实践操作，采用R和Python来实现逻辑回归。

* R 
     
    逻辑回归属于广义线性模型，在R中实现比较简单，调用glm函数即可。
   
    glm函数的语法为：

          glm(formula,family=binomial,data=data.frame）

    其中formula是待输入的公式，同线性回归一样，只是公式的左边可以有两种输入方法，一种是输入成功和失败的次数，一种是输入Y值。其实这里的Y值指的就是胜率(odds ratio)的对数值

* Python 
  
    《机器学习实战》一书中，给出了逻辑回归的部分代码，这部分代码实际上就是本文的参数求解部分：

        def sigmoid(inX):  #sigmod function  
              return 1.0/(1+exp(-inX))  
        def gradAscent(dataArray,labelArray,alpha,maxCycles):
              dataMat=mat(dataArray)    #size:m*n
              labelMat=mat(labelArray)      #size:m*1
              m,n=shape(dataMat)
              weigh=ones((n,1)) 
              for i in range(maxCycles):
                 h=sigmoid(dataMat*weigh)
                 error=labelMat-h    #size:m*1
                 weigh=weigh+alpha*dataMat.transpose()*error
              return weigh

     Python 有很好的机器学习库：sklearn，我们直接调用学习即可，以下是一个例子，代码是2.X版本：

        from numpy import * 
        from sklearn.datasets import load_iris     # import datasets
        # load the dataset: iris
        iris = load_iris() 
        samples = iris.data
        #print samples 
        target = iris.target 
        # import the LogisticRegression
        from sklearn.linear_model import LogisticRegression 

        classifier = LogisticRegression()  # 使用类，参数全是默认的
        classifier.fit(samples, target)  # 训练数据来学习，不需要返回值

        x = classifier.predict([5, 3, 5, 2.5])  # 测试数据，分类返回标记

        print x 

## **结束语**
  
逻辑回归是机器学习中实用高效的一种算法，本篇文章只讲述了一些基本的原理及实现，更深的内容还需要学习更多的内容。

## **参考资料**

【1】 [Where does sigmoid come from](http://synckey.github.io/posts/2015/12/24/where-does-sigmoid-come-from.html)

【2】 [机器学习之正则化（Regularization）](http://doc.okbase.net/jianxinzhou/archive/111322.html)

【3】[逻辑回归](http://blog.csdn.net/pakko/article/details/37878837)

【4】[ Stanford机器学习---第三讲. 逻辑回归和过拟合问题的解决 logistic Regression & Regularization](http://blog.csdn.net/abcjennifer/article/details/7716281)

【5】[【机器学习笔记1】Logistic回归总结](http://blog.csdn.net/dongtingzhizi/article/details/15962797)

【6】[Gradient descent](https://en.wikipedia.org/wiki/Gradient_descent)

【7】[机器学习笔记4 正则化](http://nanshu.wang/post/2015-02-17/)

【8】[机器学习中的范数规则化之（一）L0、L1与L2范数](http://blog.csdn.net/zouxy09/article/details/24971995/)

【9】[为什么CTR预估使用AUC来评估模型？](https://github.com/geeseek/MLToolkits/wiki/为什么CTR预估使用AUC来评估模型？)

【10】[调用python的sklearn实现Logistic Reression算法](http://www.itnose.net/detail/6197189.html)

【11】[最优化](http://www.codelast.com/tag/最优化/)