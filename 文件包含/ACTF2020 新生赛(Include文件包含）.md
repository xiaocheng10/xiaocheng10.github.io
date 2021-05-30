## include函数的文件包含

## include函数
include函数是包含并运行指定的文件，如果有看到?file=，则一般要先想到include函数的文件包含，而且一般和伪协议一起使用

##实例
看到?file=，且题目提示include函数，因此可以想到文件包含和伪协议的联合使用

因此可以构造payload:
```
?file=php://filter/read=conver.base64-encode/resource=flag.php
```
![ACTF2020 新生赛1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/ACTF2020 新生赛1.jpg)

可以看到出现base64编码的数，然后进行解码即可得到flag

![ACTF2020 新生赛2](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/ACTF2020 新生赛2.jpg)

![ACTF2020 新生赛3](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/ACTF2020 新生赛3.jpg)