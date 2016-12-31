### 前言

看到不少网上关于垂直居中的实现方法，自己也想过一些，这里做个整理，分享出来

### 基本结构

HTML:

<pre lang="html">
<div class="container>
    <div class="content">233333333</div>
</div>
</pre>

CSS: 
<pre lang="css">
.container{ width: 400px; height: 400px; background: red; } .content{ width: 200px; height: 200px; background: yellow; 
</pre>

<h4>方法一：绝对定位+负margin值</h4>

<pre lang="css"> .content{ position: absolute; top: 50%; left: 50%; margin-top: -100px; margin-left: -100px; } </pre>

负margin值各为宽高的一半

<h4>方法二：绝对定位+transform</h4>

<pre lang="css"> .content{ position: absolute; top: 50%; left: 50%; transform: translate(-50% -50%); }</pre>

transform为CSS3新特性，在网易云课堂微专业课上见过一次，觉得蛮简洁好用的

<h4>方法三：绝对定位+marigin：auto</h4>

<pre lang="css"> .content{ position: absolute; left: 0; top: 0; right: 0; bottom:0; margin: auto; } </pre>

设置上下左右全部定位为0，margin值为auto，让渲染引擎自行计算位置，一种比较hack的方法 

注：IE6不支持，其余主流浏览器均支持

<h4>方法四：绝对定位+calc函数</h4>

<pre lang="css"> .content{ position: absolute; top: calc(50% - 100px); left: calc(50% - 100px); } </pre>

利用CSS3计算函数calc自行计算定位

<h4>方法五：display:table</h4>

<pre lang="css"> body{ display: table; } .container{ display: table-cell; vertical-align: middle; } .content{ margin: auto; overflow-wrap: break-word; height: auto; }</pre>

由于利用了talble实现，所以兼容性还可以

<h4>方法六：display:inline-block+伪类元素</h4>

<pre lang="css"> .container{ text-align: center; white-space: nowrap; } .container:before{ content: ""; display: inline-block; height: 100%; vertical-align: middle; margin-right: -0.25em; } .content{ display: inline-block; vertical-align: middle; }</pre>

<h4>方法七：display:flex</h4>

<pre lang="css"> .container{ display: flex; } .content{ margin: auto; display: flex; align-items: center; justify-content: center; } </pre>

flex布局转为解决移动端问题，被用在很多地方，但是旧版浏览器兼容性不太好

推荐教程:[Flex 布局教程：语法篇-阮一峰的网络日志][1]

### 尾语

这里摘录了我个人见过几种比较好的垂直居中的用法，有的需要已知宽高（方法一、三和四），有的略有些hack的感觉，但我最常用的还是方法二，比较css3现在主流浏览器都支持，而且这种写起来很流畅。

此外我还见过诸如：利用display：box，利用css3新单位vh等的方法，感兴趣可以自行百度

这里安利一篇实现原理讲解非常详细的博客：

[盘点8种CSS实现垂直居中水平居中的绝对定位居中技术][2]

 [1]: http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool
 [2]: http://blog.csdn.net/freshlover/article/details/11579669