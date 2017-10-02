---
layout: post
title: "REmap的作用"
date: 2017-10-02 16:31:23 +0800
categories: [技术篇]
tags: [R, REmap]
published: true
---

## 个性化地图

REmap中`get_theme`提供了迁徙地图中常用颜色的调整:

```
get_theme(theme = "Dark", lineColor = "Random",
  backgroundColor = "#1b1b1b", titleColor = "#fff",
  borderColor = "rgba(100,149,237,1)", regionColor = "#1b1b1b")
```

```
  theme
```


  默认主题,除了12个内置主题,可以使用“none”来自定义颜色

![background](http://xukuang.github.io/blog/images/background.png)

  - ​

  - a character object in (“Dark”,“Bright,”Sky“,”none“)

  - ​

- ​

- ```
  lineColor
  ```


  线条颜色,默认随机,也可以使用固定颜色

  ​

  - ​

  - Control the color of the line, “Random” for random color

  - ​

- ​

- ```
  backgroundColor
  ```


  背景颜色

  ​

  - ​

  - Control the color of the background

  - ​

- ​

- ```
  titleColor
  ```


  标题颜色

  ​

  - ​

  - Control the color of the title

  - ​

- ​

- ```
  borderColor
  ```


  边界颜色(省与省之间的信息)

  ​

  - ​

  - Control the color of the border

  - ​

- ​

- ```
  regionColor
  ```


  区域颜色

  ​

  - ​

  - Control the color of the region

  - ​

- ​

颜色可以使用颜色名(比如’red’,’skyblue’等),RGB(“#1b1b1b”,“#fff”)或者一个rgba的形式(“rgba(100,100,100,1)”),可以在[这里](http://www.114la.com/other/rgb.htm)找到颜色对照表.

- ​

- 默认模板: Bright

- ​

```
## default theme:"Bright"
set.seed(125)
out = remap(dat,title = "REmap实例数据",subtitle = "theme:Bright",
            theme = get_theme("Bright"))
plot(out)
```

- ​

- 更改线条颜色

- ​

```
## set Line color as ‘orange’
set.seed(125)
out = remap(dat,title = "REmap实例数据",subtitle = "theme:Bright",
            theme = get_theme("None",
                             lineColor = "orange"))
plot(out)
```

- ​

- 更改其他颜色

- ​

```
## Set Region Color
out = remap(dat,title = "REmap实例数据",subtitle = "theme:Bright",
            theme = get_theme("None",
                              lineColor = "orange",
                              backgroundColor = "#FFC1C1",
                              titleColor = "#1b1b1b",
                              regionColor = ‘#ADD8E6’))
plot(out)
```







##  获取经纬度

* `get_city_coord` 获取一个城市的经纬度
* `get_geo_position` 获取一个城市向量的经纬度



```
library(REmap)
city_vec = c("北京","Shanghai","广州")
get_city_coord("Shanghai")
	[1] 121.45950  31.21004
```

```
get_geo_position (city_vec)
              lon       lat     city
    1  121.459503 31.210037 Shanghai
    12 116.413554 39.911013     北京
    21 113.270793 23.135308     广州
```

## 绘制迁徙地图



绘制地图使用的是主函数`remap`

```
remap(mapdata, title = "", subtitle = "",
      theme =get_theme("Dark"))
```

- ​

- `mapdata` 一个数据框对象,第一列为出发地点,第二列为到达地点

- ​

- `title` 标题

- ​

- `subtitle` 副标题

- ​

- `theme` 控制生成地图的颜色,具体将会在`get_theme`部分说明

- ​

```
set.seed(125)
origin = rep("北京",10)
destination = c(‘上海’,’广州’,’大连’,’南宁’,’南昌’,
                ‘拉萨’,’长春’,’包头’,’重庆’,’常州’)
dat = data.frame(origin,destination)
out = remap(dat,title = "REmap实例数据",subtitle = "theme:Dark")
plot(out)
```

\- - - - - -