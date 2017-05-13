## 前言

上次说到做了一个小说阅读器，那内容从哪儿来呢？只能是爬来的了。

## 要点

* 爬虫数据存储(解决上次思考题)

* 正则表达式的使用 [Python正则表达式-菜鸟教程][1]

* 浏览器的简单伪装

* 面向对象的类的初步使用

## 准备工作

* MySQL的配置

![MySQL的配置][2]

创建一个“novel”数据库，其中再创一个“dbmj”的数据表，其中设置一个自增的int型id，一个varchar10的title，一个varchar1000的content（正文内容较多，可以设大点）

* 头部写入

<pre lang="python">
# -*- coding: utf-8 -*-
import requests
import re
import MySQLdb
import sys
reload(sys)
sys.setdefaultencoding('utf-8')
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

#### 2.1逐步讲解

用requests获取页面信息，并加入头部伪装

<pre lang="python">
response = requests.get(novel_url,headers=headers)
</pre>

![][3]

分析html结构，发现只有href，title和标签文本节点不同,使用正则匹配

<pre lang="python">

reg = re.compile(r'<li><a href="(.*?)" title=".*?">(.*?)</a></li>')

</pre>

> `.*`具有贪婪的性质，首先匹配到不能匹配为止，根据后面的正则表达式，会进行回溯 `.*？`则相反，一个匹配以后，就往下进行，所以不会进行回溯，具有最小匹配的性质。 `(.?)`设置分组

使用findall方法查找，并将匹配的结果存入urllist列表中

<pre lang="python">
urllist = re.findall(reg,response.text)
</pre>

我们打印看下结果

<pre lang="python">
print urllist
</pre>

![urllist]urllist[4]

发现其中奇怪的`\x`结构，这是anscii的编码，我们要的应该是Unicode字符

我们打印下Requests推测使用的编码格式

<pre lang="python">print response.encoding
</pre>

![response.encoding][5]

实际页面编码格式为“gb2312”，他推断为“ISO-8859-1”，所以我们需要对他手动校正

在第一句下添加如下代码：

<pre lang="python">
response.encoding = "gb2312"
</pre>

![urllist][6]

这样就正常了

#### 2.2封装函数

<pre lang="python">def get_list(novel_url):
    response = requests.get(novel_url,headers=headers)
    response.encoding = "gb2312"
    reg = re.compile(r'<li><a href="(.*?)" title=".*?">(.*?)</a></li>')
    urllist = re.findall(reg,response.text)
    return urllist
</pre>

### 3.获取页面内容

#### 3.1逐条讲解

将前面代码注释

前面获得的链接只有子目录，我们需要将其与host拼接，示例链接“http://www.quanshu.net/book/9/9055/9674264.html”，将其拆分拼接，即格式为某小说站点+/+子链接，代码转化为`novel_url+"/"+url`

<pre lang="python">
response = requests.get(novel_url+"/"+"9674264.html",headers=headers)
</pre>

为了测试方便我们指定url->"9674264.html"

*感兴趣的同学可以研究urlparse.urljoin()方法*

如果打印response.encoding,你会发现requests推断再次错误，手动校正为‘gbk’

<pre lang="python">
response.encoding = 'gbk'
</pre>

在页面点击右键查看源代码，用ctrl+f快速搜索定位到文章代码位置，使用正则匹配。

![][7]

![][9]

**切记要对括号进行转义**

用re的findall方法匹配正文内容存入content的变量

<pre lang="python">
content = re.findall(reg,response.text)
</pre>

我们打印看一下结果

<pre lang="python">
print content[0]
</pre>

由于findall返回的是一个列表，而匹配的正文只有一个，所以我们取第一项

![][8]

<img src="" alt="" />



<p>
  一切正常
</p>



<h4>
  3.2函数封装
</h4>



<pre lang="python">
def get_text(url):
    response = requests.get(novel_url+"/"+url,headers=headers)
    response.encoding = 'gbk'
    reg = re.compile(r'style5\(\);</script>(.*?)<script type="text/javascript">style6')
    content = re.findall(reg,response.text)
    return content[0]
</pre>

### 4.数据库配置

#### 4.1数据库的配置


