---
layout: post
title: R/Python 文件操作
category: 学习
tags:  软件学习        
keywords: 
description: 用程序对文件进行自动操作
---

在编程处理某个问题时，不时涉及到对文件夹和文件的操作，在此做一个总结。

涉及到文件夹及文件的操作基本有5种：1.创建 2.修改 3.复制 4.移动 5.删除。以下分别对R语言和Python的操作进行介绍。

## R

dir.create(path)  创建文件夹

dir.exists(path)  文件夹是否存在

file.create(path)  创建文件

file.exists(path)  文件是否存在

file.append(file1,file2) 将文件2内容添加到文件1

file.copy(from,to)  复制文件

file.rename(from,to)  文件重命名

list.files(x)  获取文件夹下所有文件名称

unlink(x,recursive = F) 删除文件夹，recursive默认为F，非空文件夹不删除，当设置为T，删除文件夹及其中的文件

file.remove()  删除文件

示例：

    dir.create('C:/Users/lenovo/Desktop/demo')
    dir.exists('C:/Users/lenovo/Desktop/demo')
    file.create('C:/Users/lenovo/Desktop/demo/demo.txt')
    cat('file demo\n',file='C:/Users/lenovo/Desktop/demo/demo.txt') #将'file demo‘写入demo.txt文件
    file.create('C:/Users/lenovo/Desktop/demo/demo.r')
    file.append('C:/Users/lenovo/Desktop/demo/demo.r','C:/Users/lenovo/Desktop/demo/demo.txt')
    file.copy(from='C:/Users/lenovo/Desktop/demo/demo.txt',to='C:/Users/lenovo/Desktop/demo.txt')
    file.rename(from='C:/Users/lenovo/Desktop/demo/demo.txt',to='C:/Users/lenovo/Desktop/demo/demo2.txt')
    list.files('C:/Users/lenovo/Desktop/demo')
    unlink('C:/Users/lenovo/Desktop/demo',recursive = F) #非空文件夹，不删除
    unlink('C:/Users/lenovo/Desktop/demo',recursive = T)
    file.remove('C:/Users/lenovo/Desktop/demo2.txt')

## Python

Python 中有关文件处理的方法在os模块，需要先导入os模块。

os.getcwd() 获取当前工作目录

os.chdir(path) 更改工作目录

fpath，fname = os.path.split(path) 将路径分解为目录路径和文件名称

os.path.exists(path)  判断路径是否存在
 
os.path.isfile(path)  判断文件是否存在
 
os.path.isdir(path)  判断路径是否是目录
    
os.listdir( "你要判断的路径") 获取某目录中的文件及子目录的列表

os.makedirs(path)   创建文件夹

os.rmdir(path)   删除文件夹

os.remove(filename)   删除文件

os.rename(fileA,fileB) 文件重命名

shutil.copyfile(from,to) 复制文件

shutil.move(src, dst)  移动文件或重命名  

更详细的Python文件操作参见参考资料2

## 参考资料

【1】[Python文件夹与文件的操作](http://scm002.iteye.com/blog/1522448)

【2】[Python中os和shutil模块实用方法集锦](http://www.jb51.net/article/50070.htm)
