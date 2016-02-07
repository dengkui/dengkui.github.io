---
layout: post
title:  抓取新发地价格数据（Python版）
category: 学习
tags:  数据抓取        
keywords: Python 
description: 利用Python抓取网页数据
---
之前利用乐思软件来抓取新发地的价格信息，结果还不错，可惜的是软件要收费，想要随时使用是不大可能的，秉承着“艰苦奋斗，自力更生”的原则，还是利用开源软件来抓取数据比较靠谱……

Python作为一门工程语言，在数据抓取方面还是比较方便的。于是乎，捣鼓了一段时间，终于成功地抓取了数据，以下是代码(写的比较水）：

    #引进各种库
    import urllib
    from bs4 import BeautifulSoup
    from pandas import Series,DataFrame
    import pandas as pd	
    from numpy import *
    import os

    #获取不同类型的网页地址
    url='http://www.xinfadi.com.cn/marketanalysis/1/list/1.shtml'#目标网址
    html=urllib.request.urlopen(url) #打开网页
    html_soup=BeautifulSoup(html) #对网页进行解析
    demo=html_soup.find_all('div',class_='hq_left_1')#寻找包含不同类型网址的数据
    url_list=list([])
    for item in demo:
        url_list.append(item.find_all('a'))
    for item in url_list[0]:
        style_url[item.string]=item['href']
    del style_url['每日价格行情']    
    style_url   #不同类型的网址

    #抓取数据
    for key in style_url:
        url='http://www.xinfadi.com.cn'+style_url[key]
        price_table=DataFrame(array(['品名','最低价','平均价','最高价','规格','单位','发布日期','其他']).reshape(1,8))
        i=1
        while i <= 5: #设置只抓取前5页
            html=urllib.request.urlopen(url)
            html_soup=BeautifulSoup(html)
            price=html_soup.find_all('table',class_='hq_table') #找到价格信息所在
            a=list([])
            for item in price:
                a.append(item.find_all('td'))
            b=list([])
            for item in a[0]:
                b.append(item.get_text())
            result1=Series(b)
            result2=DataFrame(result1.reshape(21,8)) #对价格数据进行整理
            result=result2.drop([0])
            price_table=pd.concat([price_table,result])
            next_pos=html_soup.find_all(title='下一页')  #寻找下一页标签
            next_page=next_pos[0]
            next_page_url='http://www.xinfadi.com.cn'+next_page['href']
            url=next_page_url  #更新url
            i += 1
        file_name=key+'.csv'
        price_table.to_csv(file_name,header=False) #将整理后的数据写入csv格式文档

    os.getcwd() #查看文档存放地址

抓取后的数据存放文件是这样的：

<img src="http://7xo51k.com1.z0.glb.clouddn.com/xinfadi%E6%96%B0%E5%8F%91%E5%9C%B01.png-wx" align=center />

打开之后，是这样的：


<img src="http://7xo51k.com1.z0.glb.clouddn.com/xinfadi%E6%96%B0%E5%8F%91%E5%9C%B02.jpg-wx" align=center />

和乐思软件抓取结果毫无二致，呵呵!

##参考资料

【1】[Beautiful Soup 4.2.0 文档](http://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)

【2】[Python爬虫怎么获取下一页的URL和网页内容？](https://segmentfault.com/q/1010000003734875)

【3】《Python for Data Analysis》

【4】《html,css,javascript网页制作从入门到精通》


