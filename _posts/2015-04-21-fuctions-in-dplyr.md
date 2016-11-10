---
layout: post
title: "dplyr包中的基础操作"
date: 2015-04-21 10:16:25
categories: 技术篇
tags: [R, dplyr]
---
以前在网上看到的关于dplyr包基础函数的文章，放在这儿可以方便自己以后使用的时候随时查询,下文所有程序运行的结果，都使用Tab进行退格(仓皇上传，版面相当粗糙，后面抽时间详细整理一下)。

## dplyr包的加载

```
# dplyr包加载
library(dplyr)
# 加载使用到其中数据的这些包
library(Lahman) # Lahman包的棒球比赛数据
library(hflights) # hflights包里的飞机航班数据
head(hflights)
	# 	   Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier FlightNum TailNum ActualElapsedTime
	# 5424 2011     1          1         6    1400    1500            AA       428  N576AA                60
	# 5425 2011     1          2         7    1401    1501            AA       428  N557AA                60
	# 5426 2011     1          3         1    1352    1502            AA       428  N541AA                70
	# 5427 2011     1          4         2    1403    1513            AA       428  N403AA                70
	# 5428 2011     1          5         3    1405    1507            AA       428  N492AA                62
	# 5429 2011     1          6         4    1359    1503            AA       428  N262AA                64
	# 	   AirTime ArrDelay DepDelay Origin Dest Distance TaxiIn TaxiOut Cancelled CancellationCode Diverted
	# 5424      40      -10        0    IAH  DFW      224      7      13         0                         0
	# 5425      45       -9        1    IAH  DFW      224      6       9         0                         0
	# 5426      48       -8       -8    IAH  DFW      224      5      17         0                         0
	# 5427      39        3        3    IAH  DFW      224      9      22         0                         0
	# 5428      44       -3        5    IAH  DFW      224      9       9         0                         0
	# 5429      45       -7       -1    IAH  DFW      224      6      13         0                         0
class(hflights)
	# [1] "data.frame"
```
<!--more-->

## 数据转换

将过长过大的数据集转换为显示更友好的 tbl_df 类型，这一步通常不需要单独操作，而在使用dplyr包中函数时一般函数会自动调整。
```
hflights_df <- tbl_df(hflights)
```

## 数据筛选

```
# 一下两个语句作用相同，为了与其它格式保持一致，建议使用第二种
filter(hflights, Month == 1, DayofMonth == 1)
filter(hflights, Month == 1 & DayofMonth == 1)

filter(hflights_df, Month == 1 | DayofMonth == 1)

```

## 无重复内容的筛选

```
# distinct函数
distinct(hflights_df, Month, .keep_all = TRUE)
```
这里参数.keep_all = TRUE指保留除Month以外的其它列的内容。默认的情况是不保存其他列的。

## 数据排列
arrange(),按给定的列名依次对行进行排序，使用arrange()函数。

```
arrange(hflights_df, DayofMonth, Month, Year)
# 对列名加 desc() 进行倒序
arrange(hflights_df, desc(ArrDelay))

```

## 数据选择

### select函数
select()函数实现对指定列的选择。

```
select(hflights_df, Year, Month, DayOfWeek)
# 可以用 : 来连接列名, 没错, 就是把列名当作数字一样使用
select(hflights_df, Year:DayOfWeek)
# 用 - 来排除列名
select(hflights_df, -(Year:DayOfWeek))
```

### select_if()函数

select_if()函数对不同条件列的选择。

```
hflights %>% select_if(is.factor)
hflights %>% select_if(is.numeric)
hflights %>% select_if(function(col) is.numeric(col) && mean(col) > 3.5)
```

## 数据变形

```
# 变形: mutate()
# 对已有列进行数据运算并添加为新列:
mutate(hflights_df, 
  gain = ArrDelay - DepDelay, 
  speed = Distance / AirTime * 60)
# 作用与 plyr::mutate() 相同, 与 base::transform() 相似, 优势在于可以在同一语句中对刚增加的列进行操作:
mutate(hflights_df, 
  gain = ArrDelay - DepDelay, 
  gain_per_hour = gain / (AirTime / 60)
)
# 而同样操作用R自带函数 transform() 的话就会报错:
transform(hflights, 
  gain = ArrDelay - DepDelay, 
  gain_per_hour = gain / (AirTime / 60)
)
```

