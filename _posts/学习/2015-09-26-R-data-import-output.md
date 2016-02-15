---
layout: post
title:  R 数据读入及导出
category: 学习
tags:  便利化程序        
keywords: 数据处理
description: R语言读取数据及导出
---

由于这几天经常要把数据在不同软件之间进行读入导出，遂作一总结。

一般常用的数据文件有TXT/ Excel/CSV格式，而SPSS,SAS数据也可以通过其自身软件转换为CSV格式，之后读入CSV格式文件就可以啦。
   
对于Excel数据，虽然R中有直接读入Execl数据的包，比如：RODBC包，但由于操作麻烦，且容易出错，因此不建议直接读入Excel文件，而是将其转换为CSV文件。CSV文件（Comma-Separated Values），也就是逗号分隔符文件，这种文件以纯文本形式储存文本和数据，是一种简单的，通用的数据文件，而且很容易用其他软件读取。
   
Txt文件可以通过read.table（）函数读入，其常用参数如下：

`read.table(file, header = FALSE, sep = "",nrows = -1, skip = 0, fill = !blank.lines.skip，encoding = "unknown")`

file是要读入的文件，是一个带分隔符的ASCII文本文件。可以是txt文件，也可以是其他类型文件，取决于sep参数设定。文件读入路径可以采用绝对路径，也可以采用相对路径，注意R中路径采用正斜杠/而不是通常的反斜杠\，虽然这两种方法都可以正确读入，但是实在都太麻烦了，谁对敲路径不烦呢？我们可以采用file.choose()，这样R/RStudio会弹出一个对话框，这样就可以使用鼠标来选取文件，实在是方便多了，这个方式在R中是通用的，只要是读入数据，都可以这么试试，比如之前的读入Shapefile格式文件。
   

header用来设置首行是否为变量名，是逻辑值（F or T），默认为F。如果文件首行是数据的变量名，应该设置为T，否则，读入数据后第一行为变量名，第二行开始才是变量值。
   
sep表示分隔符类型，默认为””,表示为一个或多个空格，制表符，换行符或回车符。制表符为”\t”，也就是键盘上的tab,换行符为“\n”,回车符为”\n”，读入csv格式文件时，分隔符为“，”。
   
nrows表示要读入前几行，-1表示所有。
   
skip表示跳过前几行，默认为0，也就是不跳过。
   
fill设置文件中如果有缺失值，是否自动补充为NA，默认不补充。如果文件中有缺失值，会导致读入错误，提示信息为在第几行只有几个元素（变量），这时我们可以设置fill=T。
   
fileEncoding设置读入的文件编码格式，ANSI编码可以直接读入，不用设置这个参数，如果是UTF-8编码，则要指定为encoding=”UTF-8”,或者将文件转换为ANSI编码，否则，读入的数据显示乱码。
   
下面读入一个UTF-8格式的txt文件，首行是变量名。

    y <- read.table(file.choose(),header=T,fileEncoding ="UTF-8")
    head(y)
       Acc       code    gender    grade  
    1  A1021    6157082     女       2013      
    2  A1148    2011843     男       2014  
    3  A1575    4018209     男       2010  
    4  A2611    5881762     男       2011  
    5  A2625    3112091     男       2012  
    6  A2906    5127798     男       2012  

对于CSV文件，可以使用read.table()，read.csv()函数。推荐使用read.csv()，因为它已经直接设定了文件的分隔符，而如果用read.table()函数还需要设定sep=”,”，才可以读取文件。读取后的数据在R中为data.frame格式。
  
read.csv()是read.table()的一个变形，它设定sep=”,”，可以直接读入csv格式数据。其他类似的还有read.csv2()，read.delim(),read.delim2()，都可以直接读入相应格式的数据。
   
read.csv()函数参数设置如下：
   `read.csv(file, header = TRUE, sep = ",", quote = "\"",dec = ".", fill = TRUE, ...)`

其中，dec表示小数点的字符。
   
既然可以读入数据，那么一定可以将数据导出，R语言可以把数据导出为txt，csv及其他格式。
   
导出为txt格式的函数为write.table()，对应着读入函数read.table()，参数设置也有很大的相同。
   
write.table()函数参数设置；
   `write.table(x,file=””,…)`
其中x为要导出的数据名称，file为导出后数据文件名称，如果不写导出路径，默认导出到工作空间，使用getwd()查询工作空间。

    x <- head(y)
    write.table(x,file="data.txt")#将x导出到data.txt文件中

 同样的，我们也可以导出为csv格式文件，采用write.csv()函数，对应read.csv()函数。
 write.csv()函数参数设置： `write.csv(x,file=””,…)`
    
    write.csv(x,file="data.csv")#将x导出到data.csv文件中
更多详细请?write,?write.table,？write.csv
  
上面这些都是把R中数据导出到外部文件，但如果我们想直接把数据从R中复制到Excel中时，就可以使用命令：
`write.table(data,"clipboard",sep="\t",col.names=NA）`
然后在Excel中使用粘贴命令，就可以直接把R中x数据复制到Excel中，是不是很方便！
    
    write.table(x,"clipboard",sep = "\t",col.names = NA)

更多内容请学习参考资料。

## 参考资料

[1] [R学习笔记(4): 使用外部数据](http://www.cnblogs.com/holbrook/archive/2013/05/16/3081331.html)

[2] [R Data Import/Export](https://cran.r-project.org/doc/manuals/R-data.html)

[3] [read.table 的变化样式](http://www.biosino.org/R/R-doc/R-data_cn/Variations-on-read_002etable.html)	

[4] The R Book
