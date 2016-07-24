---
layout: post
title: "数据框基本操作的常见方法"
date: 2015-05-16 15:23:57
categories: 技术篇
tags: [R, dplyr]
---
本文所说的数据框基本操作，主要包括添加新列、数据排列、数据筛选、数据选择和对所有列进行相同操作，针对每一种操作在基础包和dplyr包中使用方法进行了对比介绍。相对基础包的处理方法，dplyr包中的方法除了更加灵活外，处理速度也会大幅度提升。

## 添加新列
使用的数据companiesData来自于网络，它是苹果、谷歌以及微软等三家公司在2010到2013年3年间的营收与利润数额，其中fy为年份，company为公司名称，revenue为公司营收，profit为公司利润。创建两列：公司所属国家(country)，其值为America；利润列（margin），其值等于利润(profit)除以营收(revenue)再乘以100。

```
fy <- c(2010,2011,2012,2010,2011,2012,2010,2011,2012)  
company <- c("Apple","Apple","Apple","Google","Google","Google","Microsoft",  
"Microsoft","Microsoft")  
revenue <- c(65225,108249,156508,29321,37905,50175,62484,69943,73723)  
profit <- c(14013,25922,41733,8505,9737,10737,18760,23150,16978)  
companiesData <- data.frame(fy, company, revenue, profit)
```
<!--more-->

* 为列简单创建一个变量名称

```
companiesData <- data.frame(fy, company, revenue, profit)
companiesData$country <- 'America'
companiesData$margin <- (companiesData$profit / companiesData$revenue) * 100
companiesData
		fy   company revenue profit country   margin
	1 2010     Apple   65225  14013  Aerica 21.48409
	2 2011     Apple  108249  25922  Aerica 23.94664
	3 2012     Apple  156508  41733  Aerica 26.66509
	4 2010    Google   29321   8505  Aerica 29.00651
	5 2011    Google   37905   9737  Aerica 25.68790
	6 2012    Google   50175  10737  Aerica 21.39910
	7 2010 Microsoft   62484  18760  Aerica 30.02369
	8 2011 Microsoft   69943  23150  Aerica 33.09838
	9 2012 Microsoft   73723  16978  Aerica 23.02945
```

* 利用transform函数

```
companiesData <- data.frame(fy, company, revenue, profit)
companiesData <- transform(companiesData,  country = 'Aerica', margin = (profit/revenue) * 100)
companiesData
		fy   company revenue profit country   margin
	1 2010     Apple   65225  14013  Aerica 21.48409
	2 2011     Apple  108249  25922  Aerica 23.94664
	3 2012     Apple  156508  41733  Aerica 26.66509
	4 2010    Google   29321   8505  Aerica 29.00651
	5 2011    Google   37905   9737  Aerica 25.68790
	6 2012    Google   50175  10737  Aerica 21.39910
	7 2010 Microsoft   62484  18760  Aerica 30.02369
	8 2011 Microsoft   69943  23150  Aerica 33.09838
	9 2012 Microsoft   73723  16978  Aerica 23.02945
```

相对第一种方法，transform可以同时添加多列，书写相对简单，但是利用tranform时，新列必须是原始列的计算，而不能对刚刚建立起来的列进行计算，换句话说上面的第二条语句，如果要写成下面这样是错误的,原因就在于margin.new = margin \* 1.2中margin是新生的数据列，而非数据中原有的列。

```
companiesData <- transform(companiesData,  country = 'Aerica', margin = (profit/revenue) * 100, margin.new = margin * 1.2)
```

* dplyr包中的mutate函数

dplyr包可以简单地认为是对plyr包的升级，所以这两个包中的mutate函数有相似的作用。mutate函数很好的解决了transform函数不能解决的问题，即mutate函数，允许新列对刚刚建立起来的列进行计算。所以，对于tansform函数来说，错误的用法。

```
companiesData <- transform(companiesData,  country = 'Aerica', margin = (profit/revenue) * 100, margin.new = margin \* 1.2)
```

对于mutate函数来说，是正确的。

```
library(dplyr)
companiesData <- data.frame(fy, company, revenue, profit)
companiesData <- mutate(companiesData, country = 'Aerica', margin = (profit/revenue) * 100, margin.new = margin * 1.2)
companiesData
		fy   company revenue profit country   margin margin.new
	1 2010     Apple   65225  14013  Aerica 21.48409   25.78091
	2 2011     Apple  108249  25922  Aerica 23.94664   28.73597
	3 2012     Apple  156508  41733  Aerica 26.66509   31.99811
	4 2010    Google   29321   8505  Aerica 29.00651   34.80782
	5 2011    Google   37905   9737  Aerica 25.68790   30.82548
	6 2012    Google   50175  10737  Aerica 21.39910   25.67892
	7 2010 Microsoft   62484  18760  Aerica 30.02369   36.02842
	8 2011 Microsoft   69943  23150  Aerica 33.09838   39.71806
	9 2012 Microsoft   73723  16978  Aerica 23.02945   27.63534
```

