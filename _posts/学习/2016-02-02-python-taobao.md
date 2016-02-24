---
layout: post
title:  抓取淘宝商品信息（Python版）
category: 学习
tags:  数据抓取        
keywords: Python  抓取淘宝
description: 利用Python抓取淘宝网页数据
---
之前和同学一起学习抓取淘宝数据时，折腾了很长时间，感觉很麻烦，自己摸索了一段时间，试着抓取淘宝搜索页面的商品信息，发现，采用之前的BeautifulSoup来解析网页不是很方便，淘宝的数据都在标签`script`下，而且是以json格式储存的，需要把json数据给单独提取出来(采用正则表达式），然后再采用json库对json数据转换为python格式（字典），之后采用Pandas库中的DataFrame函数就可以轻松地把字典数据转换为数据框形式。在抓取数据时，希望抓取多页的数据，但翻看许久，也没有找到下一页的标签和url，只能通过观察不同页面之间的url规律了，发现从第二页开始url只有结尾的数字有变化，而且数字变化呈等差为48的等差序列，所以利用此规律就可以很方便地构建不同页面的url了。

以下是Python代码，写的比较水:

    #引进相关的库
    import requests
    import json
    import re
    import pandas as pd
    import numpy as np
    from pandas import DataFrame,Series

    price_table=DataFrame(np.array(['月销量','价格','名称','重要度','评论数']).reshape(1,5),columns=['month_sales','price','title','importantKey','cmt_count'])
    url="https://s.taobao.com/search?initiative_id=tbindexz_20160224&ie=utf8&spm=a21bo.7724922.8452-taobao-item.2&sourceId=tb.index&search_type=item&ssid=s5-e&commend=all&imgfile=&q=%E6%89%8B%E6%9C%BA&suggest=history_1&_input_charset=utf-8&wq=s&suggest_query=s&source=suggest"
    url_base="https://s.taobao.com/search?initiative_id=tbindexz_20160224&ie=utf8&spm=a21bo.7724922.8452-taobao-item.2&sourceId=tb.index&search_type=item&ssid=s5-e&commend=all&imgfile=&q=%E6%89%8B%E6%9C%BA&suggest=history_1&_input_charset=utf-8&wq=&suggest_query=&source=suggest&s="
    i=1
    while i<5 :
        resp=requests.get(url) #获取页面
        html=resp.text #页面文字
        regex=r'g_page_config =(.+)' #商品包含区域标志，
        items=re.findall(regex,html) #正则查找 结果为list
        items=items.pop().strip() #items为字符串
        items=items[0:-1] #删除最后一个字符
        items=json.loads(items) #json格式
        data=DataFrame(items['mods']['grid']['data']['spus'],columns=['month_sales','price','title','importantKey','cmt_count'])
        price_table=pd.concat([price_table,data])
        url=url_base+str(i*48)
        i += 1

    file_name='淘宝数据.csv'
    price_table.to_csv(file_name,header=False) #将整理后的数据写入csv格式文档

抓取后的数据表现形式为：

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/taobao%E6%B7%98%E5%AE%9D%E6%95%B0%E6%8D%AE.jpg-wx"  ></div>

这次抓取淘宝数据，体会到了正则表达式的强大，真是学的东西太少了。

## 参考资料

【1】[如何去淘宝网爬取某些信息?](http://zhidao.baidu.com/link?url=vQdc4KaG1QHPEc8fw607t0idp_PIMijFd3az-wuzaX5eWtJGjvkULjeJvMqKq1YNz9ynDuH1TxOvjPmuBN_5DF_1LkwnR8Hg1HvyxmGIvam)

【2】Python for Data Analysis