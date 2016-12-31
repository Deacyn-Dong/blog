### 前言

上个月刚好抢了腾讯云学生的云服务器，就决定玩玩了，结合之前虚拟机的经验和新坑的解决方法，决定自己写一个wordpress的搭建之旅，用的LAMP的环境（php是5.6的版本）

### 涉及技术栈

centos的基本操作（主要是yum安装方法，没用编译），yum源的更换，LAMP环境的搭建，wordpress和phpmyadmin的部署，apache日志查看，二级域名绑定目录等

### 开始

1.换掉yum自带源：

在终端输入如下指令：

<pre>
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
wget http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
sudo rpm -Uvh remi-release-6*.rpm epel-release-6*.rpm
</pre>  

将enabled参数都改为1：

<pre>
sudo vim /etc/yum.repos.d/remi.repo
</pre>  

2.安装apache：

在终端输入安装指令：

<pre>
yum -y install httpd
</pre>
    

启动apache服务：

<pre>/etc/rc.d/init.d/httpd start</pre>
或
<pre>service httpd start</pre>
    

检查启动结果：

<pre>ps aux | grep httpd</pre>
    

或者直接在浏览器访问自己的ip地址，出现Apache 2 Text Page代表服务正常启动

2.安装mysql

在终端输入安装指令：

<pre>yum -y install mysql mysql-server</pre>
    

检查启动结果：

<pre>netstat -tulnp | grep ：3306</pre>
    

设置root用户密码：

<pre>mysqladmin -u root -p</pre>
    

输入设置的密码

3.安装php及其组件

在终端输入安装指令：

<pre>yum -y install php php-mysql php-devel php-xml php-xmlrpc php-gd php-imap php-ldap php-odbc php-pear</pre>
    

> 注：这里只装了几个我用的插件，具体功能此处不予说明，感兴趣可以百度

查看php情况：

<pre>php -v</pre>
    

显示版本为5.6

根目录新建index.php

<pre>nano /var/www/html/index.php</pre>
    

输入：
<pre> <?php phpinfo(); ?> </pre>
在浏览器输入ip地址或域名，显示php版本信息等，证明php服务正常

4.安装wordpress：

创建wordpress数据库：

<pre>mysql -u root -p</pre>
    

输入root用户密码进入mysql交互界面：

<pre>create database wordpress;</pre>
    

> 注：不要丢掉**;**

修改php.ini配置使长传效率提高：

<pre>locate -i php.ini</pre>
    

然后编辑它：

<pre lang="php">nano /etc/php.ini</pre>
    

可以用ctrl+w 搜索post_max_size 和 upload_max_filesize 将其大小改为64M

去wordpress官网下载压缩包，解压，通过Filezilla软件上传至/var/www/html目录，目标文件夹名应为wordpress

设置wordpress：

浏览器地址栏输入“http://ip地址/wordpress”进入wordpress初始界面

用户设置为root，密码输入root用户密码，点击下一步

此时可能提示手动设置wp-config.php文件，在桌面新建wp-config.php文件，将网站代码贴入文件内，通过filezilla上传至wordpress目录

点击下一步安装，设置自己的个人账户，包括登陆用户名和密码，然后顺利进入仪表盘

此时wordpress基本可以正常使用