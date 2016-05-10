---
layout: post
title: "data.table的包的简单介绍"
date: 2016-04-13 13:56:01
categories: [技术篇]
tags: [R, data.talbe]
---
相比dplyr包，data.table包能够更大程度地提高数据的处理速度，这里就简单介绍一下data.tale包的使用方法。


## 数据的读取
data.table包中数据读取的函数是fread()。


## data.table 的创建

```
library(data.table)
DT = data.table(x=rep(c("a","b","c"),each=3), y=c(1,3,6), v=1:9)
DT
	#    x y v
	# 1: a 1 1
	# 2: a 3 2
	# 3: a 6 3
	# 4: b 1 4
	# 5: b 3 5
	# 6: b 6 6
	# 7: c 1 7
	# 8: c 3 8
	# 9: c 6 9
```
<!--more-->

## 基础操作

### 行提取
行提取分为单行提取和多行提取。

#### 单行提取

```
DT[2]                      # 2nd row
	#    x y v
	# 1: a 3 2
DT[2,]                     # same
	#    x y v
	# 1: a 3 2
```
这里DT[2]和DT[2,]是完全相同的，这里的『,』只是说明还有其他参数可设置，而其他参数按默认值进行计算。下文所有这样的最后一个『,』都不再写出来。

#### 多行提取

* 数字提取

```
DT[1:2]
	#    x y v
	# 1: a 1 1
	# 2: a 3 2
DT[c(2,5)]
	#   x y v
	#1: a 3 2
	#2: b 3 5
```

* 逻辑提取

```
DT[c(FALSE,TRUE)]          # even rows (usual recycling)
	#    x y v
	# 1: a 3 2
	# 2: b 1 4
	# 3: b 6 6
	# 4: c 3 8
```

此时,c(FALSE,TRUE)会自己重复匹配成与DT的行数相同的向量


### 列提取
与行提取相同，列的提取也包含单列提取和多列提取。

#### 单列提取

* 数字提取

数字提取时，一定要把wth参数设置为FALSE。

```
DT[,2,with=FALSE]          # 2nd column
	#    y
	# 1: 1
	# 2: 3
	# 3: 6
	# 4: 1
	# 5: 3
	# 6: 6
	# 7: 1
	# 8: 3
	# 9: 6
```

* 列名提取

```
DT[,list(v)]               # v column (as data.table
	#    v
	# 1: 1
	# 2: 2
	# 3: 3
	# 4: 4
	# 5: 5
	# 6: 6
	# 7: 7
	# 8: 8
	# 9: 9
```

### 列名的修改
列名的修改可以使用setnames()函数，这个函数好像比对data.frame类型数据名更改的names()和colnames()函数也要快一些。

```
dt = data.table(a=1:2,b=3:4,c=5:6) # compare to data.table
try(tracemem(dt))                  # by reference, no deep or shallow copies
setnames(dt,"b","B")               # by name, no match() needed (warning if "b" is missing)
setnames(dt,3,"C")                 # by position with warning if 3 > ncol(dt)
setnames(dt,2:3,c("D","E"))        # multiple
setnames(dt,c("a","E"),c("A","F")) # multiple by name (warning if either "a" or "E" is missing)
setnames(dt,c("X","Y","Z"))        # replace all (length of names must be == ncol(DT))  
```

#### 多列提取

* 数字提取

如同上面对按数字对单列的提取，对多列提取也要设置with参数为FALSE。

```
DT[,2:3,with=FALSE]
	#    y v
	# 1: 1 1
	# 2: 3 2
	# 3: 6 3
	# 4: 1 4
	# 5: 3 5
	# 6: 6 6
	# 7: 1 7
	# 8: 3 8
	# 9: 6 9
DT[,c(1,3),with=FALSE] 
	#    x v
	# 1: a 1
	# 2: a 2
	# 3: a 3
	# 4: b 4
	# 5: b 5
	# 6: b 6
	# 7: c 7
	# 8: c 8
	# 9: c 9      
```

* 按列名提取

```
DT[,list(y, v)]
	#    y v
	# 1: 1 1
	# 2: 3 2
	# 3: 6 3
	# 4: 1 4
	# 5: 3 5
	# 6: 6 6
	# 7: 1 7
	# 8: 3 8
	# 9: 6 9
```

如果按列名提取时，不使用列表，仍然能对列进行提取，只是结果以向量的形式输出。

```
DT[,v]                     # v column (as vector)
	# [1] 1 2 3 4 5 6 7 8 9
DT[,c(v)]                  # same
	# [1] 1 2 3 4 5 6 7 8 9   
DT[, c(y, v)]
	# [1] 1 3 6 1 3 6 1 3 6 1 2 3 4 5 6 7 8 9
```

