---
layout: post
title: "数据合并-merge函数"
date: 2015-05-29 10:35:12
categories: 技术篇
tags: [R, 数据合并]
---
## merge函数介绍

merge函数是R的基础包中的函数，不需要加载R包可以直接使用，主要用于数据框的合并。数据框的合并原理是这样的：首先在x表按指定列每一行的内容在y表的指定列都进行逐行匹配，直到x中所有行匹配完为止。

```
merge(x, y, by = intersect(names(x), names(y)),
      by.x = by, by.y = by, all = FALSE, all.x = all, all.y = all,
      sort = TRUE, suffixes = c(".x",".y"),
      incomparables = NULL, ...)
```
<!--more-->

**参数说明**

* x,y：指两个用于合并的数据框。如果x，y不是数据框，那么合并时也将被强制转换为数据框，所以x，y必须是数据框类型，或者可以转换为数据框的数据类型。
* by, by.x, by.y：这三个参数共同限定来说明x，y合并按哪一列进行。在没有特别指明参数值的情况下，x，y的合并以数据框中列名相同的相同的列进行（x，y合并时，x依照by.x指定的列进行合并，y依照by.y指定的列进行合并。在默认情况情况下，by.x，by.y 都等于by，而by为x，y中列名相同的列,所以默认情况下会依照两个数据框中列名相同的内容进行合并）。如果要修改x，y合并时依照的的条件，可以直接更改by.x和by.y的值。
* all, all.x, all.y：这三个参数共同限定来表明结果的输出方式。在没有特别指明参数值的情况下，结果只会显示x，y这两个数据框中指定列中的相同内容的整理结果，而不同内容根本不会显示（x，y合并时，结果依照all.x是否显示x中不相同的内容，依照all.y是否显示y中不相同的内容。在默认情况情况下，all.x，all.y 都等于all，而all = FALSE，所以结果只会显示x，y这两个数据框中指定列中的相同内容的整理结果。如果要更改x，y中的显示结果，可以相应更改更改all.x和all.y的参数设置。
* sort：指明是否按指定列中内容对结果排序显示。默认值为TRUE，表明默认情况下会对结果重新排序。
* suffsuffixes：合并时除了依照条件外还有相同的列名，则结果中这个列表名会添加.x和.y后缀以示区别。
* incomparables：合并时可以通过这个设置来排除相同列中部分相同内容的合并。

**结果说明**
merge函数的结果展示时，当y中每个内容只有一个结果能跟x匹配上时，结果的行数就会与从x中提取出指定合并列的公有内容的函数相一致。一旦当y中某个内容有多个结果能跟x匹配上时，结果的行数就会发生变化。

## 实例说明

### 数据说明

这里用到的数据有两个：作者信息数据和书籍信息数据。依照下面介绍的合并条件，这两个数据既有相同的内容，又有彼此中不存在的内容。

* 作者信息数据

```
authors <- data.frame(
	id =  c(2, 4, 5, 7, 8, 3),
	surname = I(c("Tukey", "Venables", "Tierney", "Ripley", "McNeil", "kuangxu")),
	nationality = c("US", "Australia", "US", "UK", "Australia", "China"),
	deceased = c("yes", rep("no", 5)))
```
为了区别数据，就把自己的名字也给安排进去了，唯一的一个中国人啊。

* 书籍信息数据

```
books <- data.frame(
	id = 1:7,
    name = I(c("Tukey", "Venables", "Tierney",
             "Ripley", "Ripley", "McNeil", "R Core")),
    title = c("Exploratory Data Analysis",
              "Modern Applied Statistics ...",
              "LISP-STAT",
              "Spatial Statistics", "Stochastic Simulation",
              "Interactive Data Analysis",
              "An Introduction to R"),
    other.author = c(NA, "Ripley", NA, NA, NA, NA,
                     "Venables & Smith"))
```

### 结果显示

#### by未设定的默认情况下，依照列名相同的列进行展示而且只显示相同内容的整理结果

