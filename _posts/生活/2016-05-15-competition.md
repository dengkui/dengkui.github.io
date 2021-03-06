---
layout: post
title:  比赛心得
category: 生活
tags:  有所思
keywords: 
description: 
---

过完五一之后，就一直忙着参加比赛，虽然没有达到预期的结果，但还是学到了不少东西，在此总结一下，这篇文档仅对比赛的非技术方面进行总结。

一定要**好好读题，理解题意**！理解题意，可以少走很多弯路。这次比赛中，预测用户换机概率，最初理解为二分类问题，直接采用了决策树，以及各种元集成方法，但是无奈，成绩一直徘徊不前，直到后来，理解到题目建议提交换机概率，而不是直接的换机结果，采用LR，分数一下就上去了。总之，理解题意是做好比赛的第一步。

一定要勤于**记录**！比赛中一定要勤于记录，将每次提交结果存放在单独文件夹中，同时代码也应标注相应情况。后续的改进可以参考之前的代码及结果。

一定要**手脑并用**！一旦有想法一定要实现，不能懒，最好每天提交一次结果，验证思路。

代码书写一定要**规范**！比赛中虽然写了2000多行代码，但还是感觉到不成体系，很乱，不利于思考问题。最好还是将所有一致的需求写成函数，整齐而美观，比如数据处理部分，对所有的数据集做统一的处理，直接写一个函数，运行就可以了，千万不要**一个一个地写**！费时费力不讨好！

结果提交一定要**认真**！比赛中间，由于不认真浪费了两次测评结果，两次是上传完毕后，忘了点提交就关了电脑，一次是文件格式不正确，导致无法测评。其中，第一次是12点半后提交的结果，当时已是天淡夜云舒，时见疏星度，已有困意了。第二次是中午提交结果，但感觉整个人很累，没有休息好，结果就出了差错，休息好也是很重要的。

这次比赛，从开始到初赛结束，都是一个人在做，感觉很累，尤其是排名不断下降，思路停滞不前时，有条件的话还是团体比赛，几个人可以方便地做模型融合。从第一次提交结果开始，每天都在不断地调试，调参真是个体力活，尤其是软件不断抛出‘Memory Error’时。每天早上醒来第一件事就是看昨天提交结果的分数，虽然这里的昨日已非严格的昨日。

比赛中也有高兴的事情，尤其是看到自己的想法验证正确时。最初提交的结果有一列数算错了，竟然还排到了14，当时真是哭笑不得，当然后来排名下来了。

比赛的技术总结另起一篇，还是先看看比赛大神们的想法吧。









