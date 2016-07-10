---
layout: post
title: Python 常见数据操作
category: 学习
tags:  数据处理        
keywords: Python
description: Python数据处理记录
---

技不常用则日渐疏矣，此文目的在于记录Python数据处理的常用语句示例，方便后续翻看，Python版本采用3.5。

读写数据

    data = pd.read_csv('D:/Project/201509.csv',encoding='gbk')
    data.to_csv('D:/Project/data/data.csv',header=True,index=False)

数据基本特征：

    data.shape
    data.head()
    data.columns

更改数据列名称

    data=data.rename(columns={'name1':'name2'})

挑选数据行列

    data = data.ix[:,['name1','name2']]
    data_s = data[['X1','X2']]
    
    index = data1['IMSI']
    mask = data2['IMSI'].isin(index)
    data_t = data2[mask]

条件赋值

    data.ix[(x1 == 0) & (x2 == 0),'Y'] = 0

    gender_change[gender_change == 1]=0

数据合并

    data3 = pd.merge(data1,data2,on='x',suffixes=('1','2'),sort= False)#suffixes设定两个数据集中相同变量名称合并后的前缀，sort设定是否排序，默认排序，在合并大数据时设定为false可以提高合并速度

    data3 = pd.concat(data1,data2)#相当于R中的rbind

数据替换

    data['终端品牌'] = data['终端品牌'].replace({'天宇朗通':'天语','诺基亚':'微软','宇龙':'酷派','步步高':'维沃'})

数据行列运算

    data['X0']=data['X1'] - data['X2']

删除行列

    data = data.drop('X1',axis=1)

列联表

    pd.crosstab(data['ifchange'],data['change'],margins=True)


## 参考资料

【1】 利用Python做数据处理

