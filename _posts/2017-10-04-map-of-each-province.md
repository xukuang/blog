---
layout: post
title: "基于Emaps包的中国省市级地图"
date: 2017-10-04 20:18:05 +0800
categories: [技术篇]
tags: [R, Emap, 地图]
published: true
---
## 省级指标图


```
library(REmap)
head(chinaIphone)
chinaIphone
remapC(chinaIphone)
```

## 市级指标图

```
mapNames("河南")
set.seed(123)
henan = data.frame(country = mapNames("河南"), value = sample(17))
remapC(henan, maptype = "河南",color = "skyblue")
```

## 观测点图

```
park = read.csv('park_result.csv')
geodata = data.frame(park$lon, park$lat, park$park)

pointData = data.frame(park$park, color = 'red')


remapC(henan, maptype = "河南",color = "skyblue",
       markPointData = pointData,
       markPointTheme = markPointControl(symbol = "pin",
                                         symbolSize = 5,
                                         effect = F),
       geoData = geodata)
```

\- - - - - -
