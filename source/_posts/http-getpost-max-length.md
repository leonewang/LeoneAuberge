---
title: "The Maximum Length Of HTTP Get/Post Request"
tags:
  - GET/POST
  - HTTP
  - Request
categories:
  - Summarize
date: 2015-09-23 23:27:56
---
> REF:
[http://blog.chinaunix.net/uid-26602509-id-4495786.html](http://blog.chinaunix.net/uid-26602509-id-4495786.html)
[https://developer.yahoo.com/performance/rules.html](https://developer.yahoo.com/performance/rules.html)

## GET 方法长度限制

Http Get 方法提交的数据大小长度并没有限制，HTTP 协议规范没有对 URL 长度进行限制。这个限制是特定的浏览器及服务器对它的限制。

如：IE 对 URL 长度的限制是 2083 字节(2K + 35)。

下面就是对各种浏览器和服务器的最大处理能力做一些说明.

<!-- more -->

Microsoft Internet Explorer (Browser)
IE 浏览器对 URL 的最大限制为 2,083 个字符，如果超过这个数字，提交按钮没有任何反应。

Firefox (Browser)
对于 Firefox 浏览器 URL 的长度限制为 65,536 个字符。

Safari (Browser)
URL 最大长度限制为 80,000 个字符。

Opera (Browser)
UR L最大长度限制为 190,000 个字符。

Google (chrome)
URL 最大长度限制为 8,182 个字符。

Apache (Server)
能接受最大 url 长度为 8,192 个字符。

Microsoft Internet Information Server(IIS)
能接受最大 url 的长度为 16,384 个字符。

通过上面的数据可知，为了让所有的用户都能正常浏览， URL 最好不要超过 IE 的最大长度限制(2083 个字符），当然，如果 URL 不直接提供给用户，而是提供给程序调用，这时的长度就只受 Web 服务器影响了。

注：对于中文的传递，最终会为 `urlencode` 后的编码形式进行传递，如果浏览器的编码为 UTF8 的话，一个汉字最终编码后的字符长度为 9 个字符。

## POST 方法长度限制

理论上讲，POST 是没有大小限制的。HTTP协议规范也没有进行大小限制，起限制作用的是服务器的处理程序的处理能力。
如：在 Tomcat 下取消 POST 大小的限制（Tomcat 默认 2M）；

打开 tomcat 目录下的 conf 目录，打开 server.xml 文件，修改

```
debug="0"
acceptCount="100"
connectionTimeout="20000"
disableUploadTimeout="true"
port="8080"
redirectPort="8443"
enableLookups="false"
minSpareThreads="25"
maxSpareThreads="75"
maxThreads="150"
maxPostSize="0"
URIEncoding="GBK"
```

增加部分 `maxPostSize="0"` (设为 0 是取消 POST 的大小限制)

## HTTP 协议从未限制GET/POST 的请求长度

> The HTTP protocol does not place any a priori limit on the length of a URI. Servers MUST be able to handle the URI of any resource they serve, and SHOULD be able to handle URIs of unbounded length if they provide GET-based forms that could generate such URIs. A server SHOULD return 414 (Request-URI Too Long) status if a URI is longer than the server can handle (see section 10.4.15).


> Note: Servers ought to be cautious about depending on URI lengths above 255 bytes, because some older client or proxy implementations might not properly support these lengths.

所谓的请求长度限制室友浏览器和 web 服务器决定和设置的，各种浏览器和 web 服务器的设定均不一样，这依赖于各个浏览器厂家的规定或者根据 web 服务器的处理能力来设定。

> The limit is in MSIE and Safari about 2KB, in Opera about 4KB and in Firefox about 8KB, (255 bytes if we count very old browsers) . We may thus assume that 8KB is the maximum possible length and that 2KB is a more affordable length to rely on at the server side and that 255 bytes is the safest length to assume that the entire URL will come in.

> If the limit is exceeded in either the browser or the server, most will just truncate the characters outside the limit without any warning. Some servers however may send a HTTP 414 error. If you need to send large data, then better use POST instead of GET. Its limit is much higher, but more dependent on the server used than the client. Usually up to around 2GB is allowed by the average webserver. This is also configureable somewhere in the server settings. The average server will display a server-specific error/exception when the POST limit is exceeded, usually as HTTP 500 error.

> HTTP 1.1 defines Status Code 414 Request-URI Too Long for the cases where a server-defined limit is reached. You can see further details on RFC 2616\. For the case of client-defined limits, there is no sense on the server returning something, because the server won't receive the request at all.

> The server is refusing to service the request because the Request-URI is longer than the server is willing to interpret. This rare condition is only likely to occur when a client has improperly converted a POST request to a GET request with long query information, when the client has descended into a URI "black hole" of redirection (e.g., a redirected URI prefix that points to a suffix of itself), or when the server is under attack by a client attempting to exploit security holes present in some servers using fixed-length buffers for reading or manipulating the Request-URI.

## GET VS POST

1.  多数浏览器对于 POST 采用两阶段发送数据的，先发送请求头，再发送请求体，即使参数再少再短，也会被分成两个步骤来发送（相对于 GET），也就是第一步发送 header 数据，第二步再发送 body 部分。HTTP 是应用层的协议，而在传输层有些情况 TCP 会出现两次连结的过程，HTTP 协议本身不保存状态信息，一次请求一次响应。对于 TCP 而言，通信次数越多反而靠性越低，能在一次连结中传输完需要的消息是最可靠的，尽量使用 GET 请求来减少网络耗时。如果通信时间增加，这段时间客户端与服务器端一直保持连接状态，在服务器侧负载可能会增加，可靠性会下降。

[YSLOW 法则中，为什么 yahoo 推荐用 GET 代替 POST ？](http://blogread.cn/it/article/6100?f=wb)

1. GET 参数是带在 URL 后面，传统 IE 中 URL 的最大可用长度为 2048 字符，其他浏览器对 URL 长度限制实现上有所不同。POST 请求无长度限制（目前理论上是这样的）。</p>
2. 由于 GET 参数是在 URL 后面，所以 GET 请求能够被 Cache，GET 请求能够缓存在浏览器的历史记录中，而 POST 不进行缓存，所以重要、私密数据的提交要避免使用 GET 请求。</p>
3. GET 提交的数据大小，不同浏览器的限制不同，一般在 2k-8K 之间，POST 提交数据比较大，大小靠服务器的设定值限制，而且某些数据只能用 POST 方法「携带」，比如 file。