## 目录

1.<a href="#jump1" target="_self" rel="nofollow">第一章 什么是JSON</a>

2.<a href="#jump2" target="_self" rel="nofollow">第二章 JSON语法</a>

3.<a href="#jump3" target="_self" rel="nofollow">第三章 JSON的数据类型</a>

4.<a href="#jump4" target="_self" rel="nofollow">第四章 JSON Schema</a>

5.<a href="#jump5" target="_self" rel="nofollow">第五章 JSON中的安全问题</a>

6.<a href="#jump6" target="_self" rel="nofollow">第六章 JavaScript中的XMLHttpRequest与Web API(简略)</a>

7.<a href="#jump7" target="_self" rel="nofollow">第七章 JSON与客户端框架(略)</a>

8.<a href="#jump8" target="_self" rel="nofollow">第八章 JSON与NoSQL(略)</a>

9.<a href="#jump9" target="_self" rel="nofollow">第九章 服务端的JSON</a>

10.<a href="#jump10" target="_self" rel="nofollow">第十章 总结</a>

## 开始

### 第一章 什么是JSON {#jump1}

*   json的全称是 JavaScript对象表示法(JavaScript Object Notation)

*   json是一种用于不同平台或系统间的数据交换格式

*   json完全可以独立于JavaScript等的编程语言

### 第二章 JSON语法 {#jump2}

*   字面量即为字面意思与其要表达的意思等价

*   键值对即为拥有名称和对应值的属性

*   json的键-值对中的**键**始终被双引号包裹，**值**可以是字符串，数字，布尔值，null，对象或数组

*   json文件拓展名为.json文件，媒体类型为application/json

*   json基于JavaScript的对象字面量，所以他们的书写格式容易混淆

json的键-值对中的名称两边要加双引号，例如

<pre>
{
    "title":"Deacyn的修炼之路"
}
</pre>

JavaScript的对象字面量中键-值对的名称无需加引号，或者单双引号都行

<pre>
var Blog = {
    title:"Deacyn的修炼之路",
    'autor':"Deacyn"
}
</pre>

如果json使用了上述写法，那么它将变得不合法

### 第三章 JSON的数据类型 {#jump3}

*   数据类型概要

字符串类型：例"string"

布尔类型：true 或者 false

数字类型：整数，浮点数或指数。例11,12.45

null类型：只有null，表示空值

数组类型：值的列表或集合，值可以是字符串，数字，布尔值，对象或数组中的一种，数组被**[]**包裹，值之间用逗号相隔

*   JSON中的true，false和null均为小写

*   对象和数组的区别

1.对象是键值对构成的列表或集合，数组是值构成的列表或集合

2.数组中的所有值应该具有相同的数据类型

### 第四章 JSON Schema {#jump4}

*   JSON Schema的相关概念

JSON Schema仍在开发中

JSON Schema是数据交换中一种虚拟“合同”，用于负责JSON的一致性检验

JSON Schema是数据接收方的第一道防线，也是数据发送方节约时间、保证数据正确的好工具

JSON Schema解决以下问题：

1.值得数据类型是否正确

2.是否包含所需要的数据

3.值的形式是不是我需要的

JSON Schema举例：

使用JSON书写，在JSON第一个键值对中，声明为一个schema文件

<pre>{
    "$schema":"http://json-schema.org/draft-04/schema#"
}
</pre>

### 第五章 JSON中的安全问题 {#jump5}

*   跨站请求伪造(CSRF)

利用站点对用户浏览器的信任进行的攻击

*   跨站脚本攻击(XSS)

截取或将站点中的第三方代码替换为恶意脚本，来对站点进行注入攻击

*   CSRF和XSS对比

CSRF攻击仅包括利用信用机制的攻击，XSS则主要通过注入恶意代码来攻击

*   安全谨记

1.不适用顶级数组

2.合理使用GET和POST方法

3.使用JSON.parse()代替eval()

### 第六章 JavaScript中的XMLHttpRequest与Web API(简略) {#jump6}

*   JavaScript中的XMLHttpRequest负责在客户端发起请求，Web API负责在服务端返回响应

*   序列化和反序列化

序列化通常是将对象转化为文本的过程，例如：

<pre>var myJSON = JSON.stringify(myObject)
</pre>

反序列化通常是将文本转化为对象的过程，例如：

<pre>var myObject = JSON.parse(myJSON)
</pre>

*   共享规则

同源政策：处于安全考虑，浏览器仅会请求同一个域的脚本

跨域资源共享(CORS)：在响应头额外加一些其他属性，可使AJAX向公共API发送请求

JSON-P：带有padding的JSON，一种非标准解决方案

使用<script></script>标签，绕过同源限制，从不同域名的服务器上请求JSON

### 第七章 JSON与客户端框架(略) {#jump7}

### 第八章 JSON与NoSQL(略) {#jump8}

### 第九章 服务端的JSON {#jump9}

在服务端，可以通过将JSON反序列化为对象，进而运用在编程逻辑中，也可以将对象序列化为JSON格式

### 第十章 总结 {#jump10}

1.作为配置条件的JSON

常见的项目配置文件有INI,XML,JSON三种格式

JSON常见于前端或者Node的项目配置文件格式，一般命名为package.json

2.作为服务端的JSON

不仅是服务端的配置文件，还是作为URL请求的资源

3.作为文件的JSON

用作一种面向文档类型的数据库的文档
