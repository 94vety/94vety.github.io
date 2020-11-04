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

***

## 强缓存

Cache-Control: max-age=0 表示有缓存也会重新请求.<br>

用户发起一个 http 请求后，浏览器发现本地已有请求的资源的缓存，便检查 Cache-Control 和 Expries.<br>

1. 查看缓存是否有 Cache-Control 的 s-maxage 或 max-age 指令，若有，则使用响应报文生成时间 Date + s-maxage/max-age 获得过期时间，再与当前时间进行对比（s-maxage 适用于多用户使用的公共缓存服务器）.<br>
2. 如果没有 Cache-Control 的 s-maxage 或 max-age 指令，则比较 Expries 中的过期时间与当前时间，Expries 是一个绝对时间.<br>

**<font color='red'>注意：在 HTTP/1.1 中，当首部字段 Cache-Control 有指定 s-maxage 或 max-age 指令，比起首部字段 Expries，会优先处理 s-maxage 或 max-age.</font>**




