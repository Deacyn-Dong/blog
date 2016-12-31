### 前言

这篇主要介绍一下wordpress搭建后需要注意的事项，二级域名绑定目录，phpmyadmin管理数据库以及apache日志查看等

### 开始

#### 1.安装phpmyadmin

从phpmyadmin官网<https://www.phpmyadmin.net/>下载phpmyadin，解压重命名文件夹为phpmyadmin，放入/var/www/html路径

浏览器输入http://ip地址/phpmyadmin，输入mysql的root用户及密码进行数据库管理界面

#### 2.二级域名绑定

*鉴于通过http://ip/wordpress访问博客的url路径太过繁琐，决定尝试使用二级域名绑定相应目录（例如：blog.xxxx.cn直接访问wordpress）*

使用filezilla工具将/etc/httpd/conf/httpd.conf先行备份,然后下载到本地进行编辑

**具体编辑**:

（1）.将#LoadModule rewrite_module modules/mod_rewrite.so前的 **#** 号去掉，即取消注释状态

（2）.将AllowOverride None最后的 **None** 改为 **All**

（3）.在文件最后插入如下代码：

<pre>
RewriteEngine on
RewriteMap lowercase int:tolower
RewriteMap vhost txt:/etc/httpd/vhost.map
RewriteCond ${lowercase:%{SERVER_NAME}} ^(.+)$
RewriteCond ${vhost:%1} ^(/.*)$ 
RewriteRule ^/(.*)$ %1/$1
</pre>

编辑后将文件保存通过filezilla覆盖服务器原文件

在终端输入如下重启apache服务器：

<pre>service httpd restart</pre>
    

之后在路径/etc/httpd下新建文件vhost.map，下载本地用文本编辑器编辑

**举例**

<pre>blog.deacyn.cn /var/www/html/wordpress</pre>

代表我将wordpress目标绑定到二级域名为blog下，后即可直接通过该二级域名访问

*注：新改动该文件时无需重启apache，直接保存即可生效*

到此本次wordpress之旅基本结束

### 注意事项及解决方法

#### 1.换yum源的作用及要点

centos自带源的php软件包版本太低，为5.3.3，

知名的php框架[Laravel][2]需要 **PHP >= 5.5.9**

除此之外低版本还面临着打安全补丁的繁琐步骤，不如升级，所以需要换yum源，

国内比较好的有163，中科大等，可自行百度

#### 2.apache和Nginx选择

个人选择apache，世界第一的老牌web服务器，成熟的技术和开发社区的支持，处理动态优势比较大

Nginx并发性比较好，非阻塞，国内大厂也都在用，现在一般做负载均衡器，搭配node中间件

各有优缺点，自行搜索查阅

#### 3.数据库问题

Mysql开源免费，对于小型的个人网站十分适用，推荐这个，另外推荐尝试下postgresql，听说还不错

[阮一峰的PostgreSQL新手入门教程][1]

**wordpress与mysql交互安全问题:**
 
不少人开始图省事会在wordpress初始界面的mysql中输入root用户和密码，这样给予wordpress的数据库权限太大，容易发生安全问题

在终端输入：

<pre>grant all on wordpress.* to 'xxx'@'localhost' identified by 'xxxx'</pre>
    

然后在wp-config.php文件中同步更新**用户名**为**xxx**，**密码**为**xxxx**。

#### 4.php版本问题

同第一条，不建议升至7.0版本，太新的东西往往不太适用，而且与5.X版本语法等改动较大

#### 5.wordpress问题

（1）安装插件等需要ftp权限

在wp-config.php最后加入

<pre>
define("FS_METHOD", "direct");
define("FS_CHMOD_DIR", 0777);
define("FS_CHMOD_FILE", 0777);
</pre>

（2）插件，主题等安装老失败

推测没有给相应目录赋予的写入权限，

通过filezilla给wp-content及其子目录plugins，themes，languages右键设置权限为777**

或者chmod指令

（3）发表文章后打不开（url路径含有中文无法访问）

查看编辑文章页面的链接部门是否含有中文，如果有编辑改为英文即可

同理，分类目录，名称可以为中文，但别名要为英文

*注：听说装入Chinese Tag Names的插件可以解决url路径含有中文无法打开的问题，可以尝试下*

#### 6.phpmyadmin问题

（1）phpmyadmin登陆报错，显示查询错误：

phpadmin与php对应不一致，PhpMyAdmin4.6.x只兼容PHP5.5-PHP7.0，PhpMyAdmin4.0.x可兼容5.5版本以下

（2）phpmyadmin页面显示HTTP ERROR 500

http error 500为内部服务器报错，进入/var/log/httpd，查看error_log文件，查看最新的一条，显示错误如下：

<pre>PHP Fatal error:  Call to undefined function mb_detect_encoding() in /var/www/html/phpmyadmin/libraries/php-gettext/gettext.inc on line 177</pre>
    

大概意思为php错误调用了未定义的一个函数，函数叫做：mb_detect_encoding()

解决首先安装 php-mbstring库文件：

<pre>yum install php-mbstring</pre>
    

修改/etc/php.ini文件，加入：

<pre>extension=mbstring.so</pre>

重启apache服务：

<pre>service httpd restart</pre>

 [1]: http://www.ruanyifeng.com/blog/2013/12/getting_started_with_postgresql.html
 [2]: https://laravel.com/