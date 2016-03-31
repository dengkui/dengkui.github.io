---
layout: post
title:  抓取人人贷散标及借款人数据（Python版）
category: 学习
tags:  数据抓取        
keywords: Python  抓取人人贷
description: 利用Python抓取人人贷散标及借款人数据
---
应同学之请求，试着抓取了人人贷网站上[散标投资数据](http://www.we.com/fund/fundInfoAction!tolist.action)和借贷人的个人信息数据。

虽然之前折腾了两个网站的抓取，但这个网站的抓取真叫人**抓狂**，主要遇到的是两个问题

1. 散标投资网站不同页面的源代码相同，虽然不同页面显示的数据不同！（**莫名其妙！**）
2. 借贷人个人信息需要登录之后才能抓取

首先解决第一个问题：既然不同页面的数据显示是不相同的，那么数据来源肯定不同，需要找到真正的数据来源网址。 采用Chrome的F12-Network-XHR来查看请求，在请求中找到数据并查找来源，通过不断的点击不同页面，发现不同页面数据的真正来源。之后按照一般思路就可以解决了，还好数据是Json格式的，方便处理。

对于第二个问题：一般的解决思路是模拟浏览器登录网站，登录成功后再抓取网页并解析。这个看似简单的思路却折腾时间最长，主要原因还是对浏览器登录访问网站的原理不清楚。我们可以通过提交用户名和密码来登录网站，登录之后，通过向服务器发送带Cookie的请求，就可以实现登录了。我这里使用一个简单的方法，算是偷懒了，直接采用登录后的Cookie。因为用Python生成的Cookies没有成功登录，所以我就抓取了网站登录成功后的Cookie来构建headers，之后利用这个headers发送post请求。

模拟登陆时所使用的`username`和`password`是登陆网页中登陆框对应的tag中的name,不同的网站可能不一样。

用户信息是一个表单数据，而且其中还有隐含的数据`信用评分`,所以我设置了一个判断，来抓取不同的数据，并在最后将这些数据添加到字典中，最后转换为DataFrame，剩下的思路就和前面的两篇一致了，就不啰嗦了。（**话说字典功能真不错~**）

以下是代码，代码写的比较水：

    from pandas import Series,DataFrame
    import pandas as pd	
    import numpy as np
    import requests
    import json
    import time
    import random
    from bs4 import BeautifulSoup
    import re

    #抓取散标信息
    headers = {'User-Agent':'Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1650.63 Safari/537.36'}#设置headers，通过查看浏览器数据得到
    column = ['allowAccess', 'amount', 'amountPerShare', 'beginBidTime', 'borrowerId',
       'borrowerLevel', 'currentIsRepaid', 'displayLoanType', 'finishedRatio',
       'forbidComment', 'interest', 'interestPerShare', 'leftMonths', 'loanId',
       'loanType', 'months', 'nickName', 'oldLoan', 'openTime', 'overDued',
       'principal', 'productId', 'readyTime', 'repaidByGuarantor',
       'startTime', 'status', 'surplusAmount', 'title', 'utmSource']
    databid=DataFrame(np.array(column).reshape(1,29),columns= column)
    i=1
    while i<= 51 :
        url = "http://www.we.com/lend/loanList!json.action?pageIndex=%s&" % i
        resp=requests.get(url,headers=headers) #获取页面
        html=resp.text #页面文字
        data_dic = json.loads(html)
        data=DataFrame(data_dic['data']['loans'],columns=column)
        databid=pd.concat([databid,data])
        i += 1

    databid.to_csv('人人贷.csv',header=False) #将整理后的数据写入csv格式文档
    #抓取借贷人信息
    datauser=DataFrame(np.array(['严重逾期', '还清笔数', '信用评分', '信用额度', '年龄', '车贷', '房贷', '逾期次数', '公司规模', '岗位职位', '待还本息', '工作时间', '车产', '逾期金额', '学历', '成功借款', '公司行业', '借款总额', '房产', '婚姻', '收入', '申请借款', '工作城市', '昵称']).reshape(1,24),columns=['严重逾期', '还清笔数', '信用评分', '信用额度', '年龄', '车贷', '房贷', '逾期次数', '公司规模', '岗位职位', '待还本息', '工作时间', '车产', '逾期金额', '学历', '成功借款', '公司行业', '借款总额', '房产', '婚姻', '收入', '申请借款', '工作城市', '昵称'])
    s=requests.session()
    headers['Cookie']=' rrdLoginCartoon=rrdLoginCartoon; newuser=new; Hm_lvt_71ce3105a964d0c3748eaf0b9=147060777,14778309,14715739,147306829; Hm_lpvt_71ce3105a964d0c3748e04584a5af0b9=145307045; Hm_lvt_254723880b5ae69d69cae60a725236c=147060777,147078309,147157739,147306829; Hm_lpvt_2547238861b5ae69d69cae60a725236c=147307045; Hm_lvt_16f9bb97b83369e62ee0386631124bb1=147227320,147227562,147231071,147305815; Hm_lpvt_16f9bb97b83369e62ee1386631124bb1=147308752; JSESSIONID=14343A739C682A0602A5171155C3132B1E2B35EBA7FDCBEE9B99BDC5C89F87F8; jforumUserInfo=iTl5UMRb1UCOWMLizCyIgL4BGPO%0A; IS_MOBLIE_IDPASS=true-false; activeTimestamp=5009119; renrendaiUsername=""'#通过获取网页请求数据可得,有改动
   
    loanId = list(databid['loanId'])
    i = 1
    num = len(loanId)-1
    while i <= num:
        loanid=loanId[i]
        timestamp=str(int(time.time())) + '%03d' % random.randint(0,999)
        urll="http://www.we.com/lend/detailPage.action?loanId=%.0f&timestamp=" % loanid+timestamp
        result = s.get(urll,headers=headers)
        html = BeautifulSoup(result.text,'lxml')
        info = html.find_all('table',class_="ui-table-basic-list")
        info1= info[0]
        info2 = info1.find_all('div',class_="basic-filed")
        userinfo = {}
        for item in info2:
            vartag = item.find('span')
            var = vartag.string
            if var == '信用评级':
                var = '信用评分'
                pf1 = repr(item.find('em'))
                value = re.findall(r'\d+',pf1)
            else:
                valuetag = item.find('em')
                value = valuetag.string
            userinfo[var]=value
        data = DataFrame(userinfo)
        datauser=pd.concat([datauser,data])
        i += 1

    datauser['loanId']=loanId
    datauser.to_csv('userinfo.csv',header=False)

到这儿基本的数据抓取就完成了，剩下的任务就是对数据进行整理清洗。在个人信息数据中，常常包含有'100.00元','1,234.56元'这样的数据，而我们在分析的时候，是需要对这些数据进行整理成我们需要的格式，比如'100.00','1234.56'。要实现这样的结果，我们需要结合正则表达式对数据进行提取。
针对**datauser**中的数据，我的解决思路是这样的：首先结合循环和正则表达式将数据提取出来，之后将提取出的标准数据赋值给原始的变量。

以下是解决代码：读取数据是为了消除第一行的变量名称，也可以在原始数据中直接丢弃第一行的变量名进行运算。

    datauser = pd.read_csv('userinfo.csv',encoding='gbk')
    name1 = ['严重逾期','成功借款','申请借款','还清笔数','逾期次数']
    for name in name1:
        demo = []
        for item in datauser[name]:
            demo.append(re.findall('\d+',item)[0])
        datauser[name] = demo
	
    name2=['信用额度','借款总额','待还本息','逾期金额']
    for name in name2:
        demo1 = []
        for item in datauser[name]:
            demo1.append(re.findall('\d*\D?\d+\D?\d{2}',item))
        demo2 = []
        num = len(demo1)
        for i in range(num) :
            demo2.append(demo1[i][0].replace(',',''))
         datauser[name] = demo2

    databid = pd.read_csv('databid.csv',encoding='gbk')
    data = pd.merge(databid,datauser,on='loanId') #合并数据集
    data.to_csv('AllData.csv',header=False)  #输出到csv文件

上面的代码是普通的语法，需要循环解决，我们可以进一步使用apply方法，类似于R语言中的apply函数，可以直接对行列进行函数运算，默认是行运算(`axis=0`),效果要好。以下是代码：

    datauser = pd.read_csv('userinfo.csv',encoding='gbk')
    def num(x) : return(re.findall('\d+',x)[0])
    name1 = ['严重逾期','成功借款','申请借款','还清笔数','逾期次数']
    for name in name1:
        datauser[name] = datauser[name].apply(num)

    name2=['信用额度','借款总额','待还本息','逾期金额']
    def numcom(x):
       a = re.findall('\d*\D?\d+\D?\d{2}',x)
       return(a[0].replace(',',''))
    for name in name2:
        datauser[name]=datauser[name].apply(numcom)

在Excel中处理这样的数据问题的解决办法可以参考另一篇文章：[Excel中处理数据格式](http://dengkui.space/2016/03/15/Excel-problem.html)



## 参考资料

【1】 各类Python爬虫帖子

【2】 Python for Data Analysis

【3】[Python Requests快速入门](http://blog.csdn.net/iloveyin/article/details/21444613)