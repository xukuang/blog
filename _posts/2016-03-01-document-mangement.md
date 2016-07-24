---
layout: post
title: "用R进行文件系统管理"
date: 2016-03-01 17:24:47
categories: 技术篇
tags: [R, 文件系统管理]
---
学习中，已经体会到了R在数据处理方面的厉害，偶然看到张丹的博文[《用R进行文件系统管理》](http://blog.fens.me/r-file-folder/)，发现R居然还可以进行文件系统的管理，又一次全新的认识了世界（其实主要是R）。这里基本上是把张丹的文章照搬过来了，只是加了少许自己学习时心得。本文中提到的文件系统管理主要是指文件和目录（即文件夹）的创建、查看、重命名、复制、删除和文件权限处理。

## 文件操作

* 创建文件

```
# 创建一个空文件 A.txt
file.create("A.txt")
# 查看当前目录下的子目录和文件
list.files()

# 创建多个空文件 A1.txt，A2.txt，A3.txt
file.create("A1.txt", "A2.txt", "A3.txt")
# 查看当前目录下的子目录和文件
list.files()

# 创建一个有内容的文件 B.txt
cat("文件测试\n", file = "B.txt")
list.files()
```

* 查看文件

```
# 显示当前目录中的目录和文件

## 下面两句结果相同
list.files() # 建议使用该命令，方便记忆
dir()


## 参数full.names = TRUE，确定文件显示全名

## 参数recursive = TRUE，递归显示，即把目录下的目录和文件都以文件的形式显示
list.files(, full.names = TRUE, recursive = TRUE)

# 检查文件是否存在

## 存在的文件
file.exists("A.txt")

## 不存在的文件
file.exists("readme.txt")

# 判断是否是文件
file_test("-f", "A.txt")
```
<!--more-->

* 读取文件

```
# 文件A.txt读取
readLines("A.txt")

# 文件B.txt读取
readLines("B.txt")
```

* 文件重命名

```
# 给文件A.txt重命名为AA.txt
file.rename("A.txt","AA.txt")
list.files()
```

* 复制文件

```
file.copy("B.txt", "C.txt")
# 查看文件内容
readLines("C.txt")
```

* 追加文件

```
file.append("A.txt", rep("B.txt", 10))
# 查看文件内容
readLines("A.txt") 
```

* 删除文件

```
# 新建文件
file.create("tempa1", "tempa2", "tempa3", "tempa4")
list.files()
# 删除文件
file.remove("tempa1", "tempa2", "tempa3", "tempa4")
list.files()
```

* 文件权限

```
# 查看文件完整信息
file.info("A.txt")

# 查看文件访问权限
# 0表示有相关权限
# 1表示没有相关权限

## 是否存在
file.access("A.txt",0)

## 是否可执行
file.access("A.txt",1)

## 是否可写
file.access("A.txt",2)

## 是否可读
file.access("A.txt",4)

# 文件权限修改

## 查看文件信息
file.info("A.txt")

## 修改文件权限，创建者可读可写可执行，其他人无权限
Sys.chmod("A.txt", mode = "0400", use_umask = TRUE)

## 查看文件信息
file.info("A.txt")
```


## 目录操作

* 创建目录

```
# 新建一个目录
dir.create("test") # 不能同时创建多个目录
list.dirs()

# 递归创建
dir.create("test/test",recursive = TRUE)
list.dirs()
```

* 查看目录

```
# 查看当前默认目录的一级目录和文件
list.files() # 同时会显示目录下的文件
dir()

# 查看当前默认目录的子目录

## 默认递归显示各级子目录
list.dirs()

## 只显示一级子目录
list.dirs(recursive= FALSE)

# 通过系统命令查看目录结构
system("tree")

# 检查目录是否存在
# 存在的目录
file.exists(".")
file.exists("./test")

# 不存在的目录
file.exists("./test1")

# 判断是否是目录

## 下面两个语句结果相同
file_test("-d", "./test")
file_test("-d", "test")
```

* 目录重命名

```
# 查看目录
dir()

# 对test目录重命名
file.rename("test", "test1")

# 查看目录
dir()

# 目录重命名
file.rename("test1", "test")
```

* 删除目录

```
dir.create('temp')
dir.create('temp1/temp1', recursive = TRUE)
dir()

# 删除temp目录
unlink("temp", recursive = TRUE) # unlink也可用来删除文件，此时不需要设置参数recursive
dir()

unlink("temp1", recursive = TRUE) # 
dir()
```

* 目录的权限

```
# 查看目录完整信息
file.info(".")

# 检查目录的权限
df<-dir(full.names = TRUE)

# 检查文件或目录是否存在，mode=0
file.access(df, 0) == 0

# 检查文件或目录是否可执行，mode=1，目录为可以执行
file.access(df, 1) == 0

# 检查文件或目录是否可写，mode=2
file.access(df, 2) == 0

# 检查文件或目录是否可读，mode=4
file.access(df, 4) == 0

# 修改目录权限，所有用户只读
Sys.chmod("./test", mode = "0555", use_umask = TRUE)
file.info("./test")
```

## 其他功能函数

* 拼接目录字符串

```
file.path("p1","p2","p3")
```

* 所在目录

```
dirname("/home/conan/R/fs/readme.txt")
```

* 最底层子目录或文件名

```
basename("/home/conan/R/fs/readme.txt")
basename("/home/conan/R/fs")
```

* win下标准路径转化

```
normalizePath(c(R.home(), tempdir()))
```

* 短路径，缩减路径的显示长度，只在win中运行

```
shortPathName(c(R.home(), tempdir()))
```

## 小结
正如张丹所说，用R语言操作文件系统还是很方便的，但其对于函数命名不太规范,比如文件和目录的操作函数名有时区分，有时又不区分。最后，对文件和目录的操作进行简单的小结以方便自己的记忆。

| 操作名称 | 文件操作 | 目录操作 |
| :-------------: |:-------------:|: -----:|
| 创建 | file.create() | dir.create() |
| 查看 | list.files() | list.dirs() |
| 读取 | readLines() | - |
| 重命名 | file.rename() | file.rename() |
| 复制 |  file.copy() |  - |
| 追加 | file.append() | - |
| 删除 | file.remove() | unlink()| 
| 权限查看 | file.info() | file.info() |
| 权限查看 | file.access() | file.access() |
| 权限修改 | Sys.chomod() | Sys.chomod() | |
