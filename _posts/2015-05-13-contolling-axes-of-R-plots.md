---
layout: post
title: "精通R画图之坐标轴"
date: 2015-05-13 09:13:12
categories: 技术篇
tags: [R, 坐标轴]
---
## 1. 使用到的数据

```
set.seed(1)
n = 100
x = rnorm(n)
y = rnorm(n, x)
```

## 2. 相关函数介绍

### plot函数

plot函数非常常用，这里不再详述，主要提醒一下其中的几个参数axes，xaxt和yaxt。

* axes决定是否画坐标轴(包括坐标轴，刻度线和刻度线标签)。axes = F时，不画坐标轴。
* xaxt和yaxt决定是否画坐标轴的刻度线和刻度线标签。xaxt = 'n'时，不画坐标轴x的刻度线和刻度线标签；yaxt = 'n'时，不画坐标轴y的刻度线和刻度线标签。
具体的区别可以参见下面的程序和图形：

```
par(mfrow = c(2,2))
plot(x, y)
plot(x, y, xaxt = 'n')
plot(x, y, xaxt = 'n', yaxt = 'n')
plot(x, y, axes = F)
```
<!--more-->
![plot不同参数设置图](http://7xize0.com1.z0.glb.clouddn.com/plot.png)

### axis函数

axis函数用来添坐标轴(包括坐标轴，刻度线和刻度线标签)
```
axis(side, at = NULL, labels = TRUE, tick = TRUE, line = NA, pos = NA, outer = FALSE, font = NA, lty = "solid", lwd = 1, lwd.ticks = lwd, col = NULL, col.ticks = NULL, hadj = NA, padj = NA, ...)
```

**参数说明**

* side确定添加哪个轴，其中1为x轴，2为y轴，3为上面的轴，4为右面的轴。
* at确定坐标轴刻度线添加的位置。at也可以不赋值，此时，刻度线会根据默认的算法添加
* labels确定坐标轴刻度线标签的名称。当at确定后，若labels没有赋值，则会把at的值赋给刻度线标签
* tck确定刻度线的长短，其中负数为向下，整数向上，越大越长
* line可以水平移动坐标轴的位置，负的向正方向移动，正的向负方

### mtext函数

mtext()函数用来向图的四条边上添加文本。

**参数说明**

* text确定添加文本的内容
* side确定添加哪个轴，其中1为x轴，2为y轴，3为上面的轴，4为右面的轴。
* at赋值时， 在指定位置添加文本。at赋值时，在坐标中中央添加文本。
* line可以水平移动文本的位置，负的向正方向移动，正的向负方

## 3. 一个例子
这个例子解决了R的绘图坐标轴刻度线与刻度线标签距离太大的问题。解决方法是两次画坐标轴，第一次只显示坐标轴的坐标轴和刻度线，第二次只显示坐标刻度线的标签。

```
set.seed(1)
n = 100
x = rnorm(n)
y = rnorm(n, x)
par(mfrow = c(1,2))
plot(x,y, xlab = "Explanatory Variable", ylab = "Outcome Variable", las = 1)
abline(lm(y~x), col = "red", lwd = 2)

plot(x, y, axes = F, xlab = NA, ylab = NA) # xlab = NA时，不添加坐标轴名称。xlab = ‘’，添加坐标轴名称为‘’，也可达到这一目的。
abline(lm(y~x), col = "red", lwd = 2)
axis(side = 1, lwd = 1, tck = -.015, labels = NA) # 添加x轴的坐标轴(包括坐标轴，刻度线和刻度线标签)，但是却未显示刻度线标签
axis(side = 2, lwd = 1, tck = -.015, labels = NA) # 添加y轴的坐标轴(包括坐标轴，刻度线和刻度线标签)，但是却未显示刻度线标签
axis(side = 1, lwd = 0, line = -.4) # 第二次添加x轴的坐标轴(包括坐标轴，刻度线和刻度线标签)，但是只显示了刻度线标签(通过lwd = 0使坐标轴，刻度线未显示出来)
axis(side = 2, lwd = 0, line = -.4, las = 1) # 第二次添加y轴的坐标轴(包括坐标轴，刻度线和刻度线标签)，但是只显示了刻度线标签
mtext(side = 1, "Explanatory Variable", line = 2) # 添加x轴坐标轴名称
mtext(side = 2, "Outcome Variable", line = 3) # 添加x轴坐标轴名称
box()
```
![效果图](http://7xize0.com1.z0.glb.clouddn.com/plot2.png)
