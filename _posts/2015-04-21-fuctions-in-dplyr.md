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
		 Year Month DayofMonth DayOfWeek DepTime ArrTime UniqueCarrier FlightNum TailNum ActualElapsedTime
	5424 2011     1          1         6    1400    1500            AA       428  N576AA                60
	5425 2011     1          2         7    1401    1501            AA       428  N557AA                60
	5426 2011     1          3         1    1352    1502            AA       428  N541AA                70
	5427 2011     1          4         2    1403    1513            AA       428  N403AA                70
	5428 2011     1          5         3    1405    1507            AA       428  N492AA                62
	5429 2011     1          6         4    1359    1503            AA       428  N262AA                64
		 AirTime ArrDelay DepDelay Origin Dest Distance TaxiIn TaxiOut Cancelled CancellationCode Diverted
	5424      40      -10        0    IAH  DFW      224      7      13         0                         0
	5425      45       -9        1    IAH  DFW      224      6       9         0                         0
	5426      48       -8       -8    IAH  DFW      224      5      17         0                         0
	5427      39        3        3    IAH  DFW      224      9      22         0                         0
	5428      44       -3        5    IAH  DFW      224      9       9         0                         0
	5429      45       -7       -1    IAH  DFW      224      6      13         0                         0
class(hflights)
	[1] "data.frame"
```
<!--more-->

## 数据转换

```
## 将过长过大的数据集转换为显示更友好的 tbl_df 类型
hflights_df <- tbl_df(hflights)
```

## 数据筛选

```
# 筛选filter,类似于base::subset()函数
filter(hflights_df, Month == 1, DayofMonth == 1)
filter(hflights, Month == 1, DayofMonth == 1)
# 用R自带函数实现:
hflights[hflights$Month == 1 & hflights$DayofMonth == 1, ]
# 除了代码简洁外, 还支持对同一对象的任意个条件组合, 如:
filter(hflights_df, Month == 1 | Month == 2)
# 注意: 表示 AND 时要使用 & 而避免 &&
```

## 数据排列

```
# 排列: arrange(),按给定的列名依次对行进行排序.
arrange(hflights_df, DayofMonth, Month, Year)
# 对列名加 desc() 进行倒序
arrange(hflights_df, desc(ArrDelay))
# 这个函数和 plyr::arrange() 是一样的, 类似于 order()
# 用R自带函数实现:
hflights[order(hflights$DayofMonth, hflights$Month, hflights$Year), ]
hflights[order(desc(hflights$ArrDelay)), ]
```

## 数据选择

```
#  选择: select()
# 用列名作参数来选择子数据集:
select(hflights_df, Year, Month, DayOfWeek)
# 还可以用 : 来连接列名, 没错, 就是把列名当作数字一样使用:
select(hflights_df, Year:DayOfWeek)
# 用 - 来排除列名:
select(hflights_df, -(Year:DayOfWeek))
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

## 数据分组
数据分组是指按指定列的内容进行分类统计分析，具体可以参考[group和ungroup函数](http://xukuang.github.io/blog/2014/12/group-by-and-ungroup-function-in-R/)。

```
# 分组动作 group_by()
planes <- group_by(hflights_df, TailNum)
delay <- summarise(planes, 
  count = n(), 
  dist = mean(Distance, na.rm = TRUE), 
  delay = mean(ArrDelay, na.rm = TRUE))
delay <- filter(delay, count > 20, dist < 2000)

# 另: 一些汇总时的小函数
# n(): 计算个数
# n_distinct(): 计算 x 中唯一值的个数. (原文为 count_distinct(x), 测试无用)
# first(x), last(x) 和 nth(x, n): 返回对应秩的值, 类似于自带函数 x[1], x[length(x)], 和 x[n]
```

## 连接符

```
# 3 连接符 %.%
# 包里还新引进了一个操作符, 使用时把数据名作为开头, 然后依次对此数据进行多步操作.
Batting %.%
    group_by(playerID) %.%
    summarise(total = sum(G)) %.%
    arrange(desc(total)) %.%
    head(5)
# 这样可以按进行数据处理时的思路写代码, 一步步深入, 既易写又易读, 接近于从左到右的自然语言顺序
#  对比一下用R自带函数实现的:
head(arrange(summarise(group_by(Batting, playerID), total = sum(G)) , desc(total)), 5)
# 或者像这篇文章所用的方法:
totals <- aggregate(. ~ playerID, data=Batting[,c("playerID","R")], sum)
ranks <- sort.list(-totals$R)
totals[ranks[1:5],]
# 文章里还表示: 用他的 MacBook Air 跑 %.% 那段代码用了 0.036 秒, 
# 跑上面这段代码则用了 0.266 秒, 运算速度提升了近7倍. (当然这只是一例, 还有其它更大的数字.)
```

### 对所有列进行相同操作

* summarise_each可以进行求平均值、标准差、方差、最小值和最大值，此时被处理的数据列也不需要必须是数字类型(只是此时会有警告提示)。然而，无论如何要进行求和操作时，所有数据列必须是数字类型(原因是sum函数对字符求和，直接返回错误，而求求平均值、标准差、方差、最小值和最大值只是返回NA，并提示警告)。当然也可以多个指标同时计算。

```
# 平均值，字符平均值返回NA
summarise_each(hflights, funs(mean))
# 标准差，字符标准差返回NA
summarise_each(hflights, funs(sd))
# 方差，字符方差返回NA
summarise_each(hflights, funs(var))
# 最小值，字符最小值也是可以运算的
summarise_each(hflights, funs(min))
# 最大值，字符最大值也是可以运算的
summarise_each(hflights, funs(max))
# 平均值、最小值和最大值
summarise_each(hflights, funs(mean, min, max))

# 求和
hflights.dat = hflights[,! colnames(hflights) %in% c('CancellationCode', 'UniqueCarrier', 'TailNum', 'Origin', 'Dest')]
summarise_each(hflights.dat, funs(sum))

# 若果某一列中有NA，要想得到结果可以通过调整函数内部的na.rm参数来实现
# 平均值
summarise_each(hflights, funs(mean(., na.rm = T)))
# 标准差
summarise_each(hflights, funs(sd(., na.rm = T)))
# 方差
summarise_each(hflights, funs(var(., na.rm = T)))
# 最小值
summarise_each(hflights, funs(min(., na.rm = T)))
# 最大值
summarise_each(hflights, funs(max(., na.rm = T)))
# 求和
summarise_each(hflights.dat, funs(sum(., na.rm = T)))
```

* mutate_each跟summarise_each最大的不同在于产生于原数据相同行数的结果，所以mutate_each除了可以进行summarise_each函数的操作以外，还可进行简单四则运算，当然进行四则运算时，要保证所有列都是数字型。	

```
temp = mutate_each(hflights.dat, funs( . + 1))
head(hflights)
head(temp)
```
