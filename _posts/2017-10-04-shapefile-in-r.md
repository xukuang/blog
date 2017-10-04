---
layout: post
title: "地图数据shapfile文件在R中的读取.md"
date: 2017-10-04 16:18:02 +0800
categories: [技术篇]
tags: [R, 地图]
published: true
---
Shapefile文件是美国环境系统研究所ESRI所研制的GIS文件系统格式文件，用来储存基于向量的地理数据（如点、线、面）。关于shapefile文件的详细介绍在网上有很多。

R有多个包可以读取shapfile文件并作地图，下面介绍几种用来读取shapefile文件的方法。其中前两种方法要依赖sp包，它定义了一个spatial类型的集合，事实上是R里边标准的空间数据格式。

## 方法一：maptools包 
maptools包提供了多个函数用来对R当中的空间对象进行读、写、转换或者其他的处理。用来读shapefile的通用函数是readShapeSpatial，这个函数可以自动识别shapefile（或其他R对象）中是否包含点、线或面，然后对特殊的类型采用专门的函数把数据读入。这些专门的函数包括readShapePoints，readShapeLines，readShapePoly等，用来分别读点、线、面。这样做的好处是当你搞错类型时，它会报错。和下边rgdal包的对应方法不同的是，maptools的函数并不读入投影信息，而是留给你手工处理（如果需要的话）。

```
library(maptools) 
china_map<-readShapePoly("bou2_4p.shp") 
plot(china_map, axes=TRUE, border="gray") 


class(china_map1) 

	[1] "SpatialPolygonsDataFrame" 
```

readShapePoly()函数虽然读取的是bou2_4p.shp文件，但在默认情况下会把dbf文件的信息也放到变量china_map之中。在R中输入china_map@data，会得到一个925行7列的数据框，这其实是bou2_4p.dbf这个文件中存储的信息。对于这个数据框，其行名就是每一个区域的ID编号，第一列和第二列分别是面积和周长，最后一列是该区域所属的行政区名，其它的列应该也是一些编号性质的变量。

```
china_map@data
```


## 方法二：rgdal包 
rgdal包提供了GDAL/OGR library（The Geospatial Data Abstraction Library）的接口。rgdal包里边的函数readOGR可以读包括shapefile在内的多种基于向量的数据格式。ogrInfo函数可以在不读全部数据集的情况下检索数据的细节，看一下数据的情况： 
```
library(rgdal)
  # ogrInfo(".", "bou2_4p") 
  # Source: ".", layer: "bou2_4p" 
  # Driver: ESRI Shapefile number of rows 925 
  # Feature type: wkbPolygon with 2 dimensions 
  # LDID: 0 
  # Number of fields: 7 
          # name type length typeName 
  # 1 AREA 2 12 Real 
  # 2 PERIMETER 2 12 Real 
  # 3 BOU2_4M_ 2 11 Real 
  # 4 BOU2_4M_ID 2 11 Real 
  # 5 ADCODE93 0 6 Integer 
  # 6 ADCODE99 0 6 Integer 
  # 7 NAME 4 34 String 
```
读数据： 
```
china_map1<- readOGR(".", "bou2_4p") 
  # OGR data source with driver: ESRI Shapefile 
  # Source: ".", layer: "bou2_4p" 
  # with 925 features and 7 fields 
  # Feature type: wkbPolygon with 2 dimensions 
print(proj4string(china_map1)) 
  # [1] NA 
 plot(china_map1, axes=TRUE, border="gray") 
```
## 方法三：PBSmapping包 
PBSmapping包只能读不能写shapefile数据。这个包采用自定义的空间数据类型，因此不能利用许多基于sp包开发的R包。 
```
library(PBSmapping)
china_map2 <- importShapefile("bou2_4p",readDBF=FALSE) 
str(china_map2)#看数据集的属性 
plotPolys(china_map2) 
```
## 方法四： shapefiles包 
这个包是专门用来读写ESRIhapefile数据文件的。read.shapefile函数可以读入完整的shapefile文件（包括.shp, .shx和.dbf），但是，它读进来之后的数据格式是列表的列表，对于ggplot2作图还是麻烦的很。画图时需要先把数据格式进行转化。 
```
library(shapefiles)
china_map3 <- read.shapefile("bou2_4p") 
```
shapefile文件的处理还是有一点麻烦的。对于非空间数据的研究应用人士来说，画个地图也不一定要这么大费周章。因为R还有好几个利用其他地图数据来源绘制地图的工具。 
下一篇，介绍ggmap包。 


\- - - - - -
本篇几个包的文档: 
  
shapefile: http://cran.r-project.org/web/packages/shapefiles/shapefiles.pdf
maptools: http://cran.r-project.org/web/packages/maptools/maptools.pdf
rgdal: http://cran.r-project.org/web/packages/rgdal/rgdal.pdf
PBSmapping: http://cran.r-project.org/web/packages/PBSmapping/PBSmapping.pdf

在网上找shapefile文件并不麻烦，世界各国的行政区划shapefile文件：http://gadm.org/


