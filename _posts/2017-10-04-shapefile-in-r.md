---
layout: post
title: "地图数据shapfile文件在R中的读取.md"
date: 2017-10-04 16:18:02 +0800
categories: [技术篇]
tags: [R, 地图]
published: true
---
## Shapefile文件概述
Shapefile文件是美国环境系统研究所ESRI所研制的GIS文件系统格式文件，用来储存基于向量的地理数据（如点、线、面）。Shapefile文件指是由多个文件组成的。其中，要组成一个Shapefile，有三个文件是必不可少的，它们分别是".shp", ".shx"与 ".dbf"文件。表示同一数据的一组文件其文件名前缀应该相同。例如，存储一个关于湖的几何与属性数据，就必须有lake.shp，lake.shx与lake.dbf三个文件。而其中“真正”的Shapefile的后缀为shp，然而仅有这个文件数据是不完整的，必须要把其他两个附带上才能构成一组完整的地理数据。除了这三个必须的文件以外，还有八个可选的文件，使用它们可以增强空间数据的表达能力。
必须的文件:
* .shp — 图形格式，用于保存元素的几何实体。
* .shx — 图形索引格式。几何体位置索引，记录每一个几何体在shp文件之中的位置，能够加快向前或向后搜索一个几何体的效率。
* .dbf — 属性数据格式，以dBase IV的数据表格式存储每个几何形状的属性数据。

在每个.shp, .shx与.dbf文件之中，图形在每个文件的排序是一致的。也就是说，.shp的第一条记录与.shx及.dbf之中的第一条记录相对应，如此类推。此外，在.shp与.shx之中，有许多字段的字节序是不一样的。因此用户在编写读取这些文件格式的程序时，必须十分小心地处理不同文件的不同字节序。

## Shapefile文件读取
R有多个包可以读取shapfile文件并作地图，这里以中国各省及直辖市地图bou2_4p为例，介绍几种用来读取shapefile文件的方法。其中前两种方法要依赖sp包，它定义了一个spatial类型的集合，事实上是R里边标准的空间数据格式。

### 方法一：maptools包 
maptools包提供了多个函数用来对R当中的空间对象进行读、写、转换或者其他的处理。用来读shapefile的通用函数是readShapeSpatial，这个函数可以自动识别shapefile（或其他R对象）中是否包含点、线或面，然后对特殊的类型采用专门的函数把数据读入。这些专门的函数包括readShapePoints，readShapeLines，readShapePoly等，用来分别读点、线、面。这样做的好处是当你搞错类型时，它会报错。和下边rgdal包的对应方法不同的是，maptools的函数并不读入投影信息，而是留给你手工处理（如果需要的话）。

```
library(maptools) 
china_map<-readShapePoly("bou2_4p.shp") 
plot(china_map, axes=F, border="gray") 


class(china_map) 

	[1] "SpatialPolygonsDataFrame" 
```

readShapePoly()函数虽然读取的是bou2_4p.shp文件，但在默认情况下会把dbf文件的信息也放到变量china_map之中，因此.shx 和 .dbf文件也必须在同一个文件目录下才能读取成功。

这时一张完整的中国地图就已经画好了。但是在实际使用的过程中，往往需要根据情况对地图中的某些省份着以特定的颜色，这时就可以通过调节plot命令中的col参数来予以实现。然而为了清楚地说明这部分的内容，需要插播一段R绘制地图的原理。

====================== 传说中的分割线 =====================

在绘制地图时，每一个省市自治区或者岛屿都是用一个多边形来表示的。之前的 GIS数据，其实就是提供了每一个行政区其多边形逐点的坐标，然后R软件通过顺次连接这些坐标，就绘制出了一个多边形区域。在上面的数据中，一共包含了925个多边形的信息，之所以有这么多是因为一些省份有很多小的附属岛屿。在这925个多边形中，每一个都对应一个唯一的ID，编号分别从1到925。

```
china_map@data
```

在R中输入china_map@data，会得到一个925行7列的数据框，这其实是bou2_4p.dbf这个文件中存储的信息。对于这个数据框，其行名就是每一个区域的ID编号，第一列和第二列分别是面积和周长，最后一列是该区域所属的行政区名，其它的列应该也是一些编号性质的变量，其中，ADCODE99是国家基础地理信息中心定义的区域代码，共有 6 位数字，由省、地市、县各两位代码组成。这个数据少了澳门特别行政区，这是这个数据中的一块瑕疵。第899行有一个NA，不知道它代表的是否就是澳门。

====================== 传说中的分割线 =====================

回到刚才的话题，plot命令中的col参数在本例中应该是一个长度为925的向量，其第i个分量的取值就代表了地图中第i个多边形的颜色。一个简单的尝试是运行下面这个命令看看效果：

```
plot(china_map,col = gray(924:0/924));
```

于是自然就产生了一个问题：如何获取某一个特定地区的ID，进而设置我们想要的颜色？事实上，可以通过查找相应的行政区对应的行名，就可以对col参数进行赋值了。

```
china_map@data
```


### 方法二：rgdal包 
rgdal包提供了GDAL/OGR library（The Geospatial Data Abstraction Library）的接口。rgdal包里边的函数readOGR可以读包括shapefile在内的多种基于向量的数据格式。ogrInfo函数可以在不读全部数据集的情况下检索数据的细节，看一下数据的情况： 
```
library(rgdal)

```
读数据： 
```
china_map1<- readOGR(".", "bou2_4p") 
  # OGR data source with driver: ESRI Shapefile 
  # Source: ".", layer: "bou2_4p" 
  # with 925 features and 7 fields 
  # Feature type: wkbPolygon with 2 dimensions 

 plot(china_map1, axes=F, border="gray") 

 china_map1@data
```

### 方法三：PBSmapping包 
PBSmapping包只能读不能写shapefile数据。这个包采用自定义的空间数据类型，因此不能利用许多基于sp包开发的R包。 
```
library(PBSmapping)
china_map2 <- importShapefile("bou2_4p",readDBF=FALSE) 
str(china_map2)#看数据集的属性 
plotPolys(china_map2) 
```

### 方法四： shapefiles包 
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


