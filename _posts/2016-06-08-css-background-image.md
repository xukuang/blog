---
layout: post
title: "css控制背景图片"
date: 2016-06-08 22:11:08
categories: [技术篇]
tags: [html]
---
背景(background)是css中一个重要的的部分，也是需要知道的css的基础知识之一。这篇文章将会涉及css背景(background)的基本用法，包括诸如 background-attachment 等的属性，也会介绍一些有关背景(background)的常用技巧，以及 css3 中的 背景(background)(包含4个新的背景(background)属性)。

## 设置背景颜色(background-color)

* 十六进制 background-color:#ff0000;
* 英文名称 background-color:red;
* 三原色 background-color:rgb(255,0,0);
* 透明 background-color:transparent;

## 设置背景图片

1. 引入背景图片(background-image)
background-image:url("bg.jpg");


2. 背景图片的平铺方式(background-repeat)
	* 将背景图案填满整个背景 background-repeat:repeat;
	* 将背景图案在水平方向添满 repeat-x;
	* 将背景图案在垂直方向添满 repeat-y;
	* 图案只出现一次: no-repeat;

3. 背景图片位置(background-position)
	* 设置背景图案出现在上左方: background-position: top left;
	* 设置背景图案出现在上方中间 background-position: top center;
	* 设置背景图案出现在上右方 background-position: top right;
	* 设置背景图案出现在中间左方 background-position: center left;
	* 设置背景图案出现在中间 background-position: center center;
	* 设置背景图案出现中间右方 background-position: center right;
	* 设置背景图案出现在下左方 background-position: bottom left;
	* 设置背景图案出现在正下方 background-position: bottom;
	* 设置背景图案出现在下右方 background-position: bottom right;

	此外，也可以通过坐标准确的定位图片的位置(x,y) x水平方方向的位置 y垂直方向的位置，单位是像素(px)。例如，"background-position: 0 0;"。 

4. 背景附着(background-attachment)

	设置背景图像是否会随视窗滚动而滚动。
	
	* 设置背景图像会随视窗滚动条的移动而移动(默认) background-attachment: scroll
	* 设置背景图像不会随视窗滚动条的移动而移动 background-attachment: fixed 


## 背景的简写属性
可以把背景的各个属性合为一行，而不用每次都单独把他们写出来。

例如，下面的声明:

```
background-color: transparent;
background-image: url(image.jpg);
background-position: 50% 0 ;
background-attachment: scroll;
background-repeat: repeat-y;
```
可以合为单独一行：

```
background: transparent url(image.jpg) 50% 0 scroll repeat-y;
```
而且不需要指定每一个值。如果省略值地话，就使用属性地默认值。例如，上面那行和下面这个效果一样：

```
background: url(image.jpg) 50% 0 repeat-y;
```

\- - - - - -

**参考资料**

1. [css控制背景示例(css设置背景图片、设置背景颜色)](http://www.jb51.net/css/154155.html)
2. [http://blog.csdn.net/adenfeng/article/details/8199362/](http://blog.csdn.net/adenfeng/article/details/8199362/)
