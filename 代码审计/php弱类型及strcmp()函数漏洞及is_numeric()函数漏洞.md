#php弱类型

当一个字符串被当作是一个数值进行比较时，如果字符串中不含有“.”、”e“、”E“、”x“，而且数值在整型的范围内的，则这个字符串以int方式取值，且取最前面的字符前的数字，如果最前面的字符前没有数字，则返回为0。其它情况都以float的形式取值
```
<?php

var_dump("admin1"==0);
var_dump("1admin"==1);
?>
```

如果字符串里有e或E，则被当作科学计数法
```
<?php

var_dump("1e2"=="100");
var_dump("1E2"=="100");
?>
```
如果字符串里有x，则被当作为16进制数
```
<?php
var_dump("0x01"=="1");
?>
```
如果字符串里有.，则字符串以float的方式输出
```
<?php
var_dump("0.01"==0.01);
?>
```

#is_numeric()函数

is_numeric()函数是判断是否是数值或纯数值的字符串，是返回1，不是返回0
```
<?php
var_dump(is_numeric("400"));
var_dump(is_numeric(400));
?>
```
is_numeric()函数的漏洞：在输入的数值后加%00或%20，就可以绕过,而%20只适用于纯数字字符串，且要放在后面

```
<?php
var_dump(is_numeric("400%00"));
var_dump(is_numeric(400%20));
var_dump(is_numeric("400%20"));
var_dump(is_numeric(400%00));

?>
```




#strcmp()函数

strcmp()函数用来作为字符串的比较：

两个字符串相等，返回0

str1<str2，返回<0的数

str1>str2，返回>0的数


strcmp()函数的漏洞：比较的两个不是字符串会报错，且会返回0，所以可以构造数组或object绕过(适用于php5版本）
```
<?php

echo strcmp($_GET['ret'],'wer');

?>
```