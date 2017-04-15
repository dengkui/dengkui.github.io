---
layout: post
title:  《Python机器学习实战》记录帖
category: 学习
tags:  理论学习        
keywords: Python 
description: 学习记录
---

这篇文章用以记录在学习《机器学习实战》中遇到的问题。

1.在notepad++中编辑代码，调用函数时出现错误：TabError:inconsistent use of tabs and spaces in indentation 。

    答： 使用tab和空格混乱。代码缩进不一致，将def中的代码缩进统一为空两格。
     
2.自己编写的knn算法代码：demo为列表，dataset训练数据是pandas.DataFrame格式，labels
是列表。以下代码没有考虑频率相同的情况。
   
     def knn(demo,dataset,labels,k):
       dis1=demo-dataset
       dis2=dis1**2
       dis3=dis2.sum(axis=1)
       dis=DataFrame(dis3)
       dis['label']=labels
       dis=dis.rename(columns={0:'dis'})
       dis=dis.sort_index(by='dis')
       disk=dis.head(k)
       disk=disk.icol(1)
       disc=disk.value_counts()
       label=disc.index[0]
       return(label)
    
3.min-max标准化数据函数：
 
    def normalize(arr):
     return((arr-arr.min())/(arr.max()-arr.min()))

4.比较两组列表元素不同个数的占比函数：

    def comlis(list1,list2):
        n=len(list1)
        error=0
        for i in range(n):
            if list1[i] != list2[i] :
                error += 1
        print('the rate of error is : %.2f ' % (error/n)) #%.2f截取两位小数

5.计算香农熵函数：

    from numpy import *
    from pandas import Series,DataFrame
    import pandas as pd
    def shannonEnt(data):
        ncol=(shape(data)[1]-1)
        col=data.columns[ncol]
        label=data[col]
        fre=label.value_counts()/len(label)
        shannonEnt=-sum(fre*np.log2(fre))
        return(shannonEnt)

6.数据分类函数：（原书程序清单3-2）

    def splitdata(data,col,value):
        cdata=data[data[col]==value]
        sdata=cdata.drop(col,axis=1)
        return(sdata)

7.选择最优分类：依据信息熵原理，data是dataframe格式，最后一列是标签

    from numpy import *
    from pandas import Series,DataFrame
    import pandas as pd
    def choosebest(data):
        numf=shape(data)[1]-1
        baseentropy=shannonEnt(data)
        bestinfogain=0;bestf=-1
        for i in range(numf):
            k=data.columns[i]
            uniqueval = set(data[k])
            newen=0
            for value in uniqueval:
                subdata=splitdata(data,k,value)
                prob=shape(subdata)[1]/shape(data)[1]
                newen += prob*shannonEnt(subdata)
           infogain=baseentropy-newen
           if (infogain > bestinfogain):
               bestinfogain=infogain
               bestf=k
       return(bestf)

8.返回数据标签中占比最大的标签：数据格式为DataFrame,数据的最后一列是标签信息

    from numpy import *
    from pandas import Series,DataFrame
    import pandas as pd
     def resclass(data):
        ddim=shape(data)
        ncol=ddim[1]-1
        label=data.icol(ncol)
        sortlab=label.value_counts()
        result=sortlab.index[0]
        return(result)

9.创建决策树的函数代码：用到以上5，6，7，8函数

     def createTree(data,label):
        k=data.columns[-1]
        classlist=data[k]
        if len(classlist.value_counts()) == 1:
            return(list(classlist)[0])
        if shape(data)[1] == 1:
            return(resclass(data))
        bestfeat=choosebest(data)
        pos=data.columns.get_loc(bestfeat)
        bestfeatlabel=label[pos]
        mytree={bestfeatlabel:{}}
        del(label[pos])
        featvalue=set(data[bestfeat])
        for value in featvalue:
            sublabel=label[:]
            mytree[bestfeatlabel][value]=createTree(splitdata(data,bestfeat,value),sublabel)
        return(mytree)

10.计算叶节点数目：mytree为字典格式

     def getNumLeafs(mytree):
        numleaf=0
        value=list(mytree.values())
        seconddict =value[0]
        for key in seconddict.keys():
            if type(seconddict[key]).__name__=='dict':
                numleaf += getNumLeafs(seconddict[key])
            else: numleaf += 1
        return(numleaf)

11.计算树节点数目

    def getNumTree(mytree):
        maxDepth=0
        value=list(mytree.values())
        seconddict=value[0]
        for key in seconddict.keys():
            if type(seconddict[key]).__name__=='dict':
                thisdepth=1+getTreeDepth(seconddict[key])
            else: thisdepth=0
            maxDepth=maxDepth+thisdepth
        return(maxDepth)
    
    def numtree(mytree):
        num=getNumTree(mytree)+1
        return(num)

12.计算树层数：

    def getTreeDepth(mytree):
        maxDepth=0
        value=list(mytree.values())
        seconddict=value[0]
        for key in seconddict.keys():
            if type(seconddict[key]).__name__=='dict':
                thisdepth=1+getTreeDepth(seconddict[key])
            else: thisdepth=1
            if thisdepth>maxDepth:maxDepth=thisdepth
        return(maxDepth)

13：决策树分类函数：严格查询类型不能用type()

    def classify(tree,labels,testvec):
        value=list(tree.values())
        seconddict=value[0]
        labindex=labels.index(list(tree.keys())[0])
        for key in seconddict.keys():
            if testvec[labindex] == key :
                if type(seconddict[key]).__name__ == 'dict':
                    classlabel=classify(seconddict[key],labels,testvec)
                else: classlabel=seconddict[key]
        return(classlabel)
