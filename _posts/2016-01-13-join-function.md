---
layout: post
title: "数据合并-join函数"
date: 2016-01-13 20:14:06
categories: 技术篇
tags: [R, 数据合并, plyr, dplyr]
---
前面介绍了数据框合并的merge函数方法，其实还可以借助plyr和dplyr包中的join函数进行数据框的合并，它们数据框合并的原理同样是数据框的合并原理是这样的：首先在x表按指定列每一行的内容在y表的指定列都进行逐行匹配，直到x中所有行匹配完为止。

## 数据说明
这里的数据仍使用merge函数中的两个数据(略有修改)：作者信息数据和书籍信息数据。依照下面介绍的合并条件，这两个数据既有相同的内容，又有彼此中不存在的内容。

* 作者信息数据

```
authors <- data.frame(
	id =  c(2, 4, 2, 5, 7, 8, 3),
	surname = I(c("Tukey", "Venables", 'kx', "Tierney", "Ripley", "McNeil", "kuangxu")),
	nationality = c("US", "Australia", 'China', "US", "UK", "Australia", "China"),
	deceased = c("yes", rep("no", 6)))
```
<!--more-->
为了区别数据，就把自己的名字也给安排进去了，唯一的一个中国人啊。

* 书籍信息数据

```
books <- data.frame(
	id = c(1:7,2)
    name = I(c("Tukey", "Venables", "Tierney",
             "Ripley", "Ripley", "McNeil", "R Core", "kuang")),
    title = c("Exploratory Data Analysis",
              "Modern Applied Statistics ...",
              "LISP-STAT",
              "Spatial Statistics", "Stochastic Simulation",
              "Interactive Data Analysis",
              "An Introduction to R", "My R study"),
    other.author = c(NA, "Ripley", NA, NA, NA, NA,
                     "Venables & Smith", NA))
```

## plyr包的join函数
join函数：

``` 
join(x, y, by = NULL, type = 'left', match = 'all')
```

* x,y 为合并的数据框
* by 为排序依据，默认值Null时按名字相同的量匹配,此时,要求必须有相同列名的列
* type为合并方式
	* inner，行：显示x，y中共有的行; 列：显示x，y中的所有列
	* left，行：显示x中所有的行; 列：显示x，y中的所有列，未匹配到的值，不论字符数字，全显示为NA
	* right，行：显示y中所有的行; 列：显示x，y中的所有列，未匹配到的值，不论字符数字，全显示为NA
	* full，先显示x中所有的行在y中的匹配结果，接着显示y中未匹配上的内容
* match匹配的规则
	* first，只匹配y中的第一个记录
	* match，匹配y中所有记录

```
join(authors, books)
join(authors, books, type = 'left', match = 'first')
join(authors, books, type = 'inner')
join(authors, books, type = 'right')
join(authors, books, type = 'full')
```

## dplyr包的join函数
dplyr包是plyr包的升级，join函数在dplyr包中是个系列函数。
join函数：

``` 
join(x, y, by = NULL, copy = FALSE, ...)
```

* x,y 为合并的数据框，不要求x,y中排序列唯一
* by 为排序依据，默认值Null时按名字相同的量匹配,此时,要求必须有相同列名的列
* join为系列函数，包括inner_join、left_join、semi_join和anti_join函数

dplyr包的join函数似乎没有plyr包的join函数的match参数，只能进行所谓的all匹配

### inner_join函数
函数结果, 行：显示x中所有能在y中匹配到行; 列：显示x，y中的所有列

```
library(dplyr)
# 单指标匹配
inner_join(authors, books)
inner_join(authors, books, by = c('surname' = 'name'))
# 多指标匹配
inner_join(authors, books, by = c('id'='id','surname' = 'name'))
```

### left_join函数

结果, 行：显示x中所有的行; 列：显示x，y中的所有列，未匹配到的值，不论字符数字，全显示为NA。

```
# 单指标匹配
left_join(authors, books)
left_join(authors, books, by = c('surname' = 'name'))
# 多指标匹配
left_join(authors, books, by = c('id'='id','surname' = 'name'))
```

### semi_join函数
结果, 行：显示x中所有能在y匹配到行,并对显示结果按匹配依据进行了排序; 列：显示x中的所有列。
```
# 单指标匹配
semi_join(authors, books)
semi_join(authors, books, by = c('surname' = 'name'))
# 多指标匹配
semi_join(authors, books, by = c('id'='id','surname' = 'name'))
```

### anti_join函数
结果, 行：显示x中所有未能在y中匹配到行, 并对显示结果按匹配依据进行了排序; 列：显示x中的所有列。

```
# 单指标匹配
anti_join(authors, books)
anti_join(authors, books, by = c('surname' = 'name'))
# 多指标匹配
anti_join(authors, books, by = c('id'='id','surname' = 'name'))
```

### full_join函数
结果, 行：显示x, y中所有的行, 未匹配到的值，不论字符数字，全显示为NA。

```
# 单指标匹配
full_join(authors, books)
full_join(authors, books, by = c('surname' = 'name'))
# 多指标匹配
full_join(authors, books, by = c('id'='id','surname' = 'name'))
```