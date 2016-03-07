---
layout: post
title:  抓取人人贷散标及借款人数据（Python版）
category: 学习
tags:  数据抓取        
keywords: Python  抓取人人贷
description: 利用Python抓取人人贷散标及借款人数据
---
应同学之求，试着抓取了人人贷网站上[散标投资数据](http://www.we.com/fund/fundInfoAction!tolist.action)和借贷人的个人信息数据。

虽然之前折腾了两个网站的抓取，但这个网站的抓取真叫人**抓狂**，主要遇到的是两个问题

1. 散标投资数据网站不同页面的源代码相同，虽然不同页面的显示不同！（**真不可思议-_-|||**）
2. 借贷人个人信息需要登录之后才能抓取

首先解决第一个问题：既然不同页面的数据显示是不相同的，那么数据来源肯定不同，需要找到真正的数据来源网址。 采用Chrome的F12-Network-XHR来查看请求，在请求中找到数据并查找来源，通过不断的点击不同页面，发现不同页面数据的真正来源。之后按照一般思路就可以解决了，还好数据是Json格式的，方便处理。

对于第二个问题：一般的解决思路是模拟浏览器登录网站，登录成功后再抓取网页并解析。这个看似简单的思路却折腾时间最长，主要原因还是对浏览器登录访问网站的原理不清楚。我们可以通过提交用户名和密码来登录网站，登录之后，通过向服务器发送带Cookie的请求，就可以实现登录了。我这里使用一个简单的方法，算是偷懒了。因为用Python生成的Cookies没有成功登录，所以我就抓取了网站登录成功后的Cookie来构建headers，之后利用这个headers发送post请求。

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
    table=DataFrame(np.array(['allowAccess', 'amount', 'amountPerShare', 'beginBidTime', 'borrowerId',
       'borrowerLevel', 'currentIsRepaid', 'displayLoanType', 'finishedRatio',
       'forbidComment', 'interest', 'interestPerShare', 'leftMonths', 'loanId',
       'loanType', 'months', 'nickName', 'oldLoan', 'openTime', 'overDued',
       'picture', 'principal', 'productId', 'readyTime', 'repaidByGuarantor',
       'startTime', 'status', 'surplusAmount', 'title', 'utmSource']).reshape(1,30),columns=['allowAccess', 'amount', 'amountPerShare', 'beginBidTime', 'borrowerId',
       'borrowerLevel', 'currentIsRepaid', 'displayLoanType', 'finishedRatio',
       'forbidComment', 'interest', 'interestPerShare', 'leftMonths', 'loanId',
       'loanType', 'months', 'nickName', 'oldLoan', 'openTime', 'overDued',
       'picture', 'principal', 'productId', 'readyTime', 'repaidByGuarantor',
       'startTime', 'status', 'surplusAmount', 'title', 'utmSource'])#网页源码获取
    i=1
    while i<= 51 : #51是最后一页
    url = "http://www.we.com/lend/loanList!json.action?pageIndex=%s&" % i #数据的真正来源
    resp=requests.get(url,headers=headers) #获取页面
    html=resp.text #页面文字
    data_dic = json.loads(html)
    data=DataFrame(data_dic['data']['loans'])
    table=pd.concat([table,data])
    i += 1
	#保存文件
    file_name='人人贷.csv'
    table.to_csv(file_name,header=False) #将整理后的数据写入csv格式文档

    #抓取借贷人信息
    table=DataFrame(np.array(['严重逾期', '还清笔数', '信用评分', '信用额度', '年龄', '车贷', '房贷', '逾期次数', '公司规模', '岗位职位', '待还本息', '工作时间', '车产', '逾期金额', '学历', '成功借款', '公司行业', '借款总额', '房产', '婚姻', '收入', '申请借款', '工作城市', '昵称']).reshape(1,24),columns=['严重逾期', '还清笔数', '信用评分', '信用额度', '年龄', '车贷', '房贷', '逾期次数', '公司规模', '岗位职位', '待还本息', '工作时间', '车产', '逾期金额', '学历', '成功借款', '公司行业', '借款总额', '房产', '婚姻', '收入', '申请借款', '工作城市', '昵称'])
    s=requests.session()
    rrd=pd.read_csv('loadId.csv')
    loanId=rrd.ix[:,'loanId']
    headers['Cookie']='rrdLoginCartoon=rrdLoginCartoon; newuser=new; Hm_lvt_71ce3105a964d0c3748e04584e5af0b9=1457060777,1457078309,1457157739,1457306829; Hm_lpvt_71ce3105a964d0c3748e04584e5af0b9=1457307045; Hm_lvt_2547238860b5ae69d69cae60a725236c=1457060777,1457078309,1457157739,1457306829; Hm_lpvt_2547238860b5ae69d69cae60a725236c=1457307045; Hm_lvt_16f9bb97b83369e62ee1386631124bb1=1457227320,1457227562,1457231071,1457305815; Hm_lpvt_16f9bb97b83369e62ee1386631124bb1=1457308752; JSESSIONID=14343A739C683A0602A5171155C3132B1E2B35EBA7FDCBEE9B99BDC5C89F87F8; jforumUserInfo=iTl5UMRb2UCOWMLi21HHnzCyIgL4BGPO%0A; IS_MOBLIE_IDPASS=true-false; activeTimestamp=5009119; renrendaiUsername=""'#通过获取网页请求数据可得

    i = 0
    while i <= len(loanId):
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
        table=pd.concat([table,data])
        i += 1

    table.to_csv('userinfo.csv',header=False)

到这儿基本的数据抓取就完成了，剩下的任务就是对数据进行整理清洗。

## 参考资料

【1】 各类Python爬虫帖子

【2】 Python for Data Analysis