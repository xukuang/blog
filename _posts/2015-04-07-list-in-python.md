---
layout: post
title: 列表
date: 2015-04-07 16:24:21
categories: 技术篇
tags: [python]
---
列表，Python中最灵活的内置数据类型，有如下属性：

* 任意对象的有序集合
* 通过偏移读取
* 属于可变序列
* 长度可变、异构、任意嵌套<!--more-->

## 1. 列表的创建

### 空列表的创建

```
# 空列表的创建
L = list()
L
	## []
L = [] # 两种方法效果相同
	## []
```

### 一般列表创建

```
# 方法一，利用[]
L = ['Spam', 'eggs', ['ham', 'egg']]
# 方法二，list()
L = list(['Spam', 'eggs', ['ham', 'egg']]) # list函数只有一个参数，所以要把所有对象放到一个列表(或元组)中
L = list(range(-5,5)) # 可迭代对象产生的列表
L = list('spam') # 可迭代对象产生的列表
# 方法三，利用列表解析
L = [x for x in range(-5, 5)]
L = [x for x in [1, 2, 3, 4]]
L = [x * 4 for x in 'spam']
```

## 2. 列表的引用

### 列表的索引，返回该位置的对象

```
L = ['Spam', 'eggs', ['ham', 'egg']]
L[2] # 返回一个列表
L[2][1] # 返回一个字符串
```

### 列表的分片，返回一个列表

```
L = ['Spam', 'eggs', ['ham', 'egg']]
L[0:2]
```

## 3. 列表相关属性的查看

###  列表长度

```
L = ['Spam', 'eggs', ['ham', 'egg']]
# len列表长度
len(L)
```

### 成员关系判断

```
# in
L = ['Spam', 'eggs', ['ham', 'egg']]
'Spam' in L
# L.index
L.index('Spam') # 返回参数在列表中的位置
```

### 列表某项出现次数统计

```
# L.count
L = ['Spam', 'eggs', 'I', 'eggs']
L.count('eggs')
```

### 列表排序

列表排序时，要求列表的每一项的类型应该相同，且该类型可以比较。对列表排序后，列表本身发生了变化。

```
# L.sort升序排列
L = ['Spam', 'eggs', 'I'] 
L.sort() # 字符串只比较首字符
L = [[1,2],[3,4],[1,5]]
L.sort()
L = [4, 3, 9, 2]
L.sort()
```

### 列表原地翻转

```
# L.reverse
L = ['Spam', 'eggs', 'I']
L.reverse()
```

### 列表重复

```
L = ['Spam', 'eggs', ['ham', 'egg']]
L * 3
```

## 4. 列表的修改

```
L = ['Spam', 'eggs', ['ham', 'egg']]
# 根据索引和分片改变列表
L[2] = 'apples'
L[0:2] = ['eat', 'more']
L[0:2] = ['I', 'eat', 'more'] # 注意分片，长度不一样时的结果
# 在末尾在添加新的一项
L[4] = 'please' 
L.append("please")
# 列表某个位置添加一项
L.insert(1, 'kuangxu') # 原来的项往后推移
# 删除列表某一项
L.pop() # 删除最后一项并返回最后一项的值
L.pop(0) # 删除列表的第一项并返回第一项的值，这里参数是项的位置
del L[0]
L.remove("Spam") # 删除列表的"Spam"这一项
# 在末尾在添加多项
# list1.extend(list2)
L = [1, 2, 3]
L.extend([1, 2, 3])
[1, 2, 3] + [1, 2, 3]
```