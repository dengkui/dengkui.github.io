---
layout: post
title:  R  查看函数源码
category: 学习
tags:  软件学习 
keywords: R  function source code  函数源码
description: 查看R语言函数源码
---

本篇文章主要介绍如何查看R语言中函数源代码的方法。

文章结构内容主要参考参考资料1。

R语言中的函数按照编写语言可以分为R语言和其他语言，比如C，Fortran等，按照是否可见，又可以分为可见函数和不可见函数，按照是否是泛函函数，又可以分为泛函函数和其它函数。函数的类型不同，源码查看方式也不同。

## 普通函数

对于输入名称就可以查看源码的函数，我称之为普通函数。这类函数在所有函数中占了大部分，是采用R语言编写。

示例: 

    var
     function (x, y = NULL, na.rm = FALSE, use) 
    {
    /* many lines omitted */
    .Call(C_cov, x, y, na.method, FALSE)
    }
    <bytecode: 0x1422fee8>
    <environment: namespace:stats>

## 隐藏函数(non-visible functions)

部分函数输入名称的返回结果为`Error: object not found`，这部分函数为隐藏函数。我们可以通过`getAnyWhere()`来查看源码。这类函数在S3的泛型函数的方法中会标*,表示为隐藏函数。

`getAnyWhere()`不能直接查看S3，S4类泛型函数的源码。

示例：
   
    > methods(plot)
    [1] plot.acf*          
    [2] plot.data.frame*   
    [3] plot.decomposed.ts*
    [4] plot.default       
    /* many lines omitted */

    > plot.acf
    Error: object 'plot.acf' not found

    > getAnywhere(plot.acf)
    A single object matching ‘plot.acf’ was found
    It was found in the following places
    registered S3 method for plot from namespace stats
    namespace:stats
    with value

    function (x, ci = 0.95, type = "h", xlab = "Lag", ylab = NULL, 
    ylim = NULL, main = NULL, ci.col = "blue", ci.type = c("white", 
        "ma"), max.mfrow = 6, ask = Npgs > 1 && 
    /* many lines omitted */
    }
    <bytecode: 0x13d74be0>
    <environment: namespace:stats>

## S3

部分函数输入名称后提示UseMethod，但没有源码，说明函数是S3泛型函数。

对于这类函数，我们先通过`methods()`查看函数的具体方法，之后调用具体方法，在R语言中，该类函数的运行，是通过方法派发(method dispatch.)实现的。

示例：

    > mean
    function (x, ...) 
    UseMethod("mean")
    <bytecode: 0x0ff878c0>
    <environment: namespace:base>

    > methods(mean)
    [1] mean.Date     mean.default 
    [3] mean.difftime mean.POSIXct 
    [5] mean.POSIXlt 
    see '?methods' for accessing help and source code

    > mean.default
    function (x, trim = 0, na.rm = FALSE, ...) 
    {
    /* many lines omitted */
    .Internal(mean(x))
    }
    <bytecode: 0x0ff8d070>
    <environment: namespace:base>

我们也可以通过`getS3method(function, class)`方式来获得S3泛型函数的源码,返回结果同直接调用泛型函数具体方法一致。

    > getS3method('mean', 'default')
        function (x, trim = 0, na.rm = FALSE, ...) 
    {
    /* many lines omitted */
    .Internal(mean(x))
    }
    <bytecode: 0x0ff8d070>
    <environment: namespace:base>



## S4

部分函数输入名称后提示standardGeneric，但没有源码，说明是S4泛型函数。

对于该类函数，我们先通过`showMethods()`获得函数的Signatures,之后通过`getMethod()`函数查看源码。

我们也可以通过`findMethods()`函数直接查看,该函数会返回方法的所有信息，可以通过指定`classes`参数获得需要的信息。在R之前的版本中，此函数为`getMethods()`。

