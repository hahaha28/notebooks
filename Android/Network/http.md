# HTTP

> 转载自[这里](https://github.com/LRH1993/android_interview/blob/master/computer-networks/http.md)

## 一. HTTP 是什么

通俗来讲，他就是计算机通过网络进行通信的规则，是一个基于请求与响应，无状态的，应用层的协议，常基于TCP/IP协议传输数据。目前任何终端（手机，笔记本电脑。。）之间进行任何一种通信都必须按照Http协议进行，否则无法连接。

**四个基于：**

1. **请求与响应：** 客户端发送请求，服务器端响应数据

2. **无状态的：** 协议对于事务处理没有记忆能力，客户端第一次与服务器建立连接发送请求时需要进行一系列的安全认证匹配等，因此增加页面等待时间，当客户端向服务器端发送请求，服务器端响应完毕后，两者断开连接，也不保存连接状态，一刀两断！恩断义绝！从此路人！下一次客户端向同样的服务器发送请求时，由于他们之前已经遗忘了彼此，所以需要重新建立连接。

3. **应用层：** Http是属于应用层的协议，配合TCP/IP使用。

4. **TCP/IP：** Http使用TCP作为它的支撑运输协议。HTTP客户机发起一个与服务器的TCP连接，一旦连接建立，浏览器（客户机）和服务器进程就可以通过套接字接口访问TCP。

**针对无状态的一些解决策略：**

有时需要对用户之前的HTTP通信状态进行保存，比如执行一次登陆操作，在30分钟内所有的请求都不需要再次登陆。于是引入了Cookie技术。

HTTP/1.1想出了持久连接（HTTP keep-alive）方法。其特点是，只要任意一端没有明确提出断开连接，则保持TCP连接状态，在请求首部字段中的Connection: keep-alive即为表明使用了持久连接。等等还有很多……

HTTP报文是面向文本的，报文中的每一个字段都是一些ASCII码串，各个字段的长度是不确定的。HTTP有两类报文：请求报文和响应报文。

## 二. HTTP 请求报文

一个HTTP请求报文由请求行（request line）、请求头部（header）、空行和请求数据4个部分组成，下图给出了请求报文的一般格式。  

![](http://img.inaction.fun/static/62588.png)

### 1.请求行

请求行分为三个部分：请求方法、请求地址和协议版本

**请求方法**

HTTP/1.1 定义的请求方法有8种：GET、POST、PUT、DELETE、PATCH、HEAD、OPTIONS、TRACE。

最常的两种GET和POST，如果是RESTful接口的话一般会用到GET、POST、DELETE、PUT。

**请求地址**

URL：统一资源定位符，是一种自愿位置的抽象唯一识别方法。

组成：&lt;协议&gt;：//&lt;主机&gt;：&lt;端口&gt;/&lt;路径&gt;

**端口和路径有时可以省略（HTTP默认端口号是80）**

如下例：  

![](http://img.inaction.fun/static/58335.png)

**协议版本**

协议版本的格式为：HTTP/主版本号.次版本号，常用的有HTTP/1.0和HTTP/1.1

### 2.请求头部

请求头部为请求报文添加了一些附加信息，由“名/值”对组成，每行一对，名和值之间使用冒号分隔。

常见请求头如下： 

![](http://img.inaction.fun/static/14167.png)

请求头部的最后会有一个空行，表示请求头部结束，接下来为请求数据，这一行非常重要，必不可少。

### 3.请求数据

可选部分，比如GET请求就没有请求数据。

下面是一个POST方法的请求报文：

> POST 　/index.php　HTTP/1.1 　　 请求行  
> Host: localhost  
> User-Agent: Mozilla/5.0 \(Windows NT 5.1; rv:10.0.2\) Gecko/20100101 Firefox/10.0.2　　请求头  
> Accept: text/html,application/xhtml+xml,application/xml;q=0.9,_/_;q=0.8  
> Accept-Language: zh-cn,zh;q=0.5  
> Accept-Encoding: gzip, deflate  
> Connection: keep-alive  
> Referer:[http://localhost/](http://localhost/)  
> Content-Length：25  
> Content-Type：application/x-www-form-urlencoded  
> 　　空行  
> username=aa&password=1234　　请求数据

## 三. HTTP 响应报文

![](http://img.inaction.fun/static/37410.png)

HTTP响应报文主要由状态行、响应头部、空行以及响应数据组成。

### 1.状态行

由3部分组成，分别为：协议版本，状态码，状态码描述。

其中协议版本与请求报文一致，状态码描述是对状态码的简单描述，所以这里就只介绍状态码。

**状态码**

状态代码为3位数字。

 - 1xx：指示信息——表示请求已接收，继续处理。
 - 2xx：成功——表示请求已被成功接收、理解、接受。
 - 3xx：重定向——要完成请求必须进行更进一步的操作。
 - 4xx：客户端错误——请求有语法错误或请求无法实现。
 - 5xx：服务器端错误——服务器未能实现合法的请求。

下面列举几个常见的：

![](http://img.inaction.fun/static/42840.png)

### 2.响应头部

与请求头部类似，为响应报文添加了一些附加信息

常见响应头部如下：

![](http://img.inaction.fun/static/59179.png)

### 3.响应数据

用于存放需要返回给客户端的数据信息。

下面是一个响应报文的实例：

> HTTP/1.1 200 OK　　状态行  
> Date: Sun, 17 Mar 2013 08:12:54 GMT　　响应头部  
> Server: Apache/2.2.8 \(Win32\) PHP/5.2.5  
> X-Powered-By: PHP/5.2.5  
> Set-Cookie: PHPSESSID=c0huq7pdkmm5gg6osoe3mgjmm3; path=/  
> Expires: Thu, 19 Nov 1981 08:52:00 GMT  
> Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0  
> Pragma: no-cache  
> Content-Length: 4393  
> Keep-Alive: timeout=5, max=100  
> Connection: Keep-Alive  
> Content-Type: text/html; charset=utf-8  
> 　　空行
>
> &lt;html&gt;　　响应数据  
> &lt;head&gt;  
> &lt;title&gt;HTTP响应示例&lt;title&gt;  
> &lt;/head&gt;  
> &lt;body&gt;  
> Hello HTTP!  
> &lt;/body&gt;  
> &lt;/html&gt;

关于请求头部和响应头部的知识点很多，这里只是简单介绍。

通过以上步骤，数据已经传递完毕，HTTP/1.1会维持持久连接，但持续一段时间总会有关闭连接的时候，这时候据需要断开TCP连接。