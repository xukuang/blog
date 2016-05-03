---
layout: post
title: "长宽数据之间的转化(一)"
date: 2016-01-18 10:32:19
categories: 技术篇
tags: [R, reshape2]
---
这里将数据框分为两种：宽数据和长数据，宽数据便于数据成果的展示，长数据则便于数据在R中进行分析。reshape2包中的melt函数可以实现宽数据向长数据的转换，而dcast可以实现宽数据向长数据的转化。 

## 宽数据转化为长数据

### melt函数

```
melt(data, id.vars, measure.vars,
  variable.name = "variable", ..., na.rm = FALSE, value.name = "value",
  factorsAsStrings = TRUE)
```

**data** 要转化的数据，数据格式为数据框类型或者可以转化为数据框类型	
**id.vars** 数据的转化标准：依照那些列进行转化，数据格式为字符型或数字型的向量。如果该参数没有限定，会把所有数据转化为两列，其中指标一列，数据一列。
**measure.vars**	被转化的数据：将哪些列数据值转化，该参数在没有指定的默认情况下，会将id.vars中以外的所有列数据进行转化，数据格式为字符型或数字型的向量
**variable.name**	对转化后的数据标准列重新命名，默认情况下，列名为variable
**na.rm** 是否去除数据中的NA，默认情况下为FALSE，不去除NA
**value.name** 对转化后的数据列重新命名，默认情况下，列名为value
**factorsAsStrings**	转化过程中是否将因子类型数据转化为字符型数据，默认情况下为TRUE，将因子类型数据转化为字符型数据
<!--more-->

### 实例

* 按Month和Day列将其他指标排成长数据

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
dat1 = melt(airquality, id.vars = c('Month', 'Day'))
head(dat1)
	# 	Month Day variable value
	# 1     5   1    Ozone    41
	# 2     5   2    Ozone    36
	# 3     5   3    Ozone    12
	# 4     5   4    Ozone    18
	# 5     5   5    Ozone    NA
	# 6     5   6    Ozone    28
dim(dat1)
	# [1] 612   4
```
需要注意的是melt函数当中如果id.vars参数不指定值，则会把所有数据转化为两列输出。

* 按Month和Day列将Ozone和Solar.R列指标排成长数据

```
dat2 = melt(airquality, id.vars = c('Month', 'Day'),
 measure = c('Ozone', 'Solar.R'))
head(dat2)
	# 	Month Day variable value
	# 1     5   1    Ozone    41
	# 2     5   2    Ozone    36
	# 3     5   3    Ozone    12
	# 4     5   4    Ozone    18
	# 5     5   5    Ozone    NA
	# 6     5   6    Ozone    28
dim(dat2) # 函数是dat1的1/2
	# [1] 306   4 
```

* 对排列后的数据进行重命名

```
dat3 = melt(airquality, id.vars = c('Month', 'Day'),
variable.name = 'type', value.name = 'value.type')
head(dat3) # 列名发生变化
	# 	Month Day  type value.type
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
dat4 = melt(airquality, id.vars = c('Month', 'Day'),
variable.name = 'type', value.name = 'value.type', na.rm = TRUE)
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
此外，还发现melt函数中所有参数名都可以只写'.'前的部分。

```
dat5 = melt(airquality, id = c('Month', 'Day'),
variable = 'type', value = 'value.type', na = TRUE)
head(dat5)
	# 	Month Day  type value
	# 1     5   1 Ozone    41
	# 2     5   2 Ozone    36
	# 3     5   3 Ozone    12
	# 4     5   4 Ozone    18
	# 5     5   5 Ozone    NA
	# 6     5   6 Ozone    28
```

## 长数据转化为宽数据

### dcast函数

```
dcast(data, formula, fun.aggregate = NULL, ..., margins = NULL,
  subset = NULL, fill = NULL, drop = TRUE,
  value.var = guess_value(data))
```
**data** 要转化的数据，数据格式为数据框类型或者可以转化为数据框类型	
**formula** 转化的标准，以公式的形式定义
**fun.aggregate** 汇总函数。分类后，如果分类结果不唯一，就必须使用汇总函数。常用的汇总函数，包括求个数、和、平均值、标准差、方差，默认的为求个数
**margins**	确定结果是否显示总的结果，默认不显示
**subset** 是否只对某些标准内容进行计算，结合plyr包一起使用
**fill** 不存在的组合值的显示结果。默认的显示为0。数据类型为数值型向量
**drop** 确认不存在分分类组合是否显示，默认情况下显示
**value.var** 貌似对对转化后的数据进行命名，使用默认情况即可