```
merge(authors, books)
	#   id  surname nationality deceased     name                         title     other.author
	# 1  2    Tukey          US      yes Venables Modern Applied Statistics ...           Ripley
	# 2  3  kuangxu       China       no  Tierney                     LISP-STAT             <NA>
	# 3  4 Venables   Australia       no   Ripley            Spatial Statistics             <NA>
	# 4  5  Tierney          US       no   Ripley         Stochastic Simulation             <NA>
	# 5  7   Ripley          UK       no   R Core          An Introduction to R Venables & Smith
```

注意，哪个数据框在x的位置，结果将优先显示那个数据框的的列。当by=NULL时，则把数据框authors的每一行跟books的第一行匹配，之后匹配第二行，接着匹配下一行，直到books最后一行匹配完才结束(目前，还没有在plyr和dplyr中的join系列函数或者data.table包的merge函数中看到这个功能)。

```
merge(authors, books, by = NULL)
	#    id.x  surname nationality deceased id.y     name                         title     other.author
	# 1     2    Tukey          US      yes    1    Tukey     Exploratory Data Analysis             <NA>
	# 2     4 Venables   Australia       no    1    Tukey     Exploratory Data Analysis             <NA>
	# 3     5  Tierney          US       no    1    Tukey     Exploratory Data Analysis             <NA>
	# 4     7   Ripley          UK       no    1    Tukey     Exploratory Data Analysis             <NA>
	# 5     8   McNeil   Australia       no    1    Tukey     Exploratory Data Analysis             <NA>
	# 6     3  kuangxu       China       no    1    Tukey     Exploratory Data Analysis             <NA>
	# 7     2    Tukey          US      yes    2 Venables Modern Applied Statistics ...           Ripley
	# 8     4 Venables   Australia       no    2 Venables Modern Applied Statistics ...           Ripley
	# 9     5  Tierney          US       no    2 Venables Modern Applied Statistics ...           Ripley
	# 10    7   Ripley          UK       no    2 Venables Modern Applied Statistics ...           Ripley
	# 11    8   McNeil   Australia       no    2 Venables Modern Applied Statistics ...           Ripley
	# 12    3  kuangxu       China       no    2 Venables Modern Applied Statistics ...           Ripley
	# 13    2    Tukey          US      yes    3  Tierney                     LISP-STAT             <NA>
	# 14    4 Venables   Australia       no    3  Tierney                     LISP-STAT             <NA>
	# 15    5  Tierney          US       no    3  Tierney                     LISP-STAT             <NA>
	# 16    7   Ripley          UK       no    3  Tierney                     LISP-STAT             <NA>
	# 17    8   McNeil   Australia       no    3  Tierney                     LISP-STAT             <NA>
	# 18    3  kuangxu       China       no    3  Tierney                     LISP-STAT             <NA>
	# 19    2    Tukey          US      yes    4   Ripley            Spatial Statistics             <NA>
	# 20    4 Venables   Australia       no    4   Ripley            Spatial Statistics             <NA>
	# 21    5  Tierney          US       no    4   Ripley            Spatial Statistics             <NA>
	# 22    7   Ripley          UK       no    4   Ripley            Spatial Statistics             <NA>
	# 23    8   McNeil   Australia       no    4   Ripley            Spatial Statistics             <NA>
	# 24    3  kuangxu       China       no    4   Ripley            Spatial Statistics             <NA>
	# 25    2    Tukey          US      yes    5   Ripley         Stochastic Simulation             <NA>
	# 26    4 Venables   Australia       no    5   Ripley         Stochastic Simulation             <NA>
	# 27    5  Tierney          US       no    5   Ripley         Stochastic Simulation             <NA>
	# 28    7   Ripley          UK       no    5   Ripley         Stochastic Simulation             <NA>
	# 29    8   McNeil   Australia       no    5   Ripley         Stochastic Simulation             <NA>
	# 30    3  kuangxu       China       no    5   Ripley         Stochastic Simulation             <NA>
	# 31    2    Tukey          US      yes    6   McNeil     Interactive Data Analysis             <NA>
	# 32    4 Venables   Australia       no    6   McNeil     Interactive Data Analysis             <NA>
	# 33    5  Tierney          US       no    6   McNeil     Interactive Data Analysis             <NA>
	# 34    7   Ripley          UK       no    6   McNeil     Interactive Data Analysis             <NA>
	# 35    8   McNeil   Australia       no    6   McNeil     Interactive Data Analysis             <NA>
	# 36    3  kuangxu       China       no    6   McNeil     Interactive Data Analysis             <NA>
	# 37    2    Tukey          US      yes    7   R Core          An Introduction to R Venables & Smith
	# 38    4 Venables   Australia       no    7   R Core          An Introduction to R Venables & Smith
	# 39    5  Tierney          US       no    7   R Core          An Introduction to R Venables & Smith
	# 40    7   Ripley          UK       no    7   R Core          An Introduction to R Venables & Smith
	# 41    8   McNeil   Australia       no    7   R Core          An Introduction to R Venables & Smith
	# 42    3  kuangxu       China       no    7   R Core          An Introduction to R Venables & Smith
```

