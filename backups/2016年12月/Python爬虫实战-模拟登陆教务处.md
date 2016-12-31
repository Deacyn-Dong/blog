## 前言

上周博客居然漏了，悲伤，这周我会补上。。。这是模拟登陆的第一篇，登陆教务处，下一篇为模拟登陆知乎，两个都学会大部分网站的模拟登陆就ok了，除了一些诸如斗鱼和12306涉及图片交互的验证。

## 技术栈

*   requests的session应用

session: session对象可以存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的 Web 页之间跳转时，存储在session对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。

session和cookie的联系和区别: cookie机制采用的是在客户端保持状态的方案，而session机制采用的是在服务器端保持状态的方案。 具体见: <a href="http://www.cnblogs.com/shiyangxt/archive/2008/10/07/1305506.html" target="_blank">cookie 和session 的区别详解 - 施杨 - 博客园</a>

*   PIL库的Image模块对验证图片的处理

## 开始

### 1.头部写入

<pre lang="python">
import os
import requests
import sys
from PIL import Image
type = sys.getfilesystemencoding()
</pre>

*使用如下指令来安装上述两个第三方库*

<pre>pip install requests,Pillow</pre>

*最一行若不是linux环境请添加，用来之后处理cmd的输出编码问题*

### 2.浏览器伪装

<pre lang="python">
headers = {
    "Accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36",
    "Host":"jwpt.neuq.edu.cn",
    "Connection":"keep-alive"
}
</pre>

### 3.登录模块

![登录页面][1]

可以看到需要输入学号，密码和图形验证码（数字）

我们先登录进去，用Dev Tools查看post了哪些数据

![登录后的页面][2]

可以看到有上述几个数据,其中`Agnomen`项为验证码，我们额外通过一个函数方法来获取，现在开始书写登录函数代码

<pre lang="python">
def login(username,password):
    session = requests.session()
    post_url = "http://jwpt.neuq.edu.cn/ACTIONLOGON.APPPROCESS?mode=4"
    post_data = {
        "WebUserNO":username,
        "Password":password,
        "Agnomen":get_captcha(),
        "strStudentNO":"",
        "strPassword":""
    }
    login_page = session.post(post_url,data=post_data,headers=headers)
    print login_page.status_code #打印登录后的状态码
</pre>

### 4.验证码模块

我们通过Dev Tools找到验证码地址，在地址栏重复刷新，图片中的数字也确实在变化

<pre lang="python">
def get_captcha():
    captcha_url = 'http://jwpt.neuq.edu.cn/ACTIONVALIDATERANDOMPICTURE.APPPROCESS'
    r = session.get(captcha_url,headers=headers)
    # 将图片验证码写入'captcha.jpg'
    with open('captcha.jpg','wb') as f:
        f.write(r.content)
        f.close()
    try:
        im = Image.open('captcha.jpg')     #Image模块的方法，调用系统默认图片打开方式自动打开储存的图片
        im.show()
        im.close()
    except:
        print(u'请到 %s 目录找到captcha.jpg 手动输入' % os.path.abspath('captcha.jpg'))
    captcha = input("please input the captcha\n>")  #英文字符无需考虑编码问题
    return captcha
</pre>

这样我们就获取了验证码

### 5.登录后续操作

**这部分源码只适用于我们学校，理解其中思路即可**

我尝试用登录后的session来获取我的其中一项成绩做测试

我们需要导入其他库

<pre lang="python">
import json
from bs4 import BeautifulSoup

def get_grades():
    grade_url = "http://jwpt.neuq.edu.cn/ACTIONQUERYGRADUATESCHOOLREPORTBYSELF.APPPROCESS"
    content = session.get(grade_url).text  #用登录过的session轻松处理其他需要登录才能访问的url
    soup = BeautifulSoup(content,'lxml')

    for i in xrange(1,2):
        same_class = soup.select('tr[style="height:23px"]')[i].select("td")
        data = {
            "term":same_class[0].text.replace(u'\xa0', ''),
            "course_title":same_class[2].text.replace(u'\xa0', ''),
            "period":same_class[4].text.replace(u'\xa0', ''),
            "credit":same_class[5].text.replace(u'\xa0', ''),
            "grade":same_class[6].text.replace(u'\xa0', ''),
            "GPA":same_class[7].text.replace(u'\xa0', ''),
            # "pass_or_fail":isPass()
        }
        data_result = json.dumps(data, ensure_ascii=False, encoding='UTF-8')
        return data_result
</pre>

### 6.方法入口

<pre lang="python">
if __name__ == '__main__':
    username = raw_input("请输入你的学号\n>".decode('utf-8').encode(type)) #sublime环境中编码为utf-8，我们将其解码为系统文字编码，这里的type如果是windows环境也可写成'gbk'
    password = raw_input("请输入你的密码\n>".decode('utf-8').encode(type))
    #调用登录方法
    login(username,password)
    #调用获取成绩方法
    print get_grades()
</pre>

我们在cmd中调用python执行命令

![输出案例][3]

 [1]: http://ohc0a3roa.bkt.clouddn.com/blog/jiaowuchu/1.jpg
 [2]: http://ohc0a3roa.bkt.clouddn.com/blog/jiaowuchu/2.jpg
 [3]: http://ohc0a3roa.bkt.clouddn.com/blog/jiaowuchu/3.jpg