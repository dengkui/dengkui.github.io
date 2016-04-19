---
layout: post
title:  R--可视化展示及代码
category: 学习
tags:  可视化        
keywords: R  可视化 
description: 生活学习中的可视化案例总结
---

本篇文章用以记录生活学习中遇到的可视化案例结果，仅作案例总结及展示使用，不展示数据，不解释代码。

*  **玫瑰图**

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/rose.png-wx"  ></div>
    
    wats_data$Date <- as.Date(wats_data$date,'%Y/%m/%d')
    pos <- seq(1,61,by=4)
    ggplot(wats_data,aes(Date,change))+geom_bar(stat="identity",aes(fill = change))+scale_fill_gradient(low = "red", high = "green")+coord_polar() +ylab('change')+xlab('time')+ scale_x_date(breaks= wats_data$Date[pos])
*  **热力图**

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/contour1.png-wx"  ></div>    

    ggplot(data= contour1,aes(x=year,y=released,fill=soldperyr))+geom_tile()+xlab('Year')+ylab("released")+scale_fill_gradient('sold per year',low='lightblue', high="red",labels=c('0','10000','200000','300000','400000'))+geom_text(aes(label = soldperyr),size=3)+scale_x_continuous(breaks=c(2010,2011,2012,2013,2014,2015))
*  **时序图**

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/tsplot.png-wx"  ></div>   

    qplot(Date,fans,data=wats_data,geom='line')+geom_point(aes(x=Date,y=fans),colour='red')+ylab('inspirits')
    #修改日期为英文请修改控制面板中的时间和区域为英文