---
layout: post
title: 字典
date: 2015-04-06 14:33:39
categories: 技术篇
tags: [python]
---
字典，Python内置数据类型之一，有如下属性：

* 任意对象的无序集合
* 通过键而非偏移量读取
* 可变的映射类型
* 长度可变、任意嵌套


对于字典，也要特别注意以下几点：

* 序列运算无效
* 对新索引赋值会添加项
* 键不一定是字符串，原则上任何不可改变对象(如整数，元组)都可以作为键<!--more-->

## 1. 字典的创建

### 空字典的创建

```
# 空字典的创建
D = dict()
D = {} # 两种方法效果相同
```

### 一般字典创建

```
# 方法一，利用{}
D = {'Spam': 2, 'eggs': 3, 'food': ['ham', 'egg']}
D # 注意字典是无序的，因此结果显示与创建时并不一致
# 方法二，dict()
D = dict(Spam = 2, eggs = 3, food = ['ham', 'egg']) # 该方法所有键必须是字符
D = dict([('Spam', 2), ('eggs', 3), ('food', ['ham', 'egg'])]) dict函数只有一个参数，所以要把所有项放到一个列表(或元组)中
# 方法三，利用字典方法创建字典，该方法所有键的值必须一样
D = dict.fromkeys(['a','b']) # 字典有两项，只有键，没有值
D = dict.fromkeys(['a','b'], 0) # 字典有两项，每一项的值都为0
# 方法四，利用字典解析
D = {x : x for x in [1, 2, 3, 4]}
D = {x : x for x in 'spam'}
```

## 2. 字典的引用(通过键而非偏移量)

```
D = {'Spam': 2, 'eggs': 3, 'food': {'ham': 1, 'egg': 2}}
D['Spam']
D['food']['egg']
```

## 3. 字典相关属性的查看

###  字典长度

```
D = {'Spam': 2, 'eggs': 3, 'food': {'ham': 1, 'egg': 2}}
# len字典长度
len(D)
```

### 成员关系判断

```
# in
D = {'Spam': 2, 'eggs': 3, 'food': {'ham': 1, 'egg': 2}}
'Spam' in D
```

### 查看字典的键、值和项

```
D = {'Spam': 2, 'ham':1, 'eggs': 3}
# dict.keys返回字典的键,返回结果是个迭代器，借助list函数查看
list(D.keys()) 
# dict.values返回字典的值,返回结果是个迭代器，借助list函数查看
list(D.values()) 
# dict.items返回字典的项,返回结果是个迭代器，借助list函数查看
list(D.items()) 
```

### 判断键是否在字典中存在

```
# dict.get，键存在与字典则返回键的值，否则什么也不返回
D = {'Spam': 2, 'ham':1, 'eggs': 3}
D.get('Spam')
D.get('Spams') # 键不在字典中，什么也不返回
D.get('Spams', 0) # 键不在字典中，返回0，否则返回该键所对应的值
```

### 字典的键可以执行交集和并集运算

```
D = dict(a = 1, b = 2, c = 3)
K = D.keys() # K类似于集合，可以执行交集和并集运算
V = D.values() # V值不唯一，不能执行交集和并集运算
K | {'x': 4} # 结果为 {'a', 'b', 'c', 'x'}
K & {'b'} # 字典未特定指明时，进行合并集操作，都是针对的是键
K & {'b': 1} # 字典未特定指明时，进行合并集操作，都是针对的是键
K & {'a', 'b', 'c'} # 字典未特定指明时，进行合并集操作，都是针对的是键
D.items() | D.keys() # {('a', 1), ('b', 2), ('c', 3), 'a', 'b', 'c'}
D.items() | D # {('a', 1), ('b', 2), ('c', 3), 'a', 'b', 'c'}
```

## 4. 字典的修改

```
D = {'Spam': 2, 'ham':1, 'eggs': 3}
# 直接改变某个键的值
D['ham'] = ['grill', 'bake', 'fry']
# 添加新项
D['brunch'] = 'Bacon'
# 删除某项
del D('eggs')
D.pop('eggs') # 返回删除的键对应的值
# del 删除字典
del D
# 用一个字典更新另一个字典
# dict1.update(dict2)
# dict2中的键在dict1中存在的则用dict2中键对应值替换调dict1原来的值
# dict2中的键不在dict1中存在的直接合并到dict1中
D1 = {'Spam': 2, 'ham':1, 'eggs': 3}
D2 = {'Spams': 2, 'hams':2, 'eggs': 4}
D1.update(D2)
```