## 数据排列
数据hflights来自R包hflights，记录的是飞机航班数据信息。

```
library(hflights) 
```

* 利用order函数

```
# 默认升序排列
hf = hflights[order(hflights$DayofMonth, hflights$Month, hflights$Year), ]
# 降序排列
hflights[order(desc(hflights$ArrDelay)), ]
```

* dplyr包中的arrange函数

plyr包中也有具有相似功能arrange函数

```
require(dplyr)
# 默认升序排列
hf = arrange(hflights, DayofMonth, Month, Year)
# 对列名加 desc() 进行倒序
hf = arrange(hflights, desc(ArrDelay))
```

## 数据筛选

数据同样使用自R包hflights的hflights。数据筛选是选择满足符合某些条件的数据行。

```
# 查看各月数据行，方便下面的结果的确认
table(hlihgts$Month)
		1     2     3     4     5     6     7     8     9    10    11    12 
	18910 17128 19470 18593 19172 19600 20548 20176 18065 18696 18021 19117 
```

* 直接选取数据框中满足条件的数据行
	
满足一个条件；
	
```
## 满足数据框hflights中Month列等于1
hf1 = hflights[hflights$Month == 1]
nrow(hf1)
	[1] 18910
```

两个以上的条件：同时满足(相当于逻辑“且”)；

```
# 这里以两个条件为例
## 例子1：满足数据框hflights中Month列同时等于1和2
hf2 = hflights[c(hflights$Month == 1 & hflights$Month == 2), ]
## 其值等于0，数据框hflights的Month列没有同时等于1，又等于2的数据行
nrow(hf2)
	[1] 0
## 例子2：满足数据框hflights中Month列等于1且DayofMonth列等于2
hf3 = hflights[c(hflights$Month == 1 & hflights$DayofMonth == 2), ]
nrow(hf3)
	[1] 678
```

两个以上的条件：至少满足其中一个(相当于逻辑“或”)。

```
# 这里以两个条件为例
## 例子1：满足数据框hflights中Month列同时等于1或2
hf4 = hflights[c(hflights$Month == 1 | hflights$Month == 2), ]
## 其值等于18910(Month = 1) + 17128(Month = 1)
nrow(hf4)
	[1] 36038
## 例子2：满足数据框hflights中Month列等于1或DayofMonth列等于2
hf5 = hflights[c(hflights$Month == 1 | hflights$DayofMonth == 2), ]
nrow(hf5)
	[1] 25764
```

* subset函数

满足一个条件；

```
## 满足数据框hflights中Month列等于1
hf1 = subset(hflights, Month == 1)
nrow(hf1)
	[1] 18910
```

两个以上的条件：同时满足(相当于逻辑“且”)；

```
# 这里以两个条件为例
## 例子1：满足数据框hflights中Month列同时等于1和2
hf2 = subset(hflights, Month == 1 & Month == 2)
## 其值等于0，数据框hflights的Month列没有同时等于1，又等于2的数据行
nrow(hf2)
	[1] 0
## 例子2：满足数据框hflights中Month列等于1且DayofMonth列等于2
hf3 = subset(hflights, Month == 1 & DayofMonth == 2)
nrow(hf3)
	[1] 678
```

两个以上的条件：至少满足其中一个(相当于逻辑“或”)。

```
# 这里以两个条件为例
## 例子1：满足数据框hflights中Month列同时等于1或2
hf4 = subset(hflights, Month == 1 | Month == 2)
## 此时也只有此时，subset函数可以写多个条件
## 这种方法不建议使用
hf4 = subset(hflights, Month == 1, Month == 2)
## 其值等于18910(Month = 1) + 17128(Month = 1)
nrow(hf4)
	[1] 36038
## 例子2：满足数据框hflights中Month列等于1或DayofMonth列等于2
hf5 = subset(hflights, Month == 1 | DayofMonth == 2)
nrow(hf5)
	[1] 25764
```

* dplyr包中的fliter函数

plyr包中并没有相似功能的filter函数。

满足一个条件；

```
require(dplyr)
## 满足数据框hflights中Month列等于1
hf1 = filter(hflights, Month == 1)
nrow(hf1)
	[1] 18910
```

两个以上的条件：同时满足(相当于逻辑“且”)；