#### by.x和by.y修改参照条件

```
merge(authors, books, by.x = "surname", by.y = "name")
	#    surname id.x nationality deceased id.y                         title other.author
	# 1   McNeil    8   Australia       no    6     Interactive Data Analysis         <NA>
	# 2   Ripley    7          UK       no    4            Spatial Statistics         <NA>
	# 3   Ripley    7          UK       no    5         Stochastic Simulation         <NA>
	# 4  Tierney    5          US       no    3                     LISP-STAT         <NA>
	# 5    Tukey    2          US      yes    1     Exploratory Data Analysis         <NA>
	# 6 Venables    4   Australia       no    2 Modern Applied Statistics ...       Ripley
```
注意，两个数据框中都有相同的列id，但并没有把id作为合并依照的条件，在结果显示中分别显示为id.x和id.y。

#### all.x和all.y调整显示结果

在未对参数设置时，只显示相同内容所在的行。

* 显示x中所有的行（all.x = T）

```
merge(authors, books, by.x = "surname", by.y = "name", all.x = T)
	# surname id.x nationality deceased id.y                         title other.author
	# 1  kuangxu    3       China       no   NA                          <NA>         <NA>
	# 2   McNeil    8   Australia       no    6     Interactive Data Analysis         <NA>
	# 3   Ripley    7          UK       no    4            Spatial Statistics         <NA>
	# 4   Ripley    7          UK       no    5         Stochastic Simulation         <NA>
	# 5  Tierney    5          US       no    3                     LISP-STAT         <NA>
	# 6    Tukey    2          US      yes    1     Exploratory Data Analysis         <NA>
	# 7 Venables    4   Australia       no    2 Modern Applied Statistics ...       Ripley
```

* 显示y中所有的行（all.y = T）

```
merge(authors, books, by.x = "surname", by.y = "name", all.y = T)
	# surname id.x nationality deceased id.y                         title     other.author
	# 1   McNeil    8   Australia       no    6     Interactive Data Analysis             <NA>
	# 2   R Core   NA        <NA>     <NA>    7          An Introduction to R Venables & Smith
	# 3   Ripley    7          UK       no    4            Spatial Statistics             <NA>
	# 4   Ripley    7          UK       no    5         Stochastic Simulation             <NA>
	# 5  Tierney    5          US       no    3                     LISP-STAT             <NA>
	# 6    Tukey    2          US      yes    1     Exploratory Data Analysis             <NA>
	# 7 Venables    4   Australia       no    2 Modern Applied Statistics ...           Ripley
```

* 显示x和y中所有的行（all.x = T, all.y = T）

