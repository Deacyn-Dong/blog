## 前言

在爬虫过程中，有时候会发现F12查看的Elements中存在的内容，右键查看源码时会发现原位置的文字或图片没有了，这时多半网站使用了ajax技术来填充数据，这既是前端的页面优化中的重要部分，也有效地防止了一些爬虫机器人的骚扰

办法总比困难多，碰到这种情况我们应该怎么办？这节实战以拉钩网招聘信息为例作展示

*ps:写博客前本来打算以淘女郎为例子，结果页面改版代码废了= =*

## 技术栈

*   ajax技术的了解

**ajax**:Asynchronous Javascript And XML(异步JavaScript和XML)，通过与服务端进行少量数据交互，实现在不刷新或跳转页面的情况下，对网页局部内容进行填充或更新，也是如今SPA（单页应用程序）的重要组成部分。

使用ajax获取的不一定是xml，如今更广泛的是json格式

ajax优点: 
1.页面仅仅局部刷新，极大减轻服务端的压力 
2.按需取数据,减少冗余请求 
3.使用异步方式与服务器通信，具有更加迅速的响应能力。 
4.用户感受不到刷新，卡顿等，体验度提高

缺点： 
1.依托大量JavaScript代码实现功能和完善兼容性，这样会使得页面源代码变得冗杂 
2.一些安全问题 3.对搜索引擎不友好

*   requests库的post用法

还是推荐先看API文档，再来实战

## 准备工作

*   头部写入

<pre lang="python">
# coding:utf-8
import time
import requests
import json
import re
</pre>

*time模块之后会做简单的显式爬取间隔*

## 开始

### 1.浏览器伪装和全局变量定义

<pre lang="python">
headers= {
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36",
    "Referer":"https://www.lagou.com/jobs/list_python?labelWords=&fromSearch=true&suginput=",
    "Connection":"keep-alive"
}
items = []
</pre>

headers内依然是基本的浏览器信息，定义一个空列表items存放每个分部的信息

### 2.运用chromeF12中的Network选项查看ajax信息

搜索框输入python，进行搜索 

![1][1]

在第一条信息选中**[北京-朝阳区]**，右键点击‘检查’，出现相应代码含文字

![2][2]

页面空白处点击右键“查看源代码”

![3][3]

ctrl+f搜索`北京-朝阳区`，结果却显示零条

![4][4]

那么信息到底藏到了哪里？

我们点击F12中的Network，再点击`XHR`块，在其中Name区域出现的信息逐条点击，在Preview区域逐条查看，找到了信息来源

![5][5]

这时我们需要找寻请求的URL和请求的参数，我们点击Headers区域查找

![6][6]

在General区域我们找到了Request URL，数据就是从这里请求得到的，我们将此URL复制备用

我们简要分析下Request Headers区域

`Accept:application/json, text/javascript, */*; q=0.01` : 接受信息中有json格式

`Accept-Encoding:gzip, deflate, br` : 支持采用gzip、deflate或br压缩过的资源

`Accept-Language:zh-CN,zh;q=0.8` : 可以接受zh-CN和zh三种语言，并且zh-CN的权重最高，q=0.8，服务端应该优先返回语言等于zh-CN的版本。

`X-Requested-With:XMLHttpRequest` : 从XMLHttpRequest可以看出确实使用了ajax技术

> X-Anit-Forge-Code和X-Anit-Forge-Token的作用我也不太了解，anit-forge-code是反伪造代码，token是令牌，猜测都是为了安全考虑

从From Data区域中查看post提交了哪些信息 ![7][7]

`first:true`这个不管，直接设为true，`pn:1`，这个明显是页码的意思，`kd:python`，搜索关键词是python

### 3.获取信息代码解析

<pre lang="python">def get_content(pn,kd):
    url = "https://www.lagou.com/jobs/positionAjax.json?needAddtionalResult=false"
    data = {
        "first":"true",
        "pn":pn,
        "kd":kd
    }
    html = requests.post(url,headers=headers,data=data).text
    html = json.loads(html)
    for i in range(15):
        item = []
        item.append(html['content']['positionResult']['result'][i]['positionName'])
        item.append(html['content']['positionResult']['result'][i]['salary'])
        item.append(html['content']['positionResult']['result'][i]['positionAdvantage'])
        item.append(html['content']['positionResult']['result'][i]['workYear'])
        items.append(item)
    return items
</pre>

url为前面的Request URL，data中设置要post的信息，其中pn和kd设为变量

填充post方法参数url，headers和data，来获取<class 'requests.models.Response'>类，使用text方法转化为unicode字符

因为请求的信息为json格式，使用json模块的loads方法转化为Python的dict(字典)，进行下一步操作

用一个for循环，上限为15，一页显示15条信息，为每次循环定义一个列表，单独存放每条信息的每项内容，循环完返回总的列表

<pre lang="python">
def write():
    pageNum = 1
    while pageNum <=4:
        get_content(pageNum ,'python')
        pageNum += 1
        for i in items:
            print u'职位: %s 薪资: %s 福利:%s 要求:%s \n'  %(i[0],i[1],i[2],i[3])
        time.sleep(2)
</pre>

这里需要注意的是kd参数必须为字符串格式，还有使用time模块的sleep方法，每间隔2s再次执行

我们只爬4页做测试

<pre lang="python">
if __name__ == '__main__':
    write()
</pre>

调用打印，显示正常

![8][8]

## 思考

1.在`html['content']['positionResult']['result'][i]`中还有其他信息，诸如education（教育背景），city（所在城市）等等 2.由于我输出的数据都是字符串格式，所以选择了列表这一数据结构，而没有选择字典

the end ~~

 [1]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/1.png
 [2]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/2.png
 [3]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/3.png
 [4]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/4.png
 [5]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/5.png
 [6]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/6.png
 [7]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/7.png
 [8]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/lagou/8.png