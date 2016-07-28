---
layout: post
title: 地铁遐想 
category: 学习
tags:  数学运算	        
keywords: 
description: 地铁时光消遣
---

打发无聊的地铁时光……

### 1. 计算5562组坐标点之间的距离并挑选出距离不大于200的点组合

实现语言：R

    data <- read.table(file.choose(),header=T)
    loopn <- dim(data)[1]-1
    AimData <- data.frame()
    ptm <- proc.time()#记录时间
    for ( i in 1:loopn){
    demo <- data[-c(1:i),]
    demo$x0  <- data[i,1]
    demo$y0 <- data[i,2]
    demo$dist <- (demo[,1]-demo[3])^2+(demo[,2]-demo[4])^2
    AimDatademo <- demo[which(demo$dist <= 40000),]
    AimData <- rbind(AimData,AimDatademo)
    }
    proc.time() - ptm
    write.csv(AimData,'AimData.csv',row.names=F)

缺点：计算时间太长，即使采用向量化计算，平均一个循环需要1.06s,采用`fortran`应该更快吧！

### 2.  将N元钱随机分给M个人，并设定每个人的金额范围

实现语言：R

思路：考虑每次随机金额的设定范围以及每次随机金额发放后的随机范围

    RP <- function(N,M,a,b){
    RM <- c()
    for ( i in 1:(M-1)){
    PreSum <- sum(RM)
    min1 <- N-PreSum-(M-i)*b
    max1 <- N-PreSum-(M-i)*a
    inf <- max(a,min1)
    sup <- min(b,max1)
    if (sup < inf)break
    rm <- (sup-inf)*runif(1)+inf
    rm <- round(rm,2)
    RM <- append(RM,rm)
    }
    if (sup < inf)print('随机金额设置有误')
    last <- N-sum(RM)
    RM <- append(RM,last)
    if(sup >= inf)return(RM)
    }