### 列的添加与删除

#### 列的添加

* 单列添加

```
DT
	#    x y v
	# 1: a 1 1
	# 2: a 3 2
	# 3: a 6 3
	# 4: b 1 4
	# 5: b 3 5
	# 6: b 6 6
	# 7: c 1 7
	# 8: c 3 8
	# 9: c 6 9
DT[, a := 'k']
DT
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
	# 4: b 1 4 k
	# 5: b 3 5 k
	# 6: b 6 6 k
	# 7: c 1 7 k
	# 8: c 3 8 k
	# 9: c 6 9 k
DT[,c:=8]        # add a numeric column, 8 for all rows
DT
	#    x y v a c
	# 1: a 1 1 k 8
	# 2: a 3 2 k 8
	# 3: a 6 3 k 8
	# 4: b 1 4 k 8
	# 5: b 3 5 k 8
	# 6: b 6 6 k 8
	# 7: c 1 7 k 8
	# 8: c 3 8 k 8
	# 9: c 6 9 k 8 
DT[,d:=9L]       # add an integer column, 9L for all rows
DT[2,d:=10L]     # subassign by reference to column d
DT
	#    x y v a c  d
	# 1: a 1 1 k 8  9
	# 2: a 3 2 k 8 10
	# 3: a 6 3 k 8  9
	# 4: b 1 4 k 8  9
	# 5: b 3 5 k 8  9
	# 6: b 6 6 k 8  9
	# 7: c 1 7 k 8  9
	# 8: c 3 8 k 8  9
	# 9: c 6 9 k 8  9
DT[, e := d + 2]
DT
	#    x y v a c  d  e
	# 1: a 1 1 k 8  9 11
	# 2: a 3 2 k 8 10 12
	# 3: a 6 3 k 8  9 11
	# 4: b 1 4 k 8  9 11
	# 5: b 3 5 k 8  9 11
	# 6: b 6 6 k 8  9 11
	# 7: c 1 7 k 8  9 11
	# 8: c 3 8 k 8  9 11
	# 9: c 6 9 k 8  9 11
```
如果添加的列名，数据中已经包含则是对这一列数据的修改。

* 多列的添加

```
DT[, c('f', 'g') := list( d + 1, c)]
DT[, ':='( f =  d + 1, g = c)]          # same
DT
	#    x y v a c  d  e  f g
	# 1: a 1 1 k 8  9 11 10 8
	# 2: a 3 2 k 8 10 12 11 8
	# 3: a 6 3 k 8  9 11 10 8
	# 4: b 1 4 k 8  9 11 10 8
	# 5: b 3 5 k 8  9 11 10 8
	# 6: b 6 6 k 8  9 11 10 8
	# 7: c 1 7 k 8  9 11 10 8
	# 8: c 3 8 k 8  9 11 10 8
	# 9: c 6 9 k 8  9 11 10 8
```
此处，需要注意的是新创建的列只能依照原有数据列，而不能依照新创建的列。例如这个例子中，g = c是可以运行，而g = f则会提示错误。

#### 列的删除

```
DT[,c:=NULL]     # remove column c
DT
	#    x y v a  d  e  f g
	# 1: a 1 1 k  9 11 10 8
	# 2: a 3 2 k 10 12 11 8
	# 3: a 6 3 k  9 11 10 8
	# 4: b 1 4 k  9 11 10 8
	# 5: b 3 5 k  9 11 10 8
	# 6: b 6 6 k  9 11 10 8
	# 7: c 1 7 k  9 11 10 8
	# 8: c 3 8 k  9 11 10 8
	# 9: c 6 9 k  9 11 10 8
DT[, c('d', 'e', 'f', 'g'):=NULL]     
DT
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
	# 4: b 1 4 k
	# 5: b 3 5 k
	# 6: b 6 6 k
	# 7: c 1 7 k
	# 8: c 3 8 k
	# 9: c 6 9 k
```


## 列指标的简单操作
简单操作主要包括求和、平均值、方差和标准差等。

```
DT[2:3,sum(v)]             # sum(v) over rows 2 and 3
# [1] 5
DT[2:3,mean(v)]             # sum(v) over rows 2 and 3
# [1] 2.5
```

## 索引key

### 查看和创建索引
索引是对列而言的，索引创建后，数据将自动按索引值进行重新排序，所以每个数据最多只能有一个索引，但是索引可以由多列组成，这些列可以是数字、因子、字符串或其他格式。

#### 单列索引的创建

