---
layout: post
title:  Python 豆瓣电影排行榜抓取
category: 学习
tags:  数据抓取 
keywords: Python 数据抓取  豆瓣电影排行榜
description: 利用Python抓取股吧帖子信息
---

本篇文章利用Python3爬取了[豆瓣电影排行榜TOP250](https://www.douban.com/doulist/240962/?start=0&sort=seq&sub_type=)的电影数据，包括电影名称，评分，评分人数，导演，主演，类型，制片地区，年份。


因为代码较为简单，故没有标准化编写，以下是代码：

    import pandas as pd
    import numpy as np
    from bs4 import BeautifulSoup
    import re
    import requests

    headers = {'User-Agent':'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1650.63 Safari/537.36'}

    title = [];Href = [];Rating = [];rateNum = [];director = []
    actor = [];type = [];area = [];date = []
    n = 1
    href = 'https://www.douban.com/doulist/240962/?start=0&sort=seq&sub_type='
    while n <250:
        resp=requests.get(href,headers=headers)
        html = BeautifulSoup(resp.text,'lxml') 
        Info = html.find_all(class_="bd doulist-subject")#以列表形式输出所有的电影信息
        for item in Info:
            Title= item.find(class_="title")#电影标题信息
            title.append(Title.text.strip())#电影标题
            Href.append(Title.find('a').get('href'))#电影链接
            Rating.append(item.find(class_='rating_nums').text)#电影评分信息
            RateNum = item.find_all('span')[2].text
            rateNum.append(re.findall('\d+',RateNum)[0]) #评论人数
            Abstract= item.find(class_="abstract")#电影概述
            Director =  Abstract.contents[0].strip()
            director.append(re.split(':',Director)[1])#导演
            Actor =  Abstract.contents[2].strip()
            actor.append(re.split(':',Actor)[1])#演员
            Type =  Abstract.contents[4].strip()
            type.append(re.split(':',Type)[1])#类型
            Area =  Abstract.contents[6].strip()
            area.append(re.split(':',Area)[1])#地区
            Date =  Abstract.contents[8].strip()
            date.append(re.split(':',Date)[1])#日期
        n = len(title)
        print('已抓取TOP%s部电影'%n)
        next = html.find_all('a',text=re.compile('后页'))[0]
        href = next.get('href')
    
    movie_data = {'Title':title,'Href':Href,'Rating':Rating,'RateNum':rateNum,'Director':director,'Actor':actor,'Type':type,'Area':area,'Date':date}
    Movie_data = pd.DataFrame(movie_data)
    Movie_data.to_csv('Movie_douban.csv',header=True)  #输出到csv文件

## 参考资料

【1】[Beautiful Soup 4.2.0 文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html#id28)