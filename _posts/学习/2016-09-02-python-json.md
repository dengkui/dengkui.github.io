---
layout: post
title:  Python Json标准化处理
category: 学习
tags:  自动便利	        
keywords: json  Python
description: 利用Python处理json字符串
---

json作为一类重要的数据形式，必须转换为常见的数据形式才能用于分析，这篇文档介绍了如何利用Python对json数据进行转换，并对常见的json解析问题进行了介绍。

## JSON简介

JSON 指的是 JavaScript 对象表示法（JavaScript Object Notation），是一种文本格式数据，常见于网页数据分析。

Json数据有其独特的一套表达形式：一个Json对象由花括号括起，数据格式为名称：值，数据之间用逗号分开，名称和值用冒号分开，其中值可以是单个json对象，也可以是json对象的列表，如果是列表，则用中括号括起来。

一个json示例：(引自[百度百科](http://baike.baidu.com/link?url=kO4P0IJm-bDYX0VHci6qtEmqwHanBh8Hh7jlGDqxIF25-AhfVUqyCM0gpwc_mQzQ6CFd3pKzX9T5DR3fR--bEq)
）

    {
    "programmers": [{
        "firstName": "Brett",
        "lastName": "McLaughlin",
        "email": "aaaa"
    }, {
        "firstName": "Jason",
        "lastName": "Hunter",
        "email": "bbbb"
    }, {
        "firstName": "Elliotte",
        "lastName": "Harold",
        "email": "cccc"
    }],
    "authors": [{
        "firstName": "Isaac",
        "lastName": "Asimov",
        "genre": "sciencefiction"
    }, {
        "firstName": "Tad",
        "lastName": "Williams",
        "genre": "fantasy"
    }, {
        "firstName": "Frank",
        "lastName": "Peretti",
        "genre": "christianfiction"
    }],
    "musicians": [{
        "firstName": "Eric",
        "lastName": "Clapton",
        "instrument": "guitar"
    }, {
        "firstName": "Sergei",
        "lastName": "Rachmaninoff",
        "instrument": "piano"
    }]
    }


## JSON数据处理

Json数据同我们常见的行列数据格式不同,但我们可以通过两步实现json字符串到数据框的转换：

1. 解析json字符串为json对象。
   
        import json
        json.loads(json字符串）

2. 将json对象转换为数据框形式。

     pandas库中的DataFrame可以对json对象进行转换，其中json对象名称作为列名。

下面对上文数据中的authors信息进行提取，将其转换为数据框形式。

    import pandas as pd 
    import json
    demo = '{"programmers": [{"firstName": "Brett","lastName": "McLaughlin","email": "aaaa"}, { "firstName": "Jason","lastName": "Hunter","email": "bbbb"}, {"firstName":"Elliotte","lastName": "Harold","email": "cccc"}],"authors": [{"firstName": "Isaac","lastName": "Asimov","genre": "sciencefiction"}, {"firstName": "Tad","lastName": "Williams","genre": "fantasy"}, {"firstName": "Frank","lastName": "Peretti","genre": "christianfiction"}],"musicians": [{"firstName": "Eric", "lastName": "Clapton","instrument": "guitar"}, {"firstName": "Sergei","lastName": "Rachmaninoff","instrument": "piano"}]}'
    demojs = json.loads(demo)
    Aurdata= pd.DataFrame(demojs['authors'])

经过转换后的数据就成了这种形式（仅仅包含author信息）：

    Aurdata
        firstName        genre     lastName
    0     Isaac    sciencefiction    Asimov
    1       Tad           fantasy  Williams
    2     Frank  christianfiction   Peretti

其他信息的提取转换类似。

## JSON格式规范化

Json数据处理中经常出现的问题是json原始数据的不规范问题，导致json解析出现问题，

比如利用json.loads()函数解析json字符串的时候常常抛出这样的异常：

    JSONDecodeError: Expecting ',' delimiter
    JSONDecodeError: Expecting ':' delimiter
    JSONDecodeError: Expecting property name enclosed in double quotes

对应的问题可能依次为：

    json字符串某两个数据之间的逗号缺失或缺少}
    json字符串某个名称少了一个后引号
    json字符串某个名称缺少了双引号或者少了前引号

对于有问题的json字符串，我们可以将其复制到[json在线解析网站](http://www.bejson.com/)进行检测，根据检测结果进行相应修改。

除了人工校验，我们可以通过正则表达式对其进行检查修改，以下正则表达式仅作一个示例，具体匹配形式需根据具体情况进行修改。

1. 对于json对象中数据名称缺少双引号的情况，我们可以使用以下的正则表达式进行检测修改。
      
        re_item = re.compile(r'(?<=[{,])\w+[^,]_?\w+(?=:)')#json中名称没有用双引号
        re_item.sub("\"\g<0>\"", demo)#加上双引号

2. 对于json对象中数据名称缺少前引号/后引号，我们可以使用类似的正则表达式进行检测修改

        re_item = re.compile(r'(?<=[{,])\w+[^,]_?\w+"(?=:)')#json中名称没有用前引号
        json.loads(re_item.sub("\"\g<0>", demo))

        re_item = re.compile(r'(?<=[{,])"\w+[^,]_?\w+(?=:)')#json中名称没有用后引号
        json.loads(re_item.sub("\g<0>\"", demo))

## 结语

通过将Json数据转换为DataFrame形式，我们可以进行正常的数据分析。

## 参考资料

【1】[Python正则表达式指南](http://www.cnblogs.com/huxi/archive/2010/07/04/1771073.html)

【2】[Python处理不规范json](http://www.cnblogs.com/cnicefire/archive/2013/02/24/2924170.html)

【3】利用Python做数据分析