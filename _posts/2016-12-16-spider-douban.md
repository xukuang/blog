---
layout: post
title: "爬取豆瓣电影数据"
date: 2016-12-16 20:42:53 +0800
categories: [跟着宏伦学习全栈工程师]
tags: [R]
published: ture
---

## 目的

使用R爬取豆瓣电影数据。

##  平台

所有代码在windows7系统上运行。

## 实现

这里只是简单的在R中实现了张宏伦课程中爬取豆瓣电影的例子，具体的原理还要参考他的课程。

```
# 加载需要的R包
library(rvest)
library(rjson)
library(stringr)
library(dplyr)

# 读取数据
url = 'https://movie.douban.com/j/search_tags?type=movie'
web <- read_html(url,encoding="UTF-8")

# 找出电影的不同类型
tags <- web %>% html_nodes('p') %>% html_text() %>% fromJSON()
tag = tags$tags

basicurl<-"https://movie.douban.com/j/search_subjects?type=movie&tag="

# 爬取不同类型电影函数
WebSpider = function(m){
  i = 0
  # 存放每个类型电影的结果
  result = data.frame()
  while(1){
    #Sys.sleep(runif(1,1,2)) # 限制两次爬虫运行的时间
    url = str_c(basicurl,m,'&sort=recommend&page_limit=20&page_start=', i)
    
    web<-read_html(url,encoding="UTF-8")
    
    # 提取不同页的电影数据，并把数据格式由json转化list
    movies.inf = web %>% html_nodes("p") %>% html_text() %>% fromJSON()
    
    # 转化为数据格式为数据框
    temp = do.call("rbind",movies.inf$subjects)
    
    # 合并每个类型所有页的电影数据
    result = rbind(result, temp)
    if(is.null(temp)){
      print(i);break} else{
        i = i + 1} 
  }
  
  # 在数据中添加电影分类
  result$tags = m
  # 合并不同类型的电影数据
  return(result)
}

# 存放所有类型的电影
movies <- data.frame()

# 爬取所有类型的电影
for(j in 1:length(tag)){
  movies <- rbind(movies, WebSpider(tag[j]))}

# 转化列表中的每一项为字符型
movies.df = lapply(movies, as.character)

# 去除重复的电影
movies.unique = movies.df %>% tbl_df() %>% distinct(id, .keep_all = T)

# 写出数据
write.csv(movies.unique, file = 'data/douban_movies_unique_R.csv', row.names = F)
```

这个爬虫非常的简单，如同张宏伦课程中的那样，我们并没有进行浏览器的伪装。尽管我们在函数中加了爬虫的时间限制，但是在实际运行中，考虑到程序运行的时间，我们对这一行代码进行了注释。我的代码在12月14号运行的，大概用了半个小时。爬取的结果重复记录有很多，总共有97931行(包含表头)，去除重复后2572行。如同，张宏伦说的那样，这种爬取方法爬到的数据还是相对有限的。我把爬下来的有重复的数据放在了百度云盘上，要是大家想看看，可以点击[这里](https://pan.baidu.com/s/1bp45nlX)下载。

## 后话
以前，看过一些用R爬取网页的例子，由于对爬虫的原理不很清楚，总是不明白如何限定爬虫的节点。看了张宏伦课程爬取豆瓣电影的这一节，对爬虫依靠依靠网页的节点来实现爬取网页内容有了更深入的了解。结合以前看的内容，以为会很轻松的通过R实现。可是，在具体的实现时，发现还是遇到了一系列的坑。其中，最大的坑是如何在R中把json数据转化为R的常用数据。在解决这个问题的时候，又搜索的了陈堰平老师的文章，感觉陈老师在R的世界也是无处不在呀。

\- - - - - -

**参考文章**

1.[爬取豆瓣电影数据](http://note.youdao.com/share/?id=2155cf875395e84d92ef80baeae7c3c0&type=notebook#/WEB30cbd9a1a96a1d3af9c4397cb7fb3afe)

2.[R和JSON的傻瓜式编程](http://blog.fens.me/r-json-rjson/)

3.[用R获取flash中的数据](http://yanping.me/cn/blog/2013/02/10/get-data-from-flash/)

   ​