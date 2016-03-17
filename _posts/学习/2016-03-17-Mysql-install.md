---
layout: post
title:  Mysql安装及学习
category: 学习
tags:  软件安装        
keywords: Mysql 
description: Mysql的安装过程
---
参加比赛，数据量比较大，直接用R/Python处理数据不是很好，还是安一个数据库软件比较好，个人使用，安装一个Mysql就可以了，毕竟是个人使用，实在没必要安那些巨大的数据库软件，而且Mysql的功能也还不错。

安装使用Mysql遇到了一系列的坑！幸亏有万能的搜索，顺利带我不断填上一个一个坑，仅以此贴记录我所遇到的坑，愿后人观之，有所帮助。

* **安装版本坑！**初次安装Mysql，直接给我装C盘了，都不让改的，太霸道了，直接卸了，后来到官网下载了可以选择安装路径的版本，以下是安装简要步骤。

首先到官网下载[Mysql软件](http://www.mysql.com/downloads/)，官网提供了商业版和社区版，我们选择社区版，也就是最下面的那个：**MySQL Community Edition (GPL)**。点击打开之后，有一系列的下载选择,我们选择**MySQL on Windows (Installer & Tools)**，这个版本可以直接安装，不需要解压缩。点击打开后，我们选择**MySQL Installer**，我们看到有两个版本，一个是Web版，一个是普通版，随便点击一个下载即可。

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/Mysql360%E6%88%AA%E5%9B%BE20160317141415155.jpg-wx"  ></div>

下载之后，双击打开软件安装即可，如果不更改安装目录，默认的安装目录是在C盘。详细的安装过程可以参考这篇[文章](http://jingyan.baidu.com/article/e6c8503c06f9cee54e1a187c.html)。

安装完毕后，在开始菜单栏中找到Mysql，就表示安装成功了，点击mysql serve 5.7下任何一个都可以打开Mysql界面。初始界面要求你输入密码，输入自己的密码即可。如果输入密码后，软件闪退，查看一下Mysql服务是否打开。

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/Mysqlmysql2.jpg-wx" ></div>

我们通过cmd来启动/关闭Mysql服务，打开cmd（win+R快捷键，输入cmd回车即可），在cmd中输入 `net start mysql`，来启动Mysql。其他功能实现请查看参考资料1。我在输入`net start mysql`后，cmd提示服务名无效。后来通过查看服务名，得知mysql的服务名为MYSQL57，因此，在启动时，输入命令为`net start mysql57` 就可以正常启动了，关闭服务器的方法也是类似。如何查找服务名请查看参考资料2。

2.导入数据坑！安装完软件后，我们需要把csv格式的数据导入到数据库中，以便之后的操作，在导入数据过程中又遇到了坑！以下是正确的导入代码：Mysql命令结尾要填;

    show databases; #显示已有的数据库
    create database tianchi; #创建一个数据库tianchi
    use tianchi;  #使用数据库tianchi
    create table action (
    user_id varchar(36),
    song_id varchar(36),
    gmt_create varchar(15),
    action_type varchar(3),
    Ds varchar(10)
    ); #创建用户行为表
    load data infile 'D:\\project\\tianchi\\data\\mars_tianchi_user_actions.csv' 
    into table `action` 
    fields terminated by ',' optionally enclosed by '"' escaped by '"' 
    lines terminated by '\n'; #导入csv数据
    desc action; #显示表信息
    # alter table action change Ds Ds varchar(10); 修改表中字段属性，仅作为例子
    select * from action limit 5; 查看前5行

在导入数据时，软件提示：`1290- The MySQL server is running with the --secure-file-priv option so it cannot execute this statement`,这时我们需要打开Data文件（安装软件时设定的Data位置）中的my.ini文件（如果是默认安装，则在C:\ProgramData\MySQL\MySQL Server 5.6\my.ini，注意ProgramData为隐藏文件夹），复制一份作为备份，然后将里面的类似的一行 secure-file-priv="C:/ProgramData/MySQL/MySQL Server 5.6/Uploads" 删除。关闭服务器，再启动mysql服务，就可以了。

<div align="center"><img src="http://7xo51k.com1.z0.glb.clouddn.com/Mysqlmysql3.jpg-wx" ></div>

这只是初步的坑，后续的坑我会继续填上去的。

## 参考资料

【1】 [MySQL Command Line Client](http://blog.sina.com.cn/s/blog_92d6b8be0100wee2.html)

【2】 [Win7系统中的服务查看、关闭、启动操作图文教程](http://www.jb51.net/os/windows/305941.html)

【3】 [mysql 显示所有的数据库](http://blog.hehehehehe.cn/a/1854.htm)

【4】 [MySQL如何导入csv格式数据文件解决方案](http://www.jb51.net/article/32074.htm)