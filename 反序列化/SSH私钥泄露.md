---
title:SSH私钥泄露
data:2020-12-07
---

#信息探测
![密钥1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A51.jpg)


#探测大端口的信息
![密钥2](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A52.jpg)

roots里面存的是不允许搜索引擎所探测的文件名，以及允许被探测的文件名

![密钥3](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A53.jpg)

#针对隐藏页面分析
![密钥4](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A54.jpg)

SSH 为 Secure Shell 的缩写，由 IETF 的网络小组（Network Working Group）
所制定；SSH 为建立在应用层基础上的安全协议。SSH 是较可靠，专为远程登录
会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过
程中的信息泄露问题。SSH最初是UNIX系统上的一个程序，后来又迅速扩展到其
他操作平台。SSH在正确使用时可弥补网络中的漏洞。SSH客户端适用于多种平台。
几乎所有UNIX平台—包括HP-UX、Linux、AIX、Solaris、Digital UNIX、Irix，
以及其他平台，都可运行SSH。

![密钥5](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A55.jpg)

第一个为私钥文件，第二个文件（认证关键字文件）同理，第三个是公钥，是存储
在服务器端，进行远程访问所使用的，不需要下载

![密钥6](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A56.jpg)

打开认证关键字文件，可以发现，ssh通过密钥进行登录，用户名登录对应的主机名

![密钥7](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A57.jpg)

输入了simon尝试发现不行

#解密ssh密钥信息

![密钥8](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A58.jpg)

is列出文件和目录is/home(列出home下面的所有文件和目录

![密钥9](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A59.jpg)

![密钥10](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A510.jpg)

![密钥11](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/%E5%AF%86%E9%92%A511.jpg)