```
# 这里以两个条件为例
## 例子1：满足数据框hflights中Month列同时等于1和2
hf2 = filter(hflights, Month == 1 & Month == 2)
##在filter中,','直接相当于'&'
hf2 = filter(hflights, Month == 1, Month == 2)
## 为了统一，这种方法也不推荐使用
## 其值等于0，数据框hflights的Month列没有同时等于1，又等于2的数据行
nrow(hf2)
	[1] 0
## 例子2：满足数据框hflights中Month列等于1且DayofMonth列等于2
hf3 = filter(hflights, Month == 1 & DayofMonth == 2)
## 在filter中,','直接相当于'&'
## 为了统一，这种方法也不推荐使用
hf3 = filter(hflights, Month == 1, DayofMonth == 2)
nrow(hf3)
	[1] 678
```

两个以上的条件：至少满足其中一个(相当于逻辑“或”)。

```
# 这里以两个条件为例
## 例子1：满足数据框hflights中Month列同时等于1或2
hf4 = filter(hflights, Month == 1 | Month == 2)
## 其值等于18910(Month = 1) + 17128(Month = 1)
nrow(hf4)
	[1] 36038
## 例子2：满足数据框hflights中Month列等于1或DayofMonth列等于2
hf5 = filter(hflights, Month == 1 | DayofMonth == 2)
nrow(hf5)
	[1] 25764
```

## 数据选择

数据选择是选择数据框中的制定的列，这里使用的数据是dplyr包中自带的iris函数。

```
head(iris)
	  Sepal.Length Sepal.Width Petal.Length Petal.Width Species
	1          5.1         3.5          1.4         0.2  setosa
	2          4.9         3.0          1.4         0.2  setosa
	3          4.7         3.2          1.3         0.2  setosa
	4          4.6         3.1          1.5         0.2  setosa
	5          5.0         3.6          1.4         0.2  setosa
	6          5.4         3.9          1.7         0.4  setosa
```

* 直接选取

利用字符向量选取指定列；

```
iris.part1 = iris[,c('Sepal.Length', 'Sepal.Width')]
head(iris.part1)
	  Sepal.Length Sepal.Width
	1          5.1         3.5
	2          4.9         3.0
	3          4.7         3.2
	4          4.6         3.1
	5          5.0         3.6
	6          5.4         3.9
```
	
利用数字向量选择指定列；

```
# 利用":"选取相邻列
iris.part2 = iris[,1:2]
head(iris.part2)
	  Sepal.Length Sepal.Width
	1          5.1         3.5
	2          4.9         3.0
	3          4.7         3.2
	4          4.6         3.1
	5          5.0         3.6
	6          5.4         3.9
# 利用一般数字向量选取相邻列
iris.part3 = iris[,c(1, 3, 4)]
head(iris.part3)
	  Sepal.Length Petal.Length Petal.Width
	1          5.1          1.4         0.2
	2          4.9          1.4         0.2
	3          4.7          1.3         0.2
	4          4.6          1.5         0.2
	5          5.0          1.4         0.2
	6          5.4          1.7         0.4

```

利用“-”结合数字向量排除数据框的某些列。

```
iris.part4 = iris[,-(1:2)]
head(iris.part4)
	  Petal.Length Petal.Width Species
	1          1.4         0.2  setosa
	2          1.4         0.2  setosa
	3          1.3         0.2  setosa
	4          1.5         0.2  setosa
	5          1.4         0.2  setosa
	6          1.7         0.4  setosa
iris.part5 = iris[,-c(1, 3, 4)]
head(iris.part5)
	  Sepal.Width Species
	1         3.5  setosa
	2         3.0  setosa
	3         3.2  setosa
	4         3.1  setosa
	5         3.6  setosa
	6         3.9  setosa
```

* 利用dplyr包中的select函数

select函数能完成上部分基础函数的所有操作。plyr包中也没有相似功能的select函数。

```
# 利用列名选取指定列
iris.part1 = select(iris, Sepal.Length, Sepal.Width)
# 甚至可以对选取的列进行重命名
iris.part1 = select(iris, Sepal_length = Sepal.Length, Sepal_Width = Sepal.Width)
head(iris.part1)
	  Sepal_length Sepal_Width
	1          5.1         3.5
	2          4.9         3.0
	3          4.7         3.2
	4          4.6         3.1
	5          5.0         3.6
	6          5.4         3.9
# 利用数字向量选择指定列
iris.part2 = select(iris,1:2)
iris.part3 = iris[, c(1, 3, 4)]
# * 利用"-"结合数字向量排除数据框的某些列 
iris.part4 = select(iris,-c(1:2))
iris.part5 = select(iris,-c(1, 3, 4))
```

select函数还有自己的特点：

* ":"支持相邻列名的选取

