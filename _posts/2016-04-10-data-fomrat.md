---
layout: post
title: "长宽数据之间的转化(二)"
date: 2016-04-10 10:32:19
categories: 技术篇
tags: [R, tidyr]
---
前面一篇文章[长宽数据之间的转化(一)](http://xukuang.github.io/blog/2016/01/melt-and-dcast/)讲了用reshape2包中的函数实现长宽数据的转化，而tidyr是reshape2的升级版，主要用于数据框。这篇文章将介绍一下如何使用tidyr包中的函数实现长宽数据的转化。不过，在读这篇文章之前，建议你还是读一下前面提到的那一篇文章了解一下关于长宽数据的基础知识。

## 1. 宽数据转化为长数据

### gather函数

```
gather(data, key, value, ..., na.rm = FALSE, convert = FALSE)
```

**data** 要转化的数据，数据格式为数据框类型或者可以转化为数据框类型

**key**  指定转化后数据框的指标列的列名，用于存放原数据中不同的数据指标

**value**  指定转化后数据框的数据列的列名，用于存放原数据中不同的数据指标对应的值		

**...** 指定哪些列聚到一列中。按那些列数据作为标准转化，这个参数接不能有那些列

**na.rm** 确定是否去除数据中的NA，默认情况下为FALSE，不去除NA
<!--more-->

### 实例

* 将所有列排成长数据(默认情况下)

```
# 加载数据
library(reshape2)
head(airquality)
	#   Ozone Solar.R Wind Temp Month Day
	# 1    41     190  7.4   67     5   1
	# 2    36     118  8.0   72     5   2
	# 3    12     149 12.6   74     5   3
	# 4    18     313 11.5   62     5   4
	# 5    NA      NA 14.3   56     5   5
	# 6    28      NA 14.9   66     5   6
dim(airquality)	
	# [1] 153   6
	
library(tidyr)
dat1 = gather(airquality, index, value)
head(dat1)
    #    index value
    # 1 Ozone    41
    # 2 Ozone    36
    # 3 Ozone    12
    # 4 Ozone    18
    # 5 Ozone    NA
    # 6 Ozone    28
tail(dat1)
    #    index value
    # 913  Day    25
    # 914  Day    26
    # 915  Day    27
    # 916  Day    28
    # 917  Day    29
    # 918  Day    30
dim(dat1)	
	# [1] 918   2
```

* 按Month将其他指标排成长数据

```
# 方法一
dat2 = gather(airquality, index, value, Ozone:Temp, Day)
# 方法二
dat2 = gather(airquality, index, value, -Month)
head(dat2)
	#　　Month index value
	#　1     5 Ozone    41
	#　2     5 Ozone    36
	#　3     5 Ozone    12
	#　4     5 Ozone    18
	#　5     5 Ozone    NA
	#　6     5 Ozone    28
tail(dat2)
	#     Month index value
	# 760     9   Day    25
	# 761     9   Day    26
	# 762     9   Day    27
	# 763     9   Day    28
	# 764     9   Day    29
	# 765     9   Day    30
```

* 按Month和Day列将其它列指标排成长数据

```
dat3 = gather(airquality, type, value.type, Ozone:Temp)
dat3 = gather(airquality, type, value.type, -Month, -Day)
head(dat3) 
	# 	 Month Day  type value.type
	# 1     5   1 Ozone         41
	# 2     5   2 Ozone         36
	# 3     5   3 Ozone         12
	# 4     5   4 Ozone         18
	# 5     5   5 Ozone         NA
	# 6     5   6 Ozone         28
dim(dat3)
	# [1] 612   4
```

* 去除排列后数据中的NA

```
#　 airquality$Ozone 数的前四列有44 个NA
table(is.na(airquality$Ozone)) # 37
	# FALSE  TRUE 
	#   116    37 
table(is.na(airquality$Solar.R)) # 4
	# FALSE  TRUE 
	#   146     7 
table(is.na(airquality$Temp)) # 0
	# FALSE 
	#   153 
table(is.na(airquality$Wind)) # 0
	# FALSE 
	#   153 
dat4 = gather(airquality, type, value.type, Ozone:Temp, na.rm = TRUE)
head(dat4)
	#   Month Day  type value.type
	# 1     5   1 Ozone         41
	# 2     5   2 Ozone         36
	# 3     5   3 Ozone         12
	# 4     5   4 Ozone         18
	# 6     5   6 Ozone         28
	# 7     5   7 Ozone         23
dim(dat4)# 比dat3少44行
	# [1] 568   4	
```


## 2. 长数据转化为宽数据

### spread函数
尽管spread()和reshape中的dcast()函数都能实现长数据向宽数据的转化，但两者的的使用方法存在很大的差异。
```
spread(data, key, value, fill = NA, convert = FALSE, drop = TRUE)
```

**data** 要转化的数据，数据格式为数据框类型或者可以转化为数据框类型

**key**  指定需要将变量值拓展为字段的变量

**value**  指定要分散的值

**fill** 确认不存在的组合值的显示结果。默认的显示为NA。数据类型为数值型向量

**drop** 确认不存在分分类组合是否显示，默认情况下显示。当然，如果drop = T，那么fill参数的设置将失去意义

### 实例

```
air1 =  spread(dat4, type, value.type)
dim(air1)
	# [1] 153   6
head(air1) # 数据与airquality相同
	# 	Month Day Ozone Solar.R Wind Temp
	# 1     5   1    41     190  7.4   67
	# 2     5   2    36     118  8.0   72
	# 3     5   3    12     149 12.6   74
	# 4     5   4    18     313 11.5   62
	# 5     5   5    NA      NA 14.3   56
	# 6     5   6    28      NA 14.9   66
```

不存在的分类是否显示，以及怎么显示

```
air5 = spread(dat4, type, value.type, drop = F) # 组合上存在的结果都显示
tail(air5) # 这里的最后一行只是组合上存在，原数据中并不在
	# 		 Month Day Ozone Solar.R Wind Temp
	# 150      9  26     1       1    1    1
	# 151      9  27     1       1    1    1
	# 152      9  28     1       1    1    1
	# 153      9  29     1       1    1    1
	# 154      9  30     1       1    1    1
	# 155      9  31     0       0    0    0
tail(spread(dat4, type, value.type, drop = F, fill= -1)) # 更改不存在结果的显示值，最后一行显示为-1
	# 		Month Day Ozone Solar.R Wind Temp
	# 150     9  26     1       1    1    1
	# 151     9  27     1       1    1    1
	# 152     9  28     1       1    1    1
	# 153     9  29     1       1    1    1
	# 154     9  30     1       1    1    1
	# 155     9  31    -1      -1   -1   -1
```

## tidyr包中的其它函数

### unite函数

unite()函数可将多列合并为一列。

```
unite(data, col, ..., sep = "_", remove = TRUE)
```

**data** 要处理的数据框

**col** 被组合的新列名称

**...** 指定哪些列需要被组合

**sep** 组合列之间的连接符，默认为下划线

**remove** 是否删除被组合的列，默认删除

```
set.seed(10)
date <- as.Date('2016-04-10') + 0:14
hour <- sample(1:24, 15)
min <- sample(1:60, 15)
second <- sample(1:60, 15)
event <- sample(letters, 15)
data <- data.frame(date, hour, min, second, event)
data
	# 			date hour min second event
	# 1  2016-04-10   13  26     33     e
	# 2  2016-04-11    8   4      6     a
	# 3  2016-04-12   10  16     10     l
	# 4  2016-04-13   15  23     52     c
	# 5  2016-04-14    2  47     24     r
	# 6  2016-04-15    5  48     42     h
	# 7  2016-04-16   19  34     45     s
	# 8  2016-04-17   18  42     51     z
	# 9  2016-04-18   22  19     36     i
	# 10 2016-04-19    7  21     26     d
	# 11 2016-04-20   16  36     14     j
	# 12 2016-04-21   23  53     12     g
	# 13 2016-04-22   20  12      1     o
	# 14 2016-04-23   21  37     35     f
	# 15 2016-04-24    4  17     49     n

dataNew <- data %>%
  unite(datehour, date, hour, sep = ' ') %>%
  unite(dateindex, datehour, min, second, sep = ':')
	#              dateindex event
	#1  2016-04-10 13:26:33     e
	#2     2016-04-11 8:4:6     a
	#3  2016-04-12 10:16:10     l
	#4  2016-04-13 15:23:52     c
	#5   2016-04-14 2:47:24     r
	#6   2016-04-15 5:48:42     h
	#7  2016-04-16 19:34:45     s
	#8  2016-04-17 18:42:51     z
	#9  2016-04-18 22:19:36     i
	#10  2016-04-19 7:21:26     d
	#11 2016-04-20 16:36:14     j
	#12 2016-04-21 23:53:12     g
	#13  2016-04-22 20:12:1     o
	#14 2016-04-23 21:37:35     f
	#15  2016-04-24 4:17:49     n

```
tidyr包中的函数也可使用管道操作符%>%。

### seperate函数
separate()函数可将一列拆分为多列，一般可用于日志数据或日期时间型数据的拆分。

```
separate(data, col, into, sep = "[^[:alnum:]]+", remove = TRUE,
  convert = FALSE, extra = "warn", fill = "warn", ...)
```

**data** 要处理的数据框

**col** 需要被拆分的列

**into** 新建的列名，为字符串向量

**sep** 被拆分列的分隔符

**remove** 是否删除被分割的列，默认删除

```
dataold <- dataNew %>% 
  separate(dateindex, c('date', 'index'), sep = ' ') %>% 
  separate(index, c('hour', 'min', 'second'), sep = ':')
dataold
	# 		date hour min second event
	# 1  2016-04-10   13  26     33     e
	# 2  2016-04-11    8   4      6     a
	# 3  2016-04-12   10  16     10     l
	# 4  2016-04-13   15  23     52     c
	# 5  2016-04-14    2  47     24     r
	# 6  2016-04-15    5  48     42     h
	# 7  2016-04-16   19  34     45     s
	# 8  2016-04-17   18  42     51     z
	# 9  2016-04-18   22  19     36     i
	# 10 2016-04-19    7  21     26     d
	# 11 2016-04-20   16  36     14     j
	# 12 2016-04-21   23  53     12     g
	# 13 2016-04-22   20  12      1     o
	# 14 2016-04-23   21  37     35     f
	# 15 2016-04-24    4  17     49     n
```


