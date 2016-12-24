---
layout: post
title: summarise 和 mutate 函数使用
date: 2014-06-01 16:16:37
categories: 技术篇
tags: [plyr, R]
---

　　**summarise**和**mutate**是**R**中**plyr**包的两个函数。**plyr**是**R**中用来数据汇总的超强R包，肖凯对**plyr**的使用在网易云课堂(该链接已经失效)有较为详细的讲述。本文只对其中的**summarise**和**mutate**函数进行简单介绍，并讲述一下它和**ddply**函数的搭配使用。(注意，这两个函数在plyr包升级后的dplyr包中依然适用)

## summarise 和 mutate 函数
　　**summarise**和**mutate**函数都可以对一个数据框的某一列**(而不是整个数据框)**进行修改和汇总，两者的主要区别在于返回结果的方式不同，其中**summarise**函数返回一个只包含修改或汇总后数据的数据框，而**mutate**函数则返回一个由原始数据和修改或汇总后数据两部分构成的数据框(mutate函数与基础包的transform函数相似，两者的区别在于；muate函数可以对刚刚建立起来的列进行计算，而transform函数只能针对数据的原始列进行计算，详细内容可以参考[这篇文章](http://xukuang.github.io/blog/2015/05/common-methods-of-dealing-with-data-frame/))。具体内容加下面代码：<!--more-->

```
require(plyr)
set.seed(1) # 保证每次产生的数据框的唯一性
dfx <- data.frame(
  group = c(rep('A', 8), rep('B', 15), rep('C', 6)),
  sex = sample(c("M", "F"), size = 29, replace = TRUE),
  age = sample(20:30, size = 29, replace = TRUE),
  worktime = sample(1:5, size = 29, replace = TRUE)
)
### 数据修改
summarise(dfx, age = age + 1) # 返回一个只含一列age的数据框
mutate(dfx, age = age + 1) # 返回一个和dfx列数一样的4列数据框，但age列的数值已经修改
### 数据汇总
summarise(dfx, mean.age = mean(age), sd.age = sd(age)) # 返回一个只含汇总结果的2列数据框
mutate(dfx, mean.age = mean(age), sd.age = sd(age)) # 返回一个由dfx和汇总结果组成的4列数据框
```

## ddply函数
　　**ddply**函数按数据框的某列对数据进行汇总，其形式如下：

```
ddply(.data, .variables, .fun = NULL, ..., .progress = "none",.inform = FALSE, .drop = TRUE, .parallel = FALSE, .paropts = NULL)
```
这里对其中常用参数进行说明：

.data 要进行汇总的数据，这里要求数据格式为数据框
.variables 分类依据，这里是数据框中分类列的列名
.fun 汇总的方式，这里有三种常用的形式
.drop 是否去除不存在的汇总行，默认的方式是去除不存在的汇总行

## 单变量的汇总

常见汇总方式有三种，这里我们用单变量进行举例说明。

### 整个数据框的汇总（统计数据框的行数和列数，即nrow和ncol）

```
## 数据框的汇总，只有函数，但其中没有参数
ddply(dfx, .(sex), nrow) #这里不能对汇总结果进行命名，即 numrow = nrow 是错误的
ddply(dfx, .(sex), ncol) #这里不能对汇总结果进行命名，即 numrow = nrow 是错误的
ddply(dfx, .(sex), c("nrow","ncol"))
```
　　注意：这种方式是对整个数据框进行的，不能使用summarise或者mutate参数。

### 数据框中某一列的汇总

#### 关于指标函数（最大值，最小值，平均值，方差，标准差， 中位数）的计算

```
## 关于指标函数的计算，函数中包含参数，需借助summarise或mutate参数
## summarise参数和mutate参数的区别与函数summarise和mutate的区别一样，
## summarise参数返回的结果只包含汇总结果，mutate参数返回结果包含原始数据和汇总数据
	## summarise
ddply(dfx, .(sex), summarise, max(worktime))
ddply(dfx, .(sex), summarise, min(worktime))
ddply(dfx, .(sex), summarise, mean(worktime))
ddply(dfx, .(sex), summarise, sd(worktime))
ddply(dfx, .(sex), summarise, median(worktime))
## mutate
ddply(dfx, .(sex), mutate, max(worktime))
ddply(dfx, .(sex), mutate, min(worktime))
ddply(dfx, .(sex), mutate, mean(worktime))
ddply(dfx, .(sex), mutate, sd(worktime))
ddply(dfx, .(sex), mutate, median(worktime))
```

注意：三种汇总方式中，只有这种方式且计算指标函数时可以选择性对汇总结果进行命名，即下面的使用方式也是对的。
​	
```
## summarise
ddply(dfx, .(sex), summarise, worktime.max = max(worktime))
ddply(dfx, .(sex), summarise, worktime.min = min(worktime))
ddply(dfx, .(sex), summarise, worktime.mean = mean(worktime))
ddply(dfx, .(sex), summarise, worktime.sd = sd(worktime))
ddply(dfx, .(sex), summarise, worktime.sd = median(worktime))
## mutate
ddply(dfx, .(sex), mutate, worktime.max = max(worktime))
ddply(dfx, .(sex), mutate, worktime.min = min(worktime))
ddply(dfx, .(sex), mutate, worktime.mean = mean(worktime))
ddply(dfx, .(sex), mutate, worktime.sd = sd(worktime))
ddply(dfx, .(sex), mutate, worktime.sd = median(worktime))
```
　　注意：这种方式下，summarise或者mutate参数至少要选择其中的一个，否则不能得到想要的结果。此外，这种方式下，只是对数据的原始列进行计算时，mutate可以用基础包里的transform替换，一旦对新生成的数据进行计算transform替换mutate将会发生错误，所以推荐用mutate。

#### 通过subset实现对数据的筛选
```
ddply(dfx, .(sex), subset,  age > mean(age))
```


### 通用汇总方式（编写函数）

　　这种汇总方式是最通用的汇总方式，其中的函数参数为数据框。事实上，1,2的汇总方式都可以通过3的汇总方式调用函数实现。

* 1对应的函数调用方式

```
ddply(dfx, .(sex), function(x){nrow(x)}) # 不能对汇总结果进行重新命名，即numrow = nrow(x)是错误的
ddply(dfx, .(sex), function(x){ncol(x)}) # 不能对汇总结果进行重新命名，即numcol = ncol(x)是错误的
```

* 2对应的函数调用方式

```
## 这里其实对应2中summarise参数的形式
ddply(dfx, .(sex), function(x){max(x$worktime)})
ddply(dfx, .(sex), function(x){min(x$worktime)})
ddply(dfx, .(sex), function(x){mean(x$worktime)})
ddply(dfx, .(sex), function(x){sd(x$worktime)})
ddply(dfx, .(sex), function(x){median(x$worktime)})
```

* colwise函数

　　colwise函数是plyr包的内置函数，可以对数据框中所有的数据列进行相同的操作，省去每一例的大量重复操作。(类似的功能在dplyr包中可以通过summarise_all或mutate_all来实现)

* 直接对数据列进行相同操作

```
dfx.data = dfx[, c('age', 'worktime')]
dfx.data
colwise(mean)(dfx.data)
colwise(mean)(dfx.data)
```
　　这里对所有列求平均值时，如果某些列是字符无法进行相应操作，则为返回NA，只是有“warning messages:”的提示，但是并不影响最终的结果，因此，也可以直接求平均值。

```
colwise(mean)(dfx)
colwise(mean)(dfx)
```

* 按指定类分类后进行相同操作

```
ddply(dfx, .(group), colwise(mean))
```
　　事实上，通用汇总方式的用途比我们的例子强大的多，这里有点就事论事的味道，反而限制了该方式的灵活运用。

## 多变量的汇总
plyr包当然不仅仅可以对单一列变量进行分析，还可以进行多变量的分析。

```
ddply(dfx, .(sex, age), function(x){mean(x$worktime)})

ddply(dfx, .(sex, age), function(x){mean(x$worktime)}, .drop = F)
```
　　上面两句的结果并不完全一样，在参数.drop = F会把sex和age两类中原来并不存在的组合以NA的形式展示出来。(在dplyr包 和data.talbe包中尚未发现类似.drop = F这样的功能)。

　　最后，本文的所有方法只是个人的使用心得，可能有很多不对的地方，欢迎大家提出不同的意见。