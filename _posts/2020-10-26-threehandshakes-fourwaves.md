---
title: 三次握手、四次挥手
description: TCP 的三次握手与四次挥手.
tags: 
---

## TCP 建立连接（三次握手）

![三次握手](https://s1.ax1x.com/2020/10/25/Be3IMj.png)

**第一次握手**<br>
&nbsp;&nbsp;&nbsp;&nbsp;将 SYN 置为 1，seq 随机生成一个数字 x，发送给服务端.

**第二次握手**<br>
&nbsp;&nbsp;&nbsp;&nbsp;将 ACK 置为 1，ack 为 x + 1，作为对客户端的应答，并将 SYN 置为 1，seq 随机生成一个数字 y，发送给客户端.

**第三次握手**<br>
&nbsp;&nbsp;&nbsp;&nbsp;将 ACK 置为 1，ack 为 y + 1，作为对服务端的应答，服务端接收到的数据包，确认完成之后，三次握手完毕，连接建立.

***

## TCP 断开连接（四次挥手）

![四次挥手](https://s1.ax1x.com/2020/10/25/Be8Pdx.png)

**第一次挥手**<br>
&nbsp;&nbsp;&nbsp;&nbsp;将 FIN 置为 1，seq 为 u（上一次对方传送过来的 ack 值），发送给被关闭方.

**第二次挥手**<br>
&nbsp;&nbsp;&nbsp;&nbsp;将 ACK 置为 1，ack 为 u + 1，向主动关闭房应答，并将 seq 置为 v，发送给主动关闭方.


**第三次挥手**<br>
&nbsp;&nbsp;&nbsp;&nbsp;被动关闭方将 FIN 置为 1，ACK 置为 1，ack 为 u + 1，seq 为 w，发送给主动关闭方.


**第四次挥手**<br>
&nbsp;&nbsp;&nbsp;&nbsp;主动关闭方，将 ACK 置为 1，ack 为 w + 1，向被动关闭方应答，并将 seq 设为 u + 1，发送给被动关闭方.

* 为何不能将 TCP 四次挥手的第二次与第三次合为一次？

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将 TCP 四次挥手中的第二次与第三次挥手合为一次. 也就是将 close-wait 状态的停留时间变为 0，然而，被动关闭方之所以存在 close-wait 状态，是因为被动关闭方可能还存在着需要发送给主动关闭方的，但是未发送的数据，如果存在着这些数据，那么这个状态的时间，就是用来发送这些数据的，所以，TCP 四次挥手的第二次和第三次挥手无法合并为一次.



