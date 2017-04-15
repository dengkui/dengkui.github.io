---
layout: post
title: 地铁遐想 
category: 学习
tags:  数据处理	        
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

### 3. 对某网站json数据进行整理汇总(仍有部分不规范json数据需单独处理）

实现语言： Python

思路：解析json数据，提取数据，整理成dataframe格式，合并数据

    import json
    import pandas
    import pandas as pd 
    import numpy as np
    import re

    demo = pd.read_table('dump.16-08-15.1',encoding='utf-8',sep='\t',header=None)#读入文件
    f = open('json1.txt','w',encoding='utf-8')#创建json写入文件
    lines = np.shape(demo)[0]#行数
    for i in range(lines):#写入json
        jsdata = demo.ix[i,1]
        if type(jsdata) == float:
            pass
        else:
            f.write(jsdata)

    f.close() #关闭文件
    #f.truncate()清空文件内容
    f = open('json1.txt','r+',encoding='utf-8')
    jsdata = f.readlines()#read json
    data = jsdata[0].split(']}')#拆分json字符串

    #judge NaN
    def JN(data):
        dataN = data.isnull()
        if (dataN.sum()).sum() == 0:
            return('False') #not included
        else:
            return('True')

    def MesInfo(data,i):#提取Mesinfo
        Messages = pd.DataFrame(data['messages'])
        numid0 = len(Messages)
        id = Messages['msgid'].unique()
        numid1 = len(id)
        MessagesInfo = []
        if numid1 == numid0:#没有重复msgid
            for j in range(numid1):
                messagesinfo = pd.DataFrame(Messages.ix[j,'bodydata'],index =     [str(i)])
                messagesinfo['time'+str(Messages.ix[j,'msgid'])] = Messages.ix   [j,'time']
                MessagesInfo.append(messagesinfo)
                MessagesInfo = pd.concat(MessagesInfo,axis = 1)#messageinfo
       else:#有重复id
            for k in  id:
                mesdata = Messages[Messages['msgid']==k]#按照原始数据id顺序
                MessagesInfo1 = []#单个msgid信息
                pos= mesdata.index
                if len(mesdata) == 1:
                    messagesinfo=pd.DataFrame(mesdata.ix[pos[0],'bodydata'],index = [str(i)])
                    messagesinfo['time'+str(k)] = mesdata.ix[pos[0],'time']
                    MessagesInfo.append(messagesinfo)
                else:
                    nump = range(len(mesdata))
                    for l in list(nump) :#对body依次提取
                        messagesinfo1 = pd.DataFrame(mesdata.ix[pos [l],'bodydata'],index = [str(i)])
                        MessagesInfo1.append(messagesinfo1)
                    MessagesInfo1 = pd.concat(MessagesInfo1,axis = 0)#messageinf
                    if JN(MessagesInfo1) == 'False':#不包含NaN值
                        messagesinfo = MessagesInfo1.ix[0]#提取第一行值
                        messagesinfo = pd.DataFrame(dict(messagesinfo),index=[str(i)])
                        messagesinfo['time'+str(k)] = mesdata.ix[pos[0],'time']
                        MessagesInfo.append(messagesinfo)
                    else:
                        dataN = MessagesInfo1.isnull()
                        a = dataN.sum()
                        a[a>0] = np.nan#
                        messagesinfo = pd.DataFrame(dict(a),index=[str(i)])
                        messagesinfo['time'+str(k)] = mesdata.ix[pos[0],'time'] 
                        MessagesInfo.append(messagesinfo)
            MessagesInfo = pd.concat(MessagesInfo,axis = 1)
        mesT=MessagesInfo.T
        mesT['index'] = mesT.index
        mesT = mesT.drop_duplicates(['index'])
        mesT = mesT.drop('index',axis=1)
        MessagesInfo = mesT.T
        return(MessagesInfo)

    #整理json数据格式
    num = len(data)-1
    HeadInfo = []
    DeviceInfo = []
    MessagesInfo = []
    for i in range(num):
        demotxt = data[i]+']}'#补全json
        try:
            jsinfo = json.loads(demotxt)
        except json.decoder.JSONDecodeError:
            re_item = re.compile(r'(?<=[{,])\w+[^,]_?\w+(?=:)')#json中键没有用双引号
            jsinfo = re_item.sub("\"\g<0>\"", demotxt)#加上双引号
            jsinfo = json.loads(jsinfo)
        headinfo = pd.DataFrame(jsinfo['headInfo'],index=[str(i)])
        deviceinfo = pd.DataFrame(jsinfo['deviceInfo'],index=[str(i)])
        Messages = pd.DataFrame(jsinfo['messages'])
        if len(Messages) == 0:#如果message信息为空
            messagesInfo = pd.DataFrame({"video_id":np.nan},index=[str(i)])
        else:
            messagesInfo = MesInfo(jsinfo,i)
        MessagesInfo.append(messagesInfo)
        HeadInfo.append(headinfo)
        DeviceInfo.append(deviceinfo)

    HeadInfo = pd.concat(HeadInfo)#headinfo
    DeviceInfo = pd.concat(DeviceInfo)#deviceinfo
    MessagesInfo = pd.concat(MessagesInfo)#messageinfo

    #合并信息
    InfoData = pd.merge(HeadInfo,DeviceInfo,left_index=True,right_index=True,sort=False,suffixes=('_head','_devi'))
    InfoData = pd.merge(InfoData,MessagesInfo,left_index=True,right_index=True,sort=False,suffixes=('_devi','_mes'))
    InfoData = InfoData.fillna('NaN')#填充缺失值
    InfoData.to_csv('InfoData.csv',header=True)