```

## methdod first
key(DT)                    # key
	# NULL 
setkey(DT,x)               # set a 1-column key. No quotes, for convenience.
key(DT)
[1] "x"
DT
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
	# 4: b 1 4 k
	# 5: b 3 5 k
	# 6: b 6 6 k
	# 7: c 1 7 k
	# 8: c 3 8 k
	# 9: c 6 9 k

## method second
setkeyv(DT,"y")            # same (v in setkeyv stands for vector)
key(DT)
	# [1] "y"
```
一旦对数据进行新的索引，原有的索引将消失。

#### 多列索引的创建

```
## methdod first                    # key 
setkey(DT,x,v)               # set a 1-column key. No quotes, for convenience.
key(DT)
	# [1] "x" "v"
DT
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
	# 4: b 1 4 k
	# 5: b 3 5 k
	# 6: b 6 6 k
	# 7: c 1 7 k
	# 8: c 3 8 k
	# 9: c 6 9 k


## method second
setkeyv(DT,c("x", "y"))           # same (v in setkeyv stands for vector)
key(DT)
	# [1] "x" "v"
DT
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
	# 4: b 1 4 k
	# 5: b 3 5 k
	# 6: b 6 6 k
	# 7: c 1 7 k
	# 8: c 3 8 k
	# 9: c 6 9 k
```


### 通过索引进行数据的提取
按照索引对数据提取，可以加快提取数据的速度。

#### 单索引
正向提取

```
setkey(DT, x)
DT["a"]                    # binary search (fast)
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
DT[.(x=="a")]                 # same; i.e. binary search (fast)
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
DT[x=="a"]                 # same; i.e. binary search (fast)
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
```

* 反向提取

```
DT[!.("a")]                # not join
	#    x y v a
	# 1: b 1 4 k
	# 2: b 3 5 k
	# 3: b 6 6 k
	# 4: c 1 7 k
	# 5: c 3 8 k
	# 6: c 6 9 k
DT[!"a"]                   # same
	#    x y v a
	# 1: b 1 4 k
	# 2: b 3 5 k
	# 3: b 6 6 k
	# 4: c 1 7 k
	# 5: c 3 8 k
	# 6: c 6 9 k
DT[!2:4]                   # all rows other than 2:4
	#    x y v a
	# 1: a 1 1 k
	# 2: b 3 5 k
	# 3: b 6 6 k
	# 4: c 1 7 k
	# 5: c 3 8 k
	# 6: c 6 9 k

```

#### 多索引

* 正向提取

```
setkey(DT, x, y)
# Mehtod First
DT["a"]                    # join to 1st column of key
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
DT[.("a")]                 # same, .() is an alias for list()
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
DT[.("a",3)]               # join to 2 columns
    #    x y v a
	# 1: a 3 2 k
DT[.("a",3:6)]             # join 4 rows (2 missing)
	#    x y  v  a
	# 1: a 3  2  k
	# 2: a 4 NA NA
	# 3: a 5 NA NA
	# 4: a 6  3  k
DT[.("a",3:6),nomatch=0]   # remove missing
	#    x y v a
	# 1: a 3 2 k
	# 2: a 6 3 k
DT[.("a",3:6),roll=TRUE]   # rolling join (locf)
	#    x y v a
	# 1: a 3 2 k
	# 2: a 4 2 k
	# 3: a 5 2 k
	# 4: a 6 3 k

## Method Second
DT[J('a')]
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
DT[J("a",3)]               # binary search (fast)
	#    x y v a
	# 1: a 3 2 k
DT[J("a",3:6)]              # same; i.e. binary search (fast)
	#    x y  v  a
	# 1: a 3  2  k
	# 2: a 4 NA NA
	# 3: a 5 NA NA
	# 4: a 6  3  k
DT[J("a",3:6), nomatch = 0]
	#    x y v a
	# 1: a 3 2 k
	# 2: a 6 3 k
DT[J("a",3:6), roll = T]
	#    x y v a
	# 1: a 3 2 k
	# 2: a 4 2 k
	# 3: a 5 2 k
	# 4: a 6 3 k


## Method Third
DT[list("a")]
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
DT[list("a",3)]
    #    x y v a
	# 1: a 3 2 k
DT[list("a", 3:6)]
	#    x y  v  a
	# 1: a 3  2  k
	# 2: a 4 NA NA
	# 3: a 5 NA NA
	# 4: a 6  3  k
DT[list("a", 3:6), nomatch = 0]
	#    x y v a
	# 1: a 3 2 k
	# 2: a 6 3 k
DT[list("a", 3:6), roll = T]
	#    x y v a
	# 1: a 3 2 k
	# 2: a 4 2 k
	# 3: a 5 2 k
	# 4: a 6 3 k
```

* 反向提取

