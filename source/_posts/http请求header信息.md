---
title: http请求header信息
date: 2018-03-08 10:30:27
tags: http
---

<br>
### Referer
> 用于记录请求来源，当浏览器向web服务器发送请求的时候会带上Referer,告诉服务端，这个请求是从哪个页面链接来的,是由浏览器生成的，不是所有浏览器都会设置这个值

<!--more-->
作用:
1.页面统计
&nbsp;&nbsp;&nbsp;&nbsp;从a链接到b页面，b的服务端可以通过Referer来统计有多少是通过a跳转到b的。
2.防止盗链
&nbsp;&nbsp;&nbsp;&nbsp;服务端通过Referer可以获知请求来源，作出处理，不是合理的请求源可以禁止访问

Referer为空的情况：
1⃣️ 通过window.open打开的页面
2⃣️ 鼠标拖拽打开新窗口
3⃣️ 跨协议间提交请求
4⃣️ 从标签or浏览器地址输入地址打开
5⃣️ ie5.5+返回空字符串
6⃣️ 点击flash内部链接
7⃣️ h5有一个新的属性：noreferrer,，用户请求这个链接的资源时候，b不带上referer

问题：
Referer有好几种情况为空，并且可以伪造，所以单纯通过Referer来判断用户身份是不够的

如何伪造Referer:
WinHttpRequest可以模拟浏览器发送请求，服务端伪造
document.referere可以识别真是的referer

<br>
### Origin
> post请求才带上的，用于说明最初请求来源

<br>

### Cache-Control
> 让网站的发布者更加全面的控制网站，定位过期时间的设置，通过这个来定义缓存策略，控制谁在什么条件下可以缓存响应以及缓存多久，cache-control是在http1.1规范中定义的，取代了express

no-store: 禁止浏览器以及中间任何缓存存储的响应
no-cache: 表示必须先与服务器确认返回的响应是否发生了变化，每次请求都要重新验证
public:  允许被代理服务器缓存，响应可以被任何缓存区缓存
private: 对于单个用户的整个或者部分响应，不能别共享缓存处理
max-age: 允许获取的响应被重用的最长时间

如何客户端缓存和快速更新：
在资源内容改变的时候更换它的网址

参考网址： https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn
<br>

### Connection
> 告诉客户端和服务端通信是对于长链接如何处理

* keep-alive: 客户端到服务端的连接持续有效
* close：关闭长链接

<br>

### Host
> http1.1中才有的，不可以缺失，但是可以为空，客户端指定自己想访问的http服务器的域名/IP 地址和端口号。为了实现虚拟web服务器，实现负载均衡

### Accept
> 表达了发送端（客户端）希望接受的数据类型，这种类型用MIME类型来表示


<MIME_type>/<MIME_subtype>： 单一精确的MIME类型，例如text/html<br><MIME_type>/*: 一类类型，但是没有指明<br> * /*： 任意类型
<br>
### Accept-Encoding
> 客户端发给服务端，声明支持的编码格式

常见：
* Accept-Encoding: compress, gzip　//支持compress 和gzip类型 
* Accept-Encoding:　　　//默认是identity 
* Accept-Encoding: *　　//支持所有类型 
* Accept-Encoding: compress;q=0.5, gzip;q=1.0　//按顺序支持 gzip , compress 
* Accept-Encoding: gzip;q=1.0, identity; q=0.5, *;q=0    // 按顺序支持 gzip , identity 

如果accept-encoding中所有类型服务端都不能处理，就返回404

<br>

### Accept-Language
> 客户端告诉服务端理解的自然语言

* ch_CN --- 简体中文
* zh ---- 中文
* en --- 英语
* fr-FR ---- 法语

<br>

### User-Agent
> 浏览器信息

### access-control-allow-origin（响应头信息）
> 服务端响应客户端的时候，在Response header带上这个头部信息

access-control-allow-origin: * ---- 允许所有域名的脚本访问该资源
access-control-allow-origin: url ---- 仅允许url域名下的脚本访问该资源

<br>

### X-request-width
ps: 以X大头的一般不是http的协议，可能是某种技术出现而产生的，或者某个组织指定的
> 判断请求是传统的http请求还是ajax请求，也就是说一般ajax请求一般会带上这个头部信息

* xmlhttprequest： 是ajax请求
* null： 传统的同步请求