```
merge(authors, books, by.x = "surname", by.y = "name", all.x = T, all.y = T)
	# surname id.x nationality deceased id.y                         title     other.author
	# 1  kuangxu    3       China       no   NA                          <NA>             <NA>
	# 2   McNeil    8   Australia       no    6     Interactive Data Analysis             <NA>
	# 3   R Core   NA        <NA>     <NA>    7          An Introduction to R Venables & Smith
	# 4   Ripley    7          UK       no    4            Spatial Statistics             <NA>
	# 5   Ripley    7          UK       no    5         Stochastic Simulation             <NA>
	# 6  Tierney    5          US       no    3                     LISP-STAT             <NA>
	# 7    Tukey    2          US      yes    1     Exploratory Data Analysis             <NA>
	# 8 Venables    4   Australia       no    2 Modern Applied Statistics ...           Ripley
```

#### sort是否对合并结果重新排序
sort = F时，对合并结果不进行排序，注意这里的不排序是根据排序条件中内容出现的先后进行数据展示而非按原始数据的行的顺序进行数据展示。

```
merge(authors, books, by.x = "surname", by.y = "name", sort = F)
	# surname id.x nationality deceased id.y                         title other.author
	# 1    Tukey    2          US      yes    1     Exploratory Data Analysis         <NA>
	# 2 Venables    4   Australia       no    2 Modern Applied Statistics ...       Ripley
	# 3  Tierney    5          US       no    3                     LISP-STAT         <NA>
	# 4   Ripley    7          UK       no    4            Spatial Statistics         <NA>
	# 5   Ripley    7          UK       no    5         Stochastic Simulation         <NA>
	# 6   McNeil    8   Australia       no    6     Interactive Data Analysis         <NA>
merge(authors, books, by.x = "surname", by.y = "name")
	#    surname id.x nationality deceased id.y                         title other.author
	# 1   McNeil    8   Australia       no    6     Interactive Data Analysis         <NA>
	# 2   Ripley    7          UK       no    4            Spatial Statistics         <NA>
	# 3   Ripley    7          UK       no    5         Stochastic Simulation         <NA>
	# 4  Tierney    5          US       no    3                     LISP-STAT         <NA>
	# 5    Tukey    2          US      yes    1     Exploratory Data Analysis         <NA>
	# 6 Venables    4   Australia       no    2 Modern Applied Statistics ...       Ripley
```
注意，上面的程序显示的结果是根据x列的内容进行排序的，而下面的sort未设置的默认情况对结果按照排序条件进行了重新排序。

#### incomparables来排除相同列的不同内容

* 未对incomparables设置

```
merge(authors, books, by.x = "surname", by.y = "name")
	#    surname id.x nationality deceased id.y                         title other.author
	# 1   McNeil    8   Australia       no    6     Interactive Data Analysis         <NA>
	# 2   Ripley    7          UK       no    4            Spatial Statistics         <NA>
	# 3   Ripley    7          UK       no    5         Stochastic Simulation         <NA>
	# 4  Tierney    5          US       no    3                     LISP-STAT         <NA>
	# 5    Tukey    2          US      yes    1     Exploratory Data Analysis         <NA>
	# 6 Venables    4   Australia       no    2 Modern Applied Statistics ...       Ripley
```

* incomparables = 'Tukey'时，则即使'Tukey'为相同的内容也要排除在外

```
merge(authors, books, by.x = "surname", by.y = "name", incomparables = 'Tukey')
	# surname id.x nationality deceased id.y                         title other.author
	# 1   McNeil    8   Australia       no    6     Interactive Data Analysis         <NA>
	# 2   Ripley    7          UK       no    4            Spatial Statistics         <NA>
	# 3   Ripley    7          UK       no    5         Stochastic Simulation         <NA>
	# 4  Tierney    5          US       no    3                     LISP-STAT         <NA>
	# 5 Venables    4   Australia       no    2 Modern Applied Statistics ...       Ripley
```

#### 此外排序条件也可以针对多列
merge(authors, books, by.x = c("surname", "id"), by.y = c("name", "id"))这条命令从理论上说也是对的，只是结果当中不存在相同的行。