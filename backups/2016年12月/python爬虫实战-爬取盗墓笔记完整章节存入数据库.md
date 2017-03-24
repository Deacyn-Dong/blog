## 前言

上次说到做了一个小说阅读器，那内容从哪儿来呢？只能是爬来的了。

## 要点

* 爬虫数据存储(解决上次思考题)
* 正则表达式的使用
* 浏览器的简单伪装
* 面向对象的类的初步使用

## 准备工作

* python正则表达式的使用

[Python正则表达式-菜鸟教程][1]

* MySQL的配置

![MySQL的配置][5]

创建一个“novel”数据库，其中再创一个“dbmj”的数据表，其中设置一个自增的int型id，一个varchar10的title，一个varchar1000的content（正文内容较多，可以设大点）

* 头部写入

<pre lang="python">
#coding: utf-8
import requests
import re
import MySQLdb
</pre>

*requests和MySQLdb模块需要安装，推荐使用pip命令*

## 开始

### 1.爬取准备工作 

#### 1.1待爬取的url 

<pre lang="python">
novel_url = "http://www.quanshu.net/book/9/9055"
</pre>

#### 1.2浏览器的伪装

<pre lang="python">
headers = {
	"User-Agent":"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36",
	"Host":"www.quanshu.net",
	"Connection":"keep-alive"
}
</pre>

### 2.爬取链接

####2.1逐步讲解

<pre lang="python">
	response = requests.get(novel_url,headers=headers)
</pre>

用requests获取页面信息，并加入了头部伪装

<pre lang="python">
	reg = re.compile(r'<li><a href="(.*?)" title=".*?">(.*?)</a></li>')
</pre>

![][2]

分析html结构，发现只有href，title和标签文本节点不同,使用正则匹配

>.*具有贪婪的性质，首先匹配到不能匹配为止，根据后面的正则表达式，会进行回溯

>.*？则相反，一个匹配以后，就往下进行，所以不会进行回溯，具有最小匹配的性质。

>(.?)设置分组

<pre lang="python">
	urllist = re.findall(reg,response.text)
</pre>

使用findall方法查找，并将匹配的结果存入urllist列表中

<pre lang="python">
	print urllist
</pre>

我们打印看下结果

![][3]

发现其中奇怪的`\x`结构，这是anscii的编码，我们要的应该是Unicode字符

我们打印下Requests推测使用的编码格式

<pre lang="python">
print response.encoding
</pre>

![][6]

#### 2.2封装函数

<pre lang="python">
def get_list(novel_url):
	response = requests.get(novel_url,headers=headers)
	reg = re.compile(r'<li><a href="(.*?)" title=".*?">(.*?)</a></li>')
	urllist = re.findall(reg,response.text)
	return urllist	
</pre>

![][4]

[1]:http://www.runoob.com/python/python-reg-expressions.html
[2]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-11.png
[3]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-2.png
[4]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-5.png
[5]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-10.png
[6]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-5.png
