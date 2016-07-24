---
layout: post
title: group和ungroup函数
date: 2014-12-23 08:11:11
categories: 技术篇
tags: [dplyr, R]
---
　　**dplyr**包是**Hadley**即**plyr**包之后又推出的一个新的数据处理包。如果说相对于R中的基础函数，**plyr**包使输出结果更加友好，那么**dplyr**包不仅使输出的结果更加友好，而且大大提高了运算效率。无论如何，今天这篇文章，主要是介绍**dplyr**中极其小的一部分，**group_by**和**ungroup**函数的使用。(下文所有运行结果，都使用**Tab**进行退格，然后以**#**开始，也有部分结果太长没有展示)

## group_by & ungroup基础
**group_by**函数对数据进行分组，**ungroup**函数去除已分组数据的分组。此外，函数**groups**则能显示出分组数据的分组依据（对于已使用**ungroup**函数去除分组的数据，**groups**函数则显示为NULL）。

### 原始数据 
原始数据的概况。<!--more-->

```
library(dplyr)
## 原始数据
### 这是一个32X12的数据框
mtcars
    #                      mpg cyl  disp  hp drat    wt  qsec vs am gear carb
    # Mazda RX4           21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
    # Mazda RX4 Wag       21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
    # Datsun 710          22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
    # Hornet 4 Drive      21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1
    # Hornet Sportabout   18.7   8 360.0 175 3.15 3.440 17.02  0  0    3    2
    # Valiant             18.1   6 225.0 105 2.76 3.460 20.22  1  0    3    1
    # Duster 360          14.3   8 360.0 245 3.21 3.570 15.84  0  0    3    4
    # Merc 240D           24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
    # Merc 230            22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
    # Merc 280            19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4
    # Merc 280C           17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4
    # Merc 450SE          16.4   8 275.8 180 3.07 4.070 17.40  0  0    3    3
    # Merc 450SL          17.3   8 275.8 180 3.07 3.730 17.60  0  0    3    3
    # Merc 450SLC         15.2   8 275.8 180 3.07 3.780 18.00  0  0    3    3
    # Cadillac Fleetwood  10.4   8 472.0 205 2.93 5.250 17.98  0  0    3    4
    # Lincoln Continental 10.4   8 460.0 215 3.00 5.424 17.82  0  0    3    4
    # Chrysler Imperial   14.7   8 440.0 230 3.23 5.345 17.42  0  0    3    4
    # Fiat 128            32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1
    # Honda Civic         30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
    # Toyota Corolla      33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1
    # Toyota Corona       21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1
    # Dodge Challenger    15.5   8 318.0 150 2.76 3.520 16.87  0  0    3    2
    # AMC Javelin         15.2   8 304.0 150 3.15 3.435 17.30  0  0    3    2
    # Camaro Z28          13.3   8 350.0 245 3.73 3.840 15.41  0  0    3    4
    # Pontiac Firebird    19.2   8 400.0 175 3.08 3.845 17.05  0  0    3    2
    # Fiat X1-9           27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1
    # Porsche 914-2       26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2
    # Lotus Europa        30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2
    # Ford Pantera L      15.8   8 351.0 264 4.22 3.170 14.50  0  1    5    4
    # Ferrari Dino        19.7   6 145.0 175 3.62 2.770 15.50  0  1    5    6
    # Maserati Bora       15.0   8 301.0 335 3.54 3.570 14.60  0  1    5    8
    # Volvo 142E          21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2
```

### group_by & ungroup
**group_by**函数对数据进行分组时，默认情况下对处理的数据的分组情况是忽略掉的，只有把参数add = TRUE才在原始数据的基础上添加新的分类。因此，在默认情况下，在第二次分组时，可以对已分组数据直接进行第二次分组，而不必先用**ungroup**函数拆除已有分组指标。

```
## 对数据mtcars进行分组，分组依据为cyl
by_cyl <- group_by(mtcars, cyl)
groups(by_cyl)
    # [[1]]
    # cyl
## 去除数据mtby_cyl分组依据后，分组依据为NULL
ungroup(by_cyl)
    # NULL
## 这里对by_cyl先去除第一次的分组依据不是必须的
groups(group_by(ungroup(by_cyl), vs, am))
    # [[1]]
    # vs
    # [[2]]
    # am
## 对已分组数据by_cyl进行再次分组，分组依据为vs和am，而没有cyl（参数add默认值为TRUE）
groups(group_by(by_cyl, vs, am))
    # [[1]]
    # vs
    # [[2]]
    # am
## 对以分组数据by_cyl进行再次分组，分组依据为cyl、vs和am
groups(group_by(by_cyl, vs, am, add = TRUE))
    # [[1]]
    # cyl
    # [[2]]
    # vs
    # [[3]]
    # am
```

## group_by & ungroup延伸
使用**group_by**函数进行分组的数据，数据本身常常包含着分组依据，所以在未去除分组（**ungroup**）或下一次分组（使用**group_by**）前，分组依据常常保留，这会影响一些函数的运算结果，其中包括**filter**，~~**arrange**函数~~(0.5.0版本的dplyr包分组不在影响arrange函数的结果，并且作者指出在以后的版本中一直保持这个更改)和指标函数。

### filter函数

```
### 这里按cyl进行分组，所以筛选结果有3条
filter(by_cyl, disp == max(disp))
    # Source: local data frame [3 x 11]
    # Groups: cyl
    #    mpg cyl  disp  hp drat    wt  qsec vs am gear carb
    # 1 21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1
    # 2 24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
    # 3 10.4   8 472.0 205 2.93 5.250 17.98  0  0    3    4
```

### 各指标函数(以平均值为例)

```
## 分组前后结果的差异，即x1与x3、x2与x4的差异
### x1和x求的是整体的平均值，x3和x4求的是按cyl进行分组后的平均值
temp = mtcars %>% mutate(x1 = mean(disp), x2 = mean(hp)) %>% group_by(cyl) %>% mutate(x3 = mean(disp), x4 = mean(hp))
groups(temp)
    # [[1]]
    # cyl
temp

## 两次分组之间的差异
## nvs1 等于nvs2，不等于n.vs.am
## 第一次分组, 显示按vs分类，各组的个数
by_vs <- mtcars %>% arrange(vs, am) %>% group_by(vs) %>% mutate(n.vs1 = n())
groups(by_vs)
    # [[1]]
    # vs
by_vs

## 第二次分组
## 第一次分组在遇到第二次分组后作用就会消失，只显示按am分组，各组的个数
by_vs_am <- by_vs %>% mutate(n.vs2 = n()) %>% group_by(am) %>% arrange(am)  %>% mutate(n.vs.am = n())
groups(by_vs_am)
    # [[1]]
    # am
by_vs_am
```