# 前言
上次说到做了一个小说阅读器，那内容从哪儿来呢？只能是爬来的了。

# 要点
* 爬虫数据存储(解决上次思考题)
* 正则表达式的使用
* 浏览器的简单伪装

# 准备工作
* python正则表达式的使用

[Python正则表达式-菜鸟教程][1]

* MySQL的基本常识

* 头部写入
<pre lang="python">
<!-- #coding: utf-8 -->
import requests
import re
import MySQLdb
</pre>

# 开始

** 1.爬取准备工作 **

** 1.1待爬取的url **
<pre lang="python">
novel_url = "http://www.quanshu.net/book/9/9055"
</pre>
** 1.2浏览器的伪装 **
<pre lang="python">
headers = {
	"User-Agent":"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36",
	"Host":"www.quanshu.net",
	"Connection":"keep-alive"
}
</pre>
** 2.爬取链接 **
** 2.1逐步讲解 **
<pre lang="python">
	response = requests.get(novel_url,headers=headers)
</pre>
用requests获取页面信息，并加入了头部伪装
<pre lang="python">
	reg = re.compile(r'<li><a href="(.*?)" title=".*?">(.*?)</a></li>')
</pre>
<img src="http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-1.png" alt="">

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
<img src="http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-2.png" alt="">
** 2.2封装函数 **
<pre lang="python">
def get_list(novel_url):
	response = requests.get(novel_url,headers=headers)
	reg = re.compile(r'<li><a href="(.*?)" title=".*?">(.*?)</a></li>')
	urllist = re.findall(reg,response.text)
	return urllist	
</pre>


<img src="http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-5.png" alt="">


[1]:http://www.runoob.com/python/python-reg-expressions.html