### 实例

* 每个分类结果有一个数据，此时不需要使用汇总函数

```R
air1 =  dcast(dat4, Month + Day ~ type)
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

* 按Month列排成宽数据，每个分类结果有多个数据，必须有汇总函数

```
air2 =  dcast(dat4, Month ~ type, sum) # 这里使用sum函数
dim(air2)
	# [1] 5 5
head(air2)
	# 	Month Ozone Solar.R  Wind Temp
	# 1     5    NA      NA 360.3 2032
	# 2     6    NA    5705 308.0 2373
	# 3     7    NA    6711 277.2 2601
	# 4     8    NA      NA 272.6 2603
	# 5     9    NA    5023 305.4 2307
```

* 是否显示总的分类结果

```
air3 = dcast(dat4, Month ~ type, sum, margins = T) #列、行的总汇总都显示
dim(air3)
	# [1] 6 6
head(air3)
	#   Month Ozone Solar.R   Wind  Temp (all)
	# 1     5    NA      NA  360.3  2032    NA
	# 2     6    NA    5705  308.0  2373    NA
	# 3     7    NA    6711  277.2  2601    NA
	# 4     8    NA      NA  272.6  2603    NA
	# 5     9    NA    5023  305.4  2307    NA
	# 6 (all)    NA      NA 1523.5 11916    NA
dcast(dat4, Month ~ type, sum, margins = c('Month'))# 只显示列的汇总
	# 	Month Ozone Solar.R   Wind  Temp
	# 1     5    NA      NA  360.3  2032
	# 2     6    NA    5705  308.0  2373
	# 3     7    NA    6711  277.2  2601
	# 4     8    NA      NA  272.6  2603
	# 5     9    NA    5023  305.4  2307
	# 6 (all)    NA      NA 1523.5 11916
dcast(dat4, Month ~ type, sum, margins = c('type'))# 只显示行的汇总
	# 	Month Ozone Solar.R  Wind Temp (all)
	# 1     5    NA      NA 360.3 2032    NA
	# 2     6    NA    5705 308.0 2373    NA
	# 3     7    NA    6711 277.2 2601    NA
	# 4     8    NA      NA 272.6 2603    NA
	# 5     9    NA    5023 305.4 2307    NA
```

* 只显示部分的分类内容

```
library(plyr)
air4 = dcast(dat4, Month ~ type, sum, subset = .(type == 'Wind' )) # 只显示type中Wind的情况
dim(air4)
	# [1] 5 2
head(air4)
	# 	Month  Wind
	# 1     5 360.3
	# 2     6 308.0
	# 3     7 277.2
	# 4     8 272.6
	# 5     9 305.4
```

* 不存在的分类是否显示，以及怎么显示

```
air5 = dcast(dat4, Month + Day ~ type, length, drop = F) # 组合上存在的结果都显示
tail(air5) # 这里的最后一行只是组合上存在，数据中并不在
	# 		 Month Day Ozone Solar.R Wind Temp
	# 150      9  26     1       1    1    1
	# 151      9  27     1       1    1    1
	# 152      9  28     1       1    1    1
	# 153      9  29     1       1    1    1
	# 154      9  30     1       1    1    1
	# 155      9  31     0       0    0    0
tail(dcast(dat4, Month + Day ~ type, length, drop = F, fill= -1)) # 更改不存在结果的显示值，最后一行显示为-1
	# 		Month Day Ozone Solar.R Wind Temp
	# 150     9  26     1       1    1    1
	# 151     9  27     1       1    1    1
	# 152     9  28     1       1    1    1
	# 153     9  29     1       1    1    1
	# 154     9  30     1       1    1    1
	# 155     9  31    -1      -1   -1   -1
```



