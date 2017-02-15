---
layout: post
title: "JavaScript初步介绍"
date: 2017-02-04 18:00:36 +0800
categories: [技术篇]
tags: [html]
---
<script type="text/javascript">
	if("12345678"==prompt("请输入密码"))
	{
		alert("正确");
	}
	else
	{
		alert("错误");
		location="http://blog.laphets.com"; #返回网站，请自定义
	}
</script>
JavaScript一种脚本编程语言，用于决定如何动态改变HTML元素。

## HTML文件使用JavaScript的方式

* 在HTML中使用JavaScript

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>

<body>
</body>
<!--插入JavaScript代码-->
<script type="text/javascript">
console.log('Hello Wold')
</script>

</html>
```
* 引入外部JavaScript文件

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <!--引入外部JavaScript文件-->
    <style type="text/css">out.js</style>
</head>

<body>
</body>
</html>
```

## 常用内容
* document.getElementById("demo")可返回对拥有指定 ID 的第一个对象的引用
* var carName = "Volvo"创建一个变量，变量名为 carName， 值为 "Volvo" :
* HTMLElementObject.innerHTML=text 设置或返回开始标签和结束标签之间的 HTML。
* console.log打印信息
* 运算符，条件和循环语句
* 用两个斜杠表示注释