## 数据分组（group_by()函数）
数据分组是指按指定列的内容进行分类统计分析，分类汇总后会影响一些函数的计算结果，具体可以参考[group和ungroup函数](http://xukuang.github.io/blog/2014/12/group-by-and-ungroup-function-in-R/)。

```
planes <- group_by(hflights_df, TailNum)
delay <- summarise(planes, 
  count = n(), 
  dist = mean(Distance, na.rm = TRUE), 
  delay = mean(ArrDelay, na.rm = TRUE))
delay <- filter(delay, count > 20, dist < 2000)
```

此外，分类汇总时，一些也可用到：

* n()计算个数
* n_distinct()计算唯一值的个数
* first(x), last(x) 和 nth(x, n): 返回值, 类似于基本包函数 x[1], x[length(x)], 和 x[n]

## 连接符（%>%）

```
Batting %>% group_by(playerID) %>%
summarise(total = sum(G)) %>% arrange(desc(total)) %>% head(5)
```

### 对所有列进行相同操作
dpyr包中对所有列进行相同的操作最初采用的是summarise_each()和mutate_each()函数，在0.5.0版本以后的dplyr包将逐渐使用summarise_all()和mutate_all()函数，这里也主要将summarise_all()和mutate_all()函数。这里的相同操作主要包括求平均值、标准差、方差、最小值和最大值，此时被处理的数据列不需要必须是数字类型(只是此时会有警告提示)。然而，无论如何要进行求和操作时，所有数据列必须是数字类型(原因是sum函数对字符求和，直接返回错误，而求平均值、标准差、方差、最小值和最大值只是返回NA，并提示警告)。

* summarise_all()函数

```
# 平均值，字符平均值返回NA
summarise_all(hflights, mean)
# 标准差，字符标准差返回NA
summarise_all(hflights, sd)
# 方差，字符方差返回NA
summarise_all(hflights, var)
# 最小值，字符最小值也是可以运算的
summarise_all(hflights, min)
# 最大值，字符最大值也是可以运算的
summarise_all(hflights, max)

# 求和
hflights.dat = hflights[,! colnames(hflights) %in% c('CancellationCode', 'UniqueCarrier', 'TailNum', 'Origin', 'Dest')]
summarise_all(hflights.dat, sum))

```

* mutate_all()函数

区别于summarise_all()函数在于mutate_all()函数产生与原数据相同行和列数的结果，这里mutate_all以及下面的mutate_at()和mutate_if()函数也不同于dplyr包的mutate()函数，mutate函数在原始数据后面产生新的结果，而mutate_系列函数则直接产生与原数据相同行和列数的结果。	

```
temp = mutate_all(hflights.dat,  mean))
head(hflights)
head(temp)
```

此外，summarise_all()和mutate_all()函数中使用的函数还可以使用完全形式，这时候不仅可以更改结果列的名字。mutate_all()的结果将与mutate函数一样在原始数据后面产生新的结果。

```
mutate_all(hflights, funs("in" = . / 2.54))
mutate_all(hflights,funs(med = median))
summarise_all(hflights,funs(med = median))
# 平均值、最小值和最大值
summarise_all(hflights, funs(mean, min, max))

```

### 对特定列进行相同操作

#### summarise_at()和mutate_at()函数

```
mtcars %>% group_by(cyl) %>% summarise_at(c("mpg", "wt"), mean)
mtcars %>% group_by(cyl) %>% summarise_at(vars(mpg, wt), mean)
mtcars %>% group_by(cyl) %>% mutate_at(c("mpg", "wt"), mean)
mtcars %>% group_by(cyl) %>% mutate_at(vars(mpg, wt), mean)
```

#### summarise_if()和mutate_if()函数

```
iris %>% summarise_if(is.numeric, mean, trim = 0.25)
iris %>% mutate_if(is.numeric, mean, trim = 0.25)
```
同样，summarise_at()和mutate_at() && summarise_if()和mutate_if()函数函数中的函数也可以使用完全形式，其结果也包含原始数据。