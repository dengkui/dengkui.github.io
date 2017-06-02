---
layout: post
title:  Python 股吧帖子数据抓取
category: 学习
tags:  数据抓取 
keywords: Python 数据抓取  股吧帖子
description: 利用Python抓取股吧帖子信息
---

本篇文章利用Python3对东方财富网的股吧帖子及评论数据进行抓取，并保存为两个数据文件。帖子数据文件包含的信息有：帖子标题，帖子url，帖子发帖时间，帖子浏览量，帖子回复量；评论数据文件包含的信息有：帖子标题，发帖时间，评论内容，评论时间。

爬虫代码为[StockSpider.py]({{site.url}}/assets/Pycode.zip)。抓取方法为spider,参数设置依次为：**抓取股票的股吧按照发帖时间排序的第一页url**，抓取起始页面，抓取结束页面，股票名称。

在抓取时部分帖子出现异常，经调试发现原因为：帖子实际已经不存在，但在帖子列表页面仍然存在，导致打开帖子具体页面时出错，针对这种现象，调查发现出现这种错误的帖子的浏览量明显偏低，因此通过if语句对其进行过滤。

具体代码如下：

    import pandas as pd
    import numpy as np
    from bs4 import BeautifulSoup
    import re
    import requests

    f = lambda x: x.text.strip()#去除空字符串

    class Spider():

        def __init__(self,url,start,stop,name):
            self.url = url		
            self.start = start
            self.stop = stop
            self.name = name
    
        def time_re(self,str):
            return(re.findall('\d+[-]\d+[-]\d+\s{2}\d+[:]\d+[:]\d+',str.text)[0])
	
        def test(self):
            print(self.url)

        def spider(self):
            headers = {'User-Agent':'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1650.63 Safari/537.36'}
            TZ_data = pd.DataFrame()
            HT_data = pd.DataFrame()
            i = self.start
            while i <= self.stop: #设定抓取页数
                next = ',f_'+str(i)+'.html'#按照发帖时间排序
                href = self.url.replace(',f_1.html',next)#股吧帖子列表页面
                resp=requests.get(href,headers=headers) #获取股吧页面
                html = BeautifulSoup(resp.text,'lxml') #股吧页面解析
                Title= html.find_all(class_="articleh")
                for item in Title:
                    if item.find_all('em')==[] or item.find_all('em')[0].text=='':
                    info = item.find_all('span',class_='l3')[0].find_all('a')[0]#每个帖子的具体信息
                    LL= int(item.find_all('span',class_="l1")[0].contents[0])#判断帖子是否异常的指标
                    if LL > 50 :#将浏览量不大于50的帖子过滤掉
                        hrefT = 'http://guba.eastmoney.com'+ info['href'] #帖子链接
                        respT=requests.get(hrefT,headers=headers) #获取帖子信息
                        htmlT = BeautifulSoup(respT.text,'lxml') #帖子首页页面解析
                        fttime_html= htmlT.find_all(class_="zwfbtime")
                        fttime = re.findall('\d+[-]\d+[-]\d+\s\d+[:]\d+[:]\d+',fttime_html[0].contents[0])[0]#发帖时间
                        biaoti_text = htmlT.find_all(id="zwconttbt")[0].text#发帖标题
                        biaoti_text = biaoti_text.strip()#去除空字符串
                        biaoti_info = htmlT.find_all(class_="stockcodec")[0].text#标题详细内容     未去除空字符串
                        biaoti_info = biaoti_info.strip()#去除空字符串
                        biaoti = {'time':[fttime],'tiezi':[biaoti_info]}
                        data = pd.DataFrame(biaoti,columns=['time','tiezi'])#帖子的标题发帖时间信息，每次更新
                        tiezi_info = htmlT.find_all(text=re.compile('var num=\d+;var count=\d+'))#根据标签内容查找
                        ll,xg = re.findall('\d+',tiezi_info[0])
                        pinglun = htmlT.find_all(text=re.compile('var pinglun_num=\d+'))
                        pinglun= re.findall('var pinglun_num=\d+',pinglun[0])
                        pl = re.findall('\d+',pinglun[0])[0]
                        tiezi = {'发帖时间':fttime,'标题':biaoti_text,'浏览量':ll,'评论量':pl,'相关帖子':xg,'URL':hrefT}  #
                        tiezi_data = pd.DataFrame(tiezi,index=['0']) #每次都更新
                        HT_data_sub = pd.DataFrame()
                        j = 1
                        print(biaoti_text)
                        ply = np.ceil(int(pl)/30)
                        while j <= ply:  #抓取全部评论
                            nextT = '_'+str(j)+'.html'
                            hrefTJ = hrefT.replace('.html',nextT)#帖子页面
                            respT = requests.get(hrefTJ,headers=headers) #获取帖子页面
                            htmlT = BeautifulSoup(respT.text,'lxml') #帖子页面解析
                            huitie_time = htmlT.find_all('div',class_="zwlitime")
                            huitie_time = list(huitie_time)
                            huitie_time = pd.Series(huitie_time)
                            huitie_time = huitie_time.apply(self.time_re)
                            huitie_info = htmlT.find_all('div',class_="zwlitext stockcodec")
                            huitie_info = pd.Series(list(huitie_info))
                            huitie_info = huitie_info.apply(f)
                            huitie_data = {'time':huitie_time,'tiezi':huitie_info}
                            huitie_data = pd.DataFrame(huitie_data,columns=['time','tiezi'])#每次都更新
                            HT_data_sub = pd.concat([HT_data_sub,huitie_data]) #不同的帖子不同的数据
                            #HT_data_sub = HT_data_sub.fillna(value='无效回复')
                            #HT_data_sub = HT_data_sub[HT_data_sub['tiezi']!='']
                            #print(HT_data_sub)
                            #print(j)
                            j += 1
                        data = pd.concat([data,HT_data_sub])#添加第一行为标题
                        data['title']=biaoti_text#添加标题列
                        data['fftime']=fttime#添加发帖时间列
                        TZ_data = pd.concat([TZ_data,tiezi_data])#所有帖子信息
                        HT_data = pd.concat([HT_data,data])#所有帖子的回帖信息
                        #HT_data = HT_data[HT_data['tiezi']!='']
                print('第%s页抓取完毕！'%i)
                i += 1
            print('爬取完毕,共爬取%s第%s页到第%s页帖子数据' %(self.name,self.start,self.stop))
            return(TZ_data,HT_data)

        if __name__ == '__main__':
            print('请输入爬取网址及爬取页数!') 

我们通过运行爬虫代码进行抓取，以工商银行的帖子为例

    import StockSpider as ss #引入模块
    import pandas as pd
    import numpy as np
    GS = ss.Spider('http://guba.eastmoney.com/list,601398,f_1.html',10,20,'工商银行')
    TZ,HT = GS.spider()#第一个数据是帖子信息数据，第二数据是帖子回复数据    

抓取完毕后，数据就保存在TZ,HT两个文件中了。

