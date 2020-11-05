---
title: HTTP 缓存 
description: HTTP 缓存机制
tags:
- 网络
---

## HTTP 缓存

浏览器第一次向一个 web 服务器发送 http 请求后，服务器会返回请求的资源，并且在响应头中添加一些有关缓存的字段如：Cache-Control、Expires、Last-Modified、ETag、Date 等等. 之后浏览器再向该服务器请求该资源就可以视情况使用 '强缓存' 和 '协商缓存'.<br>

1. **强缓存**：浏览器直接从本地缓存中获取数据，不与服务器进行交互.<br>
2. **协商缓存**：浏览器发送请求到服务器，服务器判定是否可以使用本地缓存.<br>
3. **联系与区别**：两种缓存方式最终使用的都是本地缓存，前者无需与服务器交互，后者需要.<br>

**HTTP1.0** Last-Modified、Expries<br>
**HTTP1.1** ETag、Cache-Control<br>

***

## 强缓存

Cache-Control: max-age=0 表示有缓存也会重新请求.<br>

用户发起一个 http 请求后，浏览器发现本地已有请求的资源的缓存，便检查 Cache-Control 和 Expries.<br>

1. 查看缓存是否有 Cache-Control 的 s-maxage 或 max-age 指令，若有，则使用响应报文生成时间 Date + s-maxage/max-age 获得过期时间，再与当前时间进行对比（s-maxage 适用于多用户使用的公共缓存服务器）.<br>
2. 如果没有 Cache-Control 的 s-maxage 或 max-age 指令，则比较 Expries 中的过期时间与当前时间，Expries 是一个绝对时间.<br>

**<font color='red'>注意：在 HTTP/1.1 中，当首部字段 Cache-Control 有指定 s-maxage 或 max-age 指令，比起首部字段 Expries，会优先处理 s-maxage 或 max-age.</font>**

**另外几个 Cache-Control 的常用命令**<br>
1. no-cache：含义是不使用本地缓存<br>
2. no-store：禁用本地缓存<br>
3. public：表明其他用户也可以使用缓存，适用于公共缓存服务器的情况<br>
4. private：表明只有特定用户才能使用缓存，适用于公共缓存服务器的情况<br>

**<font color='red'>若缓存未过期，返回状态码为 200，则直接从本地读取缓存，这就完成了整个强缓存的过程.</font>**

***

## 协商缓存

当浏览器发现缓存过期后，需要与服务器协商查看本地缓存是否还能再用. 此时浏览器会判断缓存中是否有 ETag 或 Last-Modified 字段，如果没有，则发起一个请求，服务器根据请求返回的资源，如果有这两个字段.<br>
则在请求头中添加 If-None-Match 字段（有 ETag 字段的话添加），If-Modified-Since 字段（有 Last-Modified 字段的话添加）.<br>

**<font color='red'>注意：同时发送 If-None-Match 和 If-Modified-Since 字段，服务器只要比较 If-None-Match 字段和 ETag 内容是否符合即可，如果内容一致，服务器认为缓存仍然可用，则返回状态码 304，浏览器直接读取本地缓存，这就完成了协商缓存的过程. 如果内容不一样，则视情况返回其他状态码，并返回请求的资源.</font>**

***

#### ETag 和 If-None-Match

**二者的值都是服务器为每份资源分配的唯一标识字符串**<br>
1. 浏览器请求资源，服务器会在响应报文中加入 ETag 字段，资源更新时，服务器的 ETag 值会随之更新.<br>
2. 浏览器再次请求资源，会在请求头中添加 If-None-Match 字段，它的值就是上次响应报文中的 ETag 的值.<br>
3. 服务器会比对 ETag 和 If-None-Match 字段的值是否相同，如果不一致，服务器则接收请求，返回更新后的资源，如果一致，表明资源未更新，则返回状态码 304 的响应，即可以继续使用本地缓存. 同时不管 ETag 字段是否发生变化，此时响应头都会加上 ETag 字段.<br>

***

#### Last-Modified 和 If-Modified-Since

**二者的值都是 GMT 格式的时间字符串**<br>
1. 浏览器第一次向服务器请求资源后，服务器会在响应头中加上 Last-Modified 字段，表明该资源最后一次修改时间.<br>
2. 浏览器再次请求资源时，会在请求头报文中添加 If-None-Since 字段，它的值就是上次响应报文中的 Last-Modified 值.<br>
3. 服务器会对比 Last-Modified 和 If-None-Since 的值是否一致，如果不一致，服务器则会接收请求，返回更新后的资源，如果一致，则返回 304 状态码的响应，即可以继续使用本地缓存，但此时响应头便不会再添加 Last-Modified 字段.<br>

***

#### ETag 较 Last-Modified 的优势

1. 一些文件也许会周期性的更改，但是它的内容并不改变（仅仅改变的修改时间），这个时候我们并不希望客户端认为这个文件被修改了，而重新 GET.<br>
2. 某些文件修改非常频繁，比如在秒以下的时间内进行修改，（比如 1s 内修改了 N 次），If-None-Since 能检查到的粒度是 s 级的，这种修改无法判断（或者说 UNIX 记录 MTIME 只能精确到秒）.<br>
3. 某些服务器不能精确的得到文件的最后修改时间.<br>

**<font color='green'>这时，利用 ETag 能给更加精确的控制缓存，因为 ETag 是服务器自动生成的资源在服务器端的唯一标识符，都会生成 ETag 值. Last-Modified 与 ETag 是可以一起使用的，但服务器会优先验证 ETag.</font>**

***

## 参考

* 对下述文章表示感谢

https://segmentfault.com/a/1190000015816331
