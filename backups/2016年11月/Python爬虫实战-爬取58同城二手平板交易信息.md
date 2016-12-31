## 前言

话说好久没写博客了，一直在学习python爬虫，这次就出个系列实战教程，今天是第一篇，侧重web页面内某个细节内容的抓取

## 要点

*   Python的基本语法知识

*   Requests和BeautifulSoup的熟练操作

*   页面DOM结构的基本认识

## 准备工作

*   熟悉BeautifulSoup的基本操作

[Beautiful Soup 4.2.0 文档][1]

[Python爬虫利器二之Beautiful Soup的用法][2]

*   熟悉Requests库的用法

[快速上手 — Requests 2.10.0 文档][3]

[Python爬虫利器一之Requests库的用法][4]

*   安装必要模块：

json模块已经内置，使用pip安装requests和beautifulsoup4

*   一个趁手的代码编辑器或者IDE

我选择sublime text 3，并且通过packeage control安装了anaconda插件

> 我的环境是windows10+Python2.7的环境

*   文件头部写入

<pre lang="python">
#coding:utf-8 使正常使用中文
import requests
import json
from bs4 import BeautifulSoup
</pre>

*json库的妙用后面会谈*

## 正文开始

### 1.选择目标url

![spider-58com][5]

去掉url后面暂时不影响访问的参数

<pre lang="python">
test_url = "http://zhuanzhuan.58.com/detail/787875725913325572z.shtml"
wb_data = requests.get(test_url)
soup = BeautifulSoup(wb_data.text,'lxml')
</pre>

这样我们就获得了整个页面的信息，可以打印输入看下结果

<pre lang="python">
print soup
</pre>

打印结果太长我就不贴了

### 2.选择目标信息

#### 2.1获取商品的标题

<pre lang="python">
title = soup.title.text
</pre>

商品的标题，可以发现它既在浏览器头部又在body内容中，选择第一种获取方式比较简单

#### 2.2获取商品的价格

<pre lang="python">
price = soup.select('span.price_now i') 
</pre>

#### 2.3获取商品的位置

<pre lang="python">
place = soup.select('div.palce_li i')
</pre>

#### 2.4获取商品的浏览量

<pre lang="python">
look_time = soup.select('span.look_time')
</pre>

#### 2.5获取商品的愿意购买人数

<pre lang="python">
want_person = soup.select('span.want_person') 
</pre>

#### 2.6打印调试

<pre lang="python">
print title,price,look_time,want_person,place
</pre>

发现输出正常：

<pre>
【3图】ipadmini900元转让_上弦月4567的闲置物品-转转,58同城二手
[<i>810</i>] 
[<span class="look_time">863\u6b21\u6d4f\u89c8</span>]
[<span class="want_person">5\u4eba\u60f3\u4e70</span>] 
[<span class="want_person">5\u4eba\u60f3\u4e70</span>] 
</pre>

#### 2.7将信息统一存入字典

在2.6中打印虽然正常，但是结果内容不令我们满意，我们只需要获取标签中的属性或者内容，而不需要标签的其他冗余信息

所以这里做下处理：

<pre lang="python">
data = {
    'title':title,
    'price':price[0].text,
    'look_time':look_time[0].text,
    'want_person':want_person[0].text,
    'place':place[0].text,
}
</pre>

**字典中的逗号分隔小心丢掉**

使用bs4的select操作返回的是一个**单值列表**，所以先输入xxxx[0]，再使用text方法获取文本内容

然后我们打印调试看下结果：

<pre lang="python">
{'want_person': u'5\u4eba\u60f3\u4e70', 'price': u'810', 'place': u'\u79e6\u7687\u5c9b-\u5c71\u6d77\u5173', 'look_time': u'864\u6b21\u6d4f\u89c8', 'title': u'\u30103\u56fe\u3011ipadmini900\u5143\u8f6c\u8ba9_\u4e0a\u5f26\u67084567\u7684\u95f2\u7f6e\u7269\u54c1-\u8f6c\u8f6c,58\u540c\u57ce\u4e8c\u624b'}
</pre>

在python中字符串在字典中的保存形式为unicode格式，但是调试时好不爽，怎么办？

这里使用json模块的方法，将字典转序列化为json格式，使汉字正常显示，又不影响后续进程。

<pre lang="python">
data_result = json.dumps(data, ensure_ascii=False, encoding='UTF-8')
</pre>

再来打印调试结果：

<pre lang="python">
print data_result
</pre>

得到结果发现一切正常