```
iris.part6 = select(iris,Sepal.Length:Petal.Length)
head(iris.part6)
	  Sepal.Length Sepal.Width Petal.Length
	1          5.1         3.5          1.4
	2          4.9         3.0          1.4
	3          4.7         3.2          1.3
	4          4.6         3.1          1.5
	5          5.0         3.6          1.4
	6          5.4         3.9          1.7
```

* 支持某些特定语法格式

```
# 以"Petal"开始的列
head(select(iris, starts_with("Petal")))
	  Petal.Length Petal.Width
	1          1.4         0.2
	2          1.4         0.2
	3          1.3         0.2
	4          1.5         0.2
	5          1.4         0.2
	6          1.7         0.4
# 以"Width"结尾的列
head(select(iris, ends_with("Width")))
	  Sepal.Width Petal.Width
	1         3.5         0.2
	2         3.0         0.2
	3         3.2         0.2
	4         3.1         0.2
	5         3.6         0.2
	6         3.9         0.4
# 包含"etal"的列
head(select(iris, contains("etal")))
	  Petal.Length Petal.Width
	1          1.4         0.2
	2          1.4         0.2
	3          1.3         0.2
	4          1.5         0.2
	5          1.4         0.2
	6          1.7         0.4
# 甚至可以对上述个操作用"-"
select(iris, -starts_with("Petal"))
select(iris, -ends_with("Width"))
select(iris, -contains("etal"))
select(iris, -Petal.Length, -Petal.Width)
```

* “-”去除指定列

```
iris.part7 = select(iris,-(Sepal.Length:Petal.Length))
head(iris.part7)
	  Petal.Width Species
	1         0.2  setosa
	2         0.2  setosa
	3         0.2  setosa
	4         0.2  setosa
	5         0.2  setosa
	6         0.4  setosa
```
这里，主要讲述的是结合“:”的使用方法。其实正如上个例子那样，"-"也可与特定的语法格式结合使用。

## 对所有列进行相同操作

这里的操作主要指求和、平均值、方差、标准、最小值、最大值以及简单的四则运算。

* colSums和colMeans
基础包里的colSums和colMeans函数，只能对所有列进行求和和求平均值操作，并且要求所有数据列必须是数字型的, 否则会提示错误。

```
library(hflights)
head(hflights)

# 提取数字类型列
hflights.dat = hflights[,! colnames(hflights) %in% c('CancellationCode', 'UniqueCarrier', 'TailNum', 'Origin', 'Dest')]
head(hflights.dat)
summary(hflights.dat)
colSums(hflights.dat, na.rm = T)
colMeans(hflights.dat, na.rm = T)
```

* dplyr包的summarise_all()或者mutate_all()函数

summarise_all()或者mutate_all()函数可以进行求平均值、标准差、方差、最小值和最大值，此时被处理的数据列也不需要必须是数字类型(只是此时会有警告提示)。然而，无论如何要进行求和操作时，所有数据列必须是数字类型(原因是sum函数对字符求和，直接返回错误，而求平均值、标准差和方差只是返回NA，并提示警告，最小值和最大值时返回真实的结果)。

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

* plyr包的colwise函数

plyr包的colwise函数也能对数据框的所有列进行相同的操作。同样可以直接进行求平均值、标准差、方差、最小值和最大值，此时被处理的数据列也不需要必须是数字类型(只是此时会有警告提示)。当所有数据列都是数字类型可以直接求和。当然也可以多个指标同时计算。

```
# 平均值，字符平均值返回NA
colwise(mean)(hflights)
# 标准差，字符标准差返回NA
colwise(sd)(hflights)
# 方差，字符方差返回NA
colwise(var)(hflights)
# 最小值，字符最小值也是可以运算的
colwise(min)(hflights)
# 最大值，字符最大值也是可以运算的
colwise(max)(hflights)
# 平均值、最小值和最大值
colwise(mean, min, max)(hflights)

# 求和
hflights.dat = hflights[,! colnames(hflights) %in% c('CancellationCode', 'UniqueCarrier', 'TailNum', 'Origin', 'Dest')]
colwise(sum)(hflights)

# 若果某一列中有NA，要想得到结果可以通过调整函数内部的na.rm参数来实现
# 平均值
colwise(function(x)mean(x, na.rm = T))(hflights)
# 标准差
colwise(function(x)sd(x, na.rm = T))(hflights)
# 方差
colwise(function(x)var(x, na.rm = T))(hflights)
# 最小值
colwise(function(x)min(x, na.rm = T))(hflights)
# 最大值
colwise(function(x)max(x, na.rm = T))(hflights)
# 求和
colwise(function(x)sum(x, na.rm = T))(hflights.dat)

temp3 = colwise(function(x){x + 1})(hflights.dat)
head(hflights)
head(temp3)
# 事实上四则运算根本不需要借助函数来实现
temp2 = hflights.dat + 1
```