```
DT[x!="b" | y!=3]          # not yet optimized, currently vector scans
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
	# 4: b 1 4 k
	# 5: b 6 6 k
	# 6: c 1 7 k
	# 7: c 3 8 k
	# 8: c 6 9 k
DT[!.("b",3)]              # same result but much faster
	#    x y v a
	# 1: a 1 1 k
	# 2: a 3 2 k
	# 3: a 6 3 k
	# 4: b 1 4 k
	# 5: b 6 6 k
	# 6: c 1 7 k
	# 7: c 3 8 k
	# 8: c 6 9 k

```

## 分类汇总
分类汇总是指按某列的分类指标进行简单操作，这里借助by参数实现。此外，by参数与索引相互没有影响。

### 单指标的分类汇总

* 默认汇总名称

```
DT[,sum(v),by=x]
	#    x V1
	# 1: a  6
	# 2: b 15
	# 3: c 24           
DT[,sum(v),by=y] 
	#    y V1
	# 1: 1 12
	# 2: 3 15
	# 3: 6 18          
```
* 自定义汇总名称

```
DT[,list(sum.v.x = sum(v)),by=x]
	#    x sum.v.x
	# 1: a       6
	# 2: b      15
	# 3: c      24           
DT[,list(sum.v.y = sum(v)),by=y] 
	#    y sum.v.y
	# 1: 1      12
	# 2: 3      15
	# 3: 6      18 
DT[,sum.v.y := sum(v) ,by=y]
	#    x y v a sum.v.y
	# 1: a 1 1 k      12
	# 2: a 3 2 k      15
	# 3: a 6 3 k      18
	# 4: b 1 4 k      12
	# 5: b 3 5 k      15
	# 6: b 6 6 k      18
	# 7: c 1 7 k      12
	# 8: c 3 8 k      15
	# 9: c 6 9 k      18 	
```

* 汇总结果与原始数据进行匹配

```
DT[,sum.v.y := sum(v) ,by=y]
	#    x y v a sum.v.y
	# 1: a 1 1 k      12
	# 2: a 3 2 k      15
	# 3: a 6 3 k      18
	# 4: b 1 4 k      12
	# 5: b 3 5 k      15
	# 6: b 6 6 k      18
	# 7: c 1 7 k      12
	# 8: c 3 8 k      15
	# 9: c 6 9 k      18
```

### 多指标的多个分类汇总

* 默认汇总名称

```
DT[,list(mean(v),sum(v)),by=list(x,y)]   # keyed by
	#    x y V1 V2
	# 1: a 1  1  1
	# 2: a 3  2  2
	# 3: a 6  3  3
	# 4: b 1  4  4
	# 5: b 3  5  5
	# 6: b 6  6  6
	# 7: c 1  7  7
	# 8: c 3  8  8
	# 9: c 6  9  9
```

* 自定义汇总名称

```
DT[,list(mean.v = mean(v),sum.v = sum(v)),by=list(x,y)]   # keyed by
	#   x y mean.v sum.v
	#1: a 1      1     1
	#2: a 3      2     2
	#3: a 6      3     3
	#4: b 1      4     4
	#5: b 3      5     5
	#6: b 6      6     6
	#7: c 1      7     7
	#8: c 3      8     8
	#9: c 6      9     9
```

* 汇总结果与原始数据进行匹配

```
DT[,c("mean.v", "sum.v.y") := list(mean(v),sum(v)) ,by=list(x,y)]
	#    x y v a sum.v.y mean.v
	# 1: a 1 1 k       1      1
	# 2: a 3 2 k       2      2
	# 3: a 6 3 k       3      3
	# 4: b 1 4 k       4      4
	# 5: b 3 5 k       5      5
	# 6: b 6 6 k       6      6
	# 7: c 1 7 k       7      7
	# 8: c 3 8 k       8      8
	# 9: c 6 9 k       9      9

```

## data.table与data.frame的转化
data.table格式加快了处理速度，而data.frame则更为基础。两者的转化可以通过data.table(),setDT()和setDT()来实现，其中data.table()和setDT()函数可以将数据从data.frame转化为data.table，setDF()函数可以将数据从data.table转化为data.frame。注意使用data.table(),setDT()和setDT()时，参数本身的数据类型也会发生变化。

```
class(DT)
	# [1] "data.table" "data.frame"  
class(setDF(DT))
	# [1] "data.frame"
class(DT)
	# [1] "data.frame"
```
此外，data.table包还可以与基础包中的duplicated()、unique()、subset() 函数结合使用。 不仅如此，data.table包还有一些基础包的替代函数。rbind()升级版的rbindlist()，可以合并列数不同和列位置不同的数据。比dplyr包中arrange()函数更快的setorder()排序函数。