示例：

    > nobs
    standardGeneric for "nobs" defined from package "stats"

    function (object, ...) 
    standardGeneric("nobs")
    <environment: 0x13d8be70>
    Methods may be defined for arguments: object
    Use  showMethods("nobs")  for currently available ones.

    > showMethods('nobs')
    Function: nobs (package stats)
    object="ANY"
    object="mle"

    > getMethod('nobs','mle')
    Method Definition:

    function (object, ...) 
    if ("nobs" %in% slotNames(object)) object@nobs else NA_integer_
    <bytecode: 0x13e44808>
    <environment: namespace:stats4>

    Signatures:
            object
    target  "mle" 
    defined "mle" 

    > findMethods('nobs')
    An object of class  “listOfMethods” 
    $ANY
    function (object, ...) 
    UseMethod("nobs")
    /* many lines omitted */

    $mle
    function (object, ...) 
    if ("nobs" %in% slotNames(object)) object@nobs else NA_integer_
    <bytecode: 0x13e44808>
    <environment: namespace:stats4>
    /* many lines omitted */
    Methods may be defined for arguments: object

## 编译代码(Compiled Code)

如果我们在查看R语言代码的过程中，看到了诸如 .C(),.Call(),.Fortran(),.External(),.Internal()和.Primitive()，说明当前函数在调用其他语言编写的源码，如果要获得这些源码，我们需要去相应的包压缩文件中去寻找对应的源码，也可以通过一些函数来快速调取源码。

对于.Internal()和.Primitive()，我们可以采用`pryr`包中的`show_c_source()`函数去查看源码，函数会返回搜索结果。

示例：

    library(pryr)    
    show_c_source(.Internal(mean(x)))
    show_c_source(.Primitive(sum(x)))

对于.Call()，我们可以在包压缩文件中查找相应的C代码文件。

示例：

    > rnorm
    function (n, mean = 0, sd = 1) 
    .Call(C_rnorm, n, mean, sd)
    <bytecode: 0x13ae16a8>
    <environment: namespace:stats>

提示rnorm调用了C代码，此函数属于stats包，属于R的基础包，我们可以在R的压缩包中src文件夹中去搜寻rnorm.c文件,即可查到源码。

一般而言，对于contributed packages，编译源代码可以从`PackageName/src/`文件夹中找到，对于标准包，编译代码一般在`$R_HOME/src/`，其中`$R_HOME/src/main/` 包含了大部分代码。

标准包的具体查看方法：

1. 在`$R HOME/src/main/names.c`中找到该方法
    
        {"rnorm",	do_random2,	8,	11,	3,	{PP_FUNCALL, PREC_FN,	0}},

2. 根据提示，在`random.c`文件中查找random2函数

        /* many lines omitted */
        /* "do_random2" - random sampling from 2 parameter families. */
        /* many lines omitted */        
        /* See switch below for distributions. */
	    RAND2(8, rnorm);
        /* many lines omitted */
3. 根据提示在`rnorm.c`文件可以找到该方法源码。

## 小结

查看源代码可以帮助我们更好地了解函数，从而为我们所用，本篇文章介绍了不同类型函数的不同查看方法，对于编译代码部分，由于个人水平所限，未能更为详细。

## 参考资料

【1】[R Code Source第43页](https://cran.r-project.org/doc/Rnews/Rnews_2006-4.pdf)

【2】[获取 R 函数或方法源代码](http://bzrlc.tech/r-basics/r-view-source-code/)

【3】[R 学习笔记: S4 编程基础](https://zhuanlan.zhihu.com/p/21396190)

【4】[Advanced R](http://adv-r.had.co.nz/)

【5】[How do I show the source code of an S4 function in a package?](http://stackoverflow.com/questions/5937832/how-do-i-show-the-source-code-of-an-s4-function-in-a-package)

【6】[How can I view the source code for a function?](http://stackoverflow.com/questions/19226816/how-can-i-view-the-source-code-for-a-function)

【7】[Source code of rnorm where can I find it](http://r.789695.n4.nabble.com/Source-code-of-rnorm-where-can-I-find-it-td3255514.html)