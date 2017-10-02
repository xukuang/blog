---
layout: post
title: "REmap的作用"
date: 2017-10-02 16:31:23 +0800
categories: [技术篇]
tags: [R, REmap]
published: true
---

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

## 分级统计图
remapC是用于创建分级统计图(Choropleth map)。.即根据子区域数值的多少进行深浅不同的颜色填充的地图形式。目前支持的地图为：‘china’ 中国省份地图，‘world’ 世界地图，各省市地图，如’广东’,’西藏’等。
函数的调用形式为:
```
remapC(data,
       maptype = ‘china’,
      color = c(‘#1e90ff’,’#f0ffff’),
      theme = get_theme("Bright"),
      title = "",
      subtitle = "",
      mindata = NA,
      maxdata = NA,
      # mark Line & point
      markLineData = NA,
      markPointData = NA,
      markLineTheme = markLineControl(),
      markPointTheme = markPointControl(),
      geoData = NA)
```
参数看起来很多，这里仅描述前几个参数，后面的markLine与markPoint是用于在绘制好的地图上添加标线和标点的，会在remapB中详细介绍。remapC中重要的参数有：

* data：数据框，第一列为子区域名(比如全国地图的省名，省级地图的市名)。
* color：传入单个颜色就使用从白色到该色的填充，多个颜色根据值大小计算填充颜色。
* maptype：地图的格式，’china’代表中地图，’world’代表世界地图。

```
head(chinaIphone)
remapC(chinaIphone)
remapC(chinaIphone,
      color = 'orange')
## 颜色改为白色到橘红色
remapC(chinaIphone,
      color = c('orange','red'))
```
此外，使用mapType参数可以改变地图的类型，绘制子地图或者世界地图。

### 世界地图
```
data = data.frame(country = mapNames("world"),
                   value = sample(178)+200)
head(data)
remapC(data,maptype = "world",color = 'skyblue')
```
### 子地图
mapNames()函数可以得到某个地图下的子图信息。
```
mapNames(‘西藏’)

data = data.frame(country = mapNames('西藏'),
                   value = sample(7)+200)
head(data)
remapC(data,maptype = '西藏',color = 'skyblue')
```
### 地图精细

```
remapC(chinaIphone,
        title = "remapC实例地图",
        theme = get_theme("none",backgroundColor = "#fff",
                          titleColor = "#1b1b1b",
                          pointShow = T),
        max = 2000)
```

## 底图的呈现

remapB是用于创建一个以百度地图为底图的recharts效果,有以下特点:
1. 支持滚轮缩放,拖拽地图
2. 详细的地图效果
3. 可视化主要以标线与标点的形式做出

remapB的使用

函数的调用形式为:

```
remapB(center = c(104.114129,37.550339),
       zoom = 5,
       color = "Bright",
       title = "",
       subtitle = "",
       # mark Line & point
       markLineData = NA,
       markPointData = NA,
       markLineTheme = markLineControl(),
       markPointTheme = markPointControl(),
       geoData = NA)
```

