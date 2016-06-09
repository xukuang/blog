---
layout: post
title: "css���Ʊ���ͼƬ"
date: 2016-06-09 22:11:08
categories: [����ƪ]
tags: [html]
---
����(background)��css��һ����Ҫ�ĵĲ��֣�Ҳ����Ҫ֪����css�Ļ���֪ʶ֮һ����ƪ���½����漰css����(background)�Ļ����÷����������� background-attachment �ȵ����ԣ�Ҳ�����һЩ�йر���(background)�ĳ��ü��ɣ��Լ� css3 �е� ����(background)(����4���µı���(background)����)��

## ���ñ�����ɫ(background-color)

* ʮ������ background-color:#ff0000;
* Ӣ������ background-color:red;
* ��ԭɫ background-color:rgb(255,0,0);
* ͸�� background-color:transparent;

## ���ñ���ͼƬ

1. ���뱳��ͼƬ(background-image)
background-image:url("bg.jpg");


2. ����ͼƬ��ƽ�̷�ʽ(background-repeat)
	* ������ͼ�������������� background-repeat:repeat;
	* ������ͼ����ˮƽ�������� repeat-x;
	* ������ͼ���ڴ�ֱ�������� repeat-y;
	* ͼ��ֻ����һ��: no-repeat;

3. ����ͼƬλ��(background-position)
	* ���ñ���ͼ������������: background-position: top left;
	* ���ñ���ͼ���������Ϸ��м� background-position: top center;
	* ���ñ���ͼ�����������ҷ� background-position: top right;
	* ���ñ���ͼ���������м��� background-position: center left;
	* ���ñ���ͼ��������IE�м� background-position: center center;
	* ���ñ���ͼ�������м��ҷ� background-position: center right;
	* ���ñ���ͼ������������ background-position: bottom left;
	* ���ñ���ͼ�����������·� background-position: bottom;
	* ���ñ���ͼ�����������ҷ� background-position: bottom right;

���⣬Ҳ����ͨ������׼ȷ�Ķ�λͼƬ��λ��(x,y) xˮƽ�������λ�� y��ֱ�����λ�ã���λ������(px)��

```
background-position: 0 0; 
background-position: 75px 0;
background-position: -75px 0;
background-position: 0 100px;
```
4. ��������(background-attachment)
���ñ���ͼ���Ƿ�����Ӵ�������������

* ���ñ���ͼ������Ӵ����������ƶ����ƶ�(Ĭ��) background-attachment: scroll
* ���ñ���ͼ�񲻻����Ӵ����������ƶ����ƶ� background-attachment: fixed 


## �����ļ�д����
���԰ѱ����ĸ������Ժ�Ϊһ�У�������ÿ�ζ�����������д������

���磬���������:

```
background-color: transparent;
background-image: url(image.jpg);
background-position: 50% 0 ;
background-attachment: scroll;
background-repeat: repeat-y;
```
���Ժ�Ϊ����һ�У�

```
background: transparent url(image.jpg) 50% 0 scroll repeat-y;
```
���Ҳ���Ҫָ��ÿһ��ֵ�����ʡ��ֵ�ػ�����ʹ�����Ե�Ĭ��ֵ�����磬�������к��������Ч��һ����

```
background: url(image.jpg) 50% 0 repeat-y;
```

\- - - - - -
**�ο�����**

1. [css���Ʊ���ʾ��(css���ñ���ͼƬ�����ñ�����ɫ)](http://www.jb51.net/css/154155.html)
2. [http://blog.csdn.net/adenfeng/article/details/8199362/](http://blog.csdn.net/adenfeng/article/details/8199362/)