<pre lang="python">
class Sql(object):
    conn = MySQLdb.connect(
        host="localhost",
        port=3306,
        user="xxxxxx",
        passwd="xxxxxx",
        db="novel",
        charset="utf8"
    )
</pre>


定义一个类，指定host(我选择本地测试)，端口(默认3306),user和passwd指定MySQL的用户和密码，db（为此次连接和爬虫指定一个数据库，novel），charset为utf-8

### 4.1数据库的连接测试

<pre lang="python">
class Sql(object):
    def testConnect(self):
        cur = self.conn.cursor()
        cur.execute("SELECT VERSION()")
        data = cur.fetchone()
        print "Database version : %s " % data
        self.conn.close()
</pre>

这段代码会打印出MySQL的版本信息，用于测试是否连接

<pre lang="python">
mysql = Sql()
mysql.testConnect()
</pre>

实例化一个mysql，调用testConnect方法，编译运行


![][10]

#### 4.3定义插入数据的方法

<pre lang="python">
    def insert(self,title,content):
        cur = self.conn.cursor()
        cur.execute("insert into dmbj(title,content) values('%s','%s')" %(title,content))
        self.conn.commit()
        # cur.close()
</pre>

获取数据库连接后的游标，我们初步只需要插入标题和具体内容，我们执行(execute)插入命令并提交(commit),然后关闭(close)此次连接，(最后我会直接关闭mysql这里我就注释掉了)

#### 4.4代码重写

<pre lang="python">
class Sql(object):
    conn = MySQLdb.connect(
        host="localhost",
        port=3306,
        user="xxxxxx",
        passwd="xxxxxx",
        db="novel",
        charset="utf8"
        )
    def insert(self,title,content):
        cur = self.conn.cursor()
        cur.execute("insert into dmbj(title,content) values('%s','%s')" %(title,content))
        self.conn.commit()
        # cur.close()
mysql = Sql()
</pre>

### 5.正式爬取并储存

#### 5.1循环遍历测试

<pre lang="python">
for i in get_list(novel_url):
    url = i[0]
    print "链接 %s" %url
</pre>

![][11]

#### 5.2循环遍历爬取标题和内容

<pre lang="python">
for i in get_list(novel_url):
    title = i[1]
    print "正在爬取文章,章节名为 %s \n" %title
    content = get_text(i[0])
</pre>

标题和链接在列表页获取，分别为第一项和第零项，将零项的链接传入getContent获取正文内容，中间加一个打印实时监控情况

![][12]

#### 5.3循环遍历插入数据库

<pre lang="python">
    print "正在插入数据库\n"
    mysql.insert(title,content)
    print "插入数据库成功"
</pre>

调用mysql的方法insert插入数据，加两个打印实时监控情况

#### 5.4爬取之后的处理

<pre lang="python">
mysql.conn.close()
print "任务成功"
</pre>

关闭数据库，显示任务结束

#### 5.5代码重写

<pre lang="python">
if __name__ == '__main__':
    for i in get_list(novel_url):
        title = i[1]
        print "正在爬取文章,章节名为 %s" %title
        content = get_text(i[0])
        print "正在插入数据库\n"
        mysql.insert(title,content)
        print "插入数据库成功"

    mysql.conn.close()
    print "任务成功"
</pre>

## 结束

此次实战就到此结束，惯例我会在github源码(含思考项的内容)

## 思考

1.尝试使用第三库web.py在本地搭建一个微型服务器，调用数据库信息显示在前端页面
2.上节58的爬取内容更适合关系型数据库MongoDB，因为爬取的data项类似json格式，MongoDB也是，尝试使用MongoDB储存上节任务的数据

## 后话

本节任务教程仅供学习使用，切勿用作商业用途

 [1]: http://www.runoob.com/python/python-reg-expressions.html
 [2]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-10.png
 [3]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-11.png
 [4]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-2.png
 [5]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-5.png
 [6]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-4.png
 [7]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-3.png
 [8]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-6.png
 [9]: http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-12.png
 [10]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-7.png
 [11]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-8.png
 [12]:http://ohc0a3roa.bkt.clouddn.com/blog/spider/dmbj-novel-9.png