<pre lang="python">
{"want_person": "5人想买", "price": "810", "place": "秦皇岛-山海关", "look_time": "865次浏览", "title": "【3图】ipadmini900元转让_上弦月4567的闲置物品-转转,58同城二手"}
</pre>

#### 拓展：

由于want_person和look_time项实际打印值中有汉字，但有时我们可能只需要数字

这里我们使用字符串的内建replace函数将其中内容进行替换处理：

在

<pre lang="python">
data_result = json.dumps(data, ensure_ascii=False, encoding='UTF-8')
</pre>

前加入如下代码：

<pre lang="python">
data['want_person'] = data['want_person'].replace(u'人想买','')
data['look_time'] = data['look_time'].replace(u'次浏览','')
</pre>

打印调试：

<pre lang="python">
{"want_person": "5", "price": "810", "place": "秦皇岛-山海关", "look_time": "911", "title": "【3图】ipadmini900元转让_上弦月4567的闲置物品-转转,58同城二手"}
</pre>

#### 2.8将函数封装

至此我们的单页信息抓取已经结束，我们将其中核心模块进行封装，方便后续调用

<pre lang="python">
def get_one_page(url):  
    data = requests.get(url)
    soup = BeautifulSoup(data.text,'lxml')
    title = soup.title.text
    price = soup.select('span.price_now i')
    look_time = soup.select('span.look_time')
    want_person = soup.select('span.want_person')
    place = soup.select('div.palce_li i')
    data = {
        'title':title,
        'price':price[0].text,
        'look_time':look_time[0].text.replace(u'次浏览',''),
        'want_person':want_person[0].text.replace(u'人想买',''),
        'place':place[0].text
    }
    data_result = json.dumps(data, ensure_ascii=False, encoding='UTF-8')
    return data_result
</pre>

### 3.获取待抓取页面的链接

首先创建一个列表，存入抓取的链接

<pre lang="python">
links = []
</pre>

获取链接，和2中方法类似

<pre lang="python">
page = 'http://qhd.58.com/pbdnipad/0/pn1/'
data = requests.get(page)
soup = BeautifulSoup(data.text,'lxml')
</pre>

进行遍历将链接存入links列表中

<pre lang="python">
for link in soup.select('table.tbimg td.t a.t'):
    links.append(link.get('href'))
return links
</pre>

同样进行函数封装：

<pre lang="python">
def get_links(url_links):
    links = []
    wb_data = requests.get(url_links)
    soup = BeautifulSoup(wb_data.text,'lxml')
    for link in soup.select('table.tbimg td.t a.t'):
        links.append(link.get('href'))
    return links
</pre>

打印调试：

<pre lang="python">
print get_links(page)
</pre>

打印正常，链接太多，我就不贴了

### 4.获取每个链接内的信息

观察url_links内是由**pn数字**来控制翻页，设置初始页码为1，总页码为12

<pre lang="python">
page = 1
end_page = 12
</pre>

用循环进行迭代

<pre lang="python">
while page <= end_page:
    new_page = 'http://qhd.58.com/pbdnipad/0/pn{}/'.format(page)
    urls = get_links(new_page)
    for url in urls:
        print get_one_page(url)
    page += 1
</pre>

同样进行函数封装：

<pre lang="python">
def get_every_page(end_page):
    page = 1
    while page <= end_page:
        new_page = 'http://qhd.58.com/pbdnipad/0/pn{}/'.format(page)
        urls = get_links(new_page)
        for url in urls:
            print get_one_page(url)
        page += 1
</pre>

打印调试：

<pre lang="python">
get_every_page(12)
</pre>

### 思考

*   `get_every_page()`函数中我使用了`print get_one_page(url)`进行调试，显然我们不能只是把爬取数据在内存中处理打印在屏幕上，所以一个良好的数据存储方式绝对是一个爬虫的价值体现，思考如何对本文最终数据进行存储

*   尝试寻找其他类似分类页面爬写（比如北京的二手手机交易信息）

## 结束

至此完整爬取已经结束，源代码之后会放入github供下载学习，切勿用作商业目的

有任何问题欢迎通过邮件私聊我，在**关于**页面有写

> 本文仅做学习交流，同学们切勿用作商业目的，转载标注出处[Python爬虫实战-爬取58同城二手平板交易信息-deacyn的修炼之路][6]

 [1]: https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html
 [2]: http://cuiqingcai.com/1319.html
 [3]: http://docs.python-requests.org/zh_CN/latest/user/quickstart.html
 [4]: http://cuiqingcai.com/2556.html
 [5]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/58com-1.png
 [6]: http://blog.deacyn.cn/index.php/2016/11/20/python-spider-58com/