* center：地图的中心(经纬度坐标)，可以从get_city_coord获得
* zoom：地图缩放尺寸越小地图越大，(5代表国家级的地图，15代表市级的地图)
* color：地图的颜色风格，默认颜色风格，除了12个内置颜色风格，可以使用“none”来自定义颜色。
  ![background](http://xukuang.github.io/blog/images/background.png)

```
library(devtools)
install_github('lchiffon/REmap')
library(REmap)
```
## 没有标点和标线的Bmap效果
```
remapB()
remapB(color = "Blue")
remapB(color = "pink")

remapB(get_city_coord("北京"),zoom = 12)
```
## 标线
markLine是Echarts中进行标线的工具。通过标线(直线，曲线)，可以完成很多有意思的可视化。
* markLineData 标线使用的数据，第一列为出发地，第二列为目的地
* markLineTheme 控制标线颜色，形状等，由markLineControl来控制
* geoData 标中各个点的经纬度坐标。如果没有，会使用BaiduAPI自动查找

markLine的这些参数需结合remapC或者remapB使用。
### 标线数据
markLineData 标线使用的数据，第一列为出发地，第二列为目的地

```
remapB(title = "Bmap迁徙图示例",
        color = "Blue",
    	markLineData = demoC)
```

### 标线风格
markLineTheme控制了标线的风格，使用markLineControl来调用。这里列出主要的参数:

```
markLineControl(symbolSize = c(2,4),
      smoothness = 0.2,
      effect = T,
      lineWidth = 1,
      lineType = ‘solid’,
      color = "Random") 
```

* symbolSize：形状的大小，标线默认是一端无形状,一端箭头，如果不想要箭头可以使symbolSize = c(0,0)。
* smoothness：曲线的弯曲度。取0标线会退化为直线。
* effect：炫光特效，标线较多的时候建议关闭。
* lineWidth：标线的宽度。
* lineType：标线的样式： ’solid’实线，’dotted’点线， ’dashed’虚线。
* color：颜色,默认为随机颜色，设置一个颜色会取为固定颜色。

```
remapB(title = "Remap:  百度迁徙模拟",
       color = "Blue",
       markLineData = demoC,
       markLineTheme = markLineControl(symbolSize = c(0,0),
                                       lineWidth = 10,
                                       lineType = ‘dashed’))
```

### 经纬度坐标
geoData 标中各个点的经纬度坐标。如果没有，会使用BaiduAPI自动查找。若会使用BaiduAPI查找不到，必须提供经纬度坐标。mapC和mapB中，都会有geoData这个变量，用以储存markLine和markPoint的地理位置信息。具体的格式与get_city_coord返回相同。第一列为lon，第二列为lat，第三列为地理名称。

```
get_geo_position(c("Beijing","Shanghai","Guangzhou"))

       lon      lat      city
1 116.4232 39.91528   Beijing
2 121.5221 31.30477  Shanghai
3 113.2684 23.12980 Guangzhou
```

## 标点
markLine是Echarts中进行标线的工具。通过标线(直线，曲线)，可以完成很多有意思的可视化。
markPoint是Echarts中进行标点的工具。通过不同形状的点(箭头，星，圆或者自定义的图片)来完成点的标注。
* markPointData 标点使用的数据，可以是一个向量，如果是数据框就仅使用第一列。
* markPointTheme 控制标点颜色，形状等，由markPointControl来控制
* geoData 标中各个点的经纬度坐标,如果没有,会使用BaiduAPI自动查找。

与markLine类似，markLine的这些参数需结合remapC或者remapB使用。



### 标点数据
markPointData 标点使用的数据，可以是一个向量，如果是数据框就仅使用第一列。

```
remapB(title = "Remap:  百度迁徙模拟",
       color = "Blue",
       markPointData = demoC[,2])
```

### 标点风格
markPointTheme控制了标点的风格，使用markPointControl来调用。这里列出主要的参数:

```
markPointControl(symbol = ‘emptyCircle’,
                  symbolSize = "Random",
                  effect = T,
                  effectType = ‘scale’,
                  color = "Random")
```

* symbol。‘circle’,‘emptyCircle’圆,空心圆。‘rectangle’,‘emptyRectangle’,方块,空心方块。‘triangle’,‘emptyTriangle’,三角,空心三角。‘diamond’,‘emptyDiamond’,钻石,空心钻石。‘heart’心形,’droplet’,水滴。‘pin’,POI标注,’arrow’箭头, ’star’五角星。或者使用’image://http://….’来引用一个图片。
* symbolSize：标点的大小。
* effect：炫光特效,标线较多的时候建议关闭。
* effectType：炫光特效的方式，’scale’放大，’bounce’跳动。
* color：颜色,默认为随机颜色，设置一个颜色会取为固定颜色。

### 经纬度坐标
geoData 标中各个点的经纬度坐标。如果没有，会使用BaiduAPI自动查找。若会使用BaiduAPI查找不到，必须提供经纬度坐标。mapC和mapB中，都会有geoData这个变量，用以储存markLine和markPoint的地理位置信息。具体的格式与get_city_coord返回相同。第一列为lon，第二列为lat，第三列为地理名称。

## 绘制迁徙地图
绘制地图使用的是主函数remap。
```
remap(mapdata, title = "", subtitle = "",
      theme =get_theme("Dark"))
```

* mapdata： 一个数据框对象，第一列为出发地点，第二列为到达地点。
* title：标题。
* subtitle：副标题。
* theme：控制生成地图的颜色，具体将会在get_theme部分说明。


```
library(REmap)
set.seed(125)
origin = rep("北京",10)
destination = c("上海","广州","大连","南宁","南昌","拉萨","长春","包头","重庆","常州")
dat = data.frame(origin,destination)
out = remap(dat,title = "REmap实例数据",subtitle = "theme:Dark")
plot(out)
```
### 个性化地图

REmap中get_theme提供了地图中常用颜色的调整:
```
get_theme(theme = "Dark", lineColor = "Random",
  backgroundColor = "#1b1b1b", titleColor = "#fff",
  borderColor = "rgba(100,149,237,1)", regionColor = "#1b1b1b")
```

* theme：默认颜色主题，除了12个内置颜色主题，可以使用“none”来自定义主题。
* lineColor： 线条颜色,默认随机，也可以使用固定颜色。
* backgroundColor： 背景颜色。
* titleColor： 标题颜色。
* borderColor：边界颜色(省与省之间的信息)。
* regionColor： 区域颜色。

```
## 默认模板: Bright
## default theme:"Bright"
set.seed(125)
out = remap(dat,title = "REmap实例数据",subtitle = "theme:Bright",
            theme = get_theme("Bright"))
plot(out)
```


```
out = remap(dat,title = "REmap实例数据",subtitle = "theme:Bright",
            theme = get_theme("None",
                              lineColor = "orange",
                              backgroundColor = "#FFC1C1",
                              titleColor = "#1b1b1b",
                              regionColor = "#ADD8E6"))
plot(out)
```


\- - - - - -
```

```

```