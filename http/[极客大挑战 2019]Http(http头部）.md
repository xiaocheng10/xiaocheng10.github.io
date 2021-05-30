# http头部

## referer

是header的一部分，是浏览器告诉服务器网页的从哪里来的


## X-Forward-For

是一个http的扩展头部，用来记录离服务器最远的源ip和中间的代理ip

## user-agent

是用户代理，识别用户使用的操作系统及版本、cpu类型、浏览器及版本、浏览器渲染引擎、浏览器语言、浏览器插件

## 实例
通过查看页面源代码可以发现一个文件Secret.php，然后构造payload

/Secret.php

可以得到
![http1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/http1.webp)


因此，我们将referer改为https://www.Sycsecret.com
然后我们得到
![http2](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/http2.webp)


我们再将User-Agent里将火狐改为Syclover
我们得到
![http3](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/http3.webp)




然后我们在报文结尾加上X-Forward-For: 127.0.0.1
![http4](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/http4.webp)
