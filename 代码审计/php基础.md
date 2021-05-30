---
title:php基础
data:2020-12-16
---

# PHP语法

## PHP脚本以<?php开始，以?>结束
```
<?php
echo "hello"
?>
```

## PHP变量

### 变量格式
变量名前加$，且变量名区分大小写

### 局部和全局变量
1. 全局变量：除了在函数外，可以被脚本中任何部分访问的变量
2. 局部变量：在PHP函数中
```
 <?php
$x=5; // 全局变量

function myTest()
{
    $y=10; // 局部变量
    echo "<p>测试函数内变量:<p>";
    echo "变量 x 为: $x";
    echo "<br>";
    echo "变量 y 为: $y";
} 

myTest();

echo "<p>测试函数外变量:<p>";
echo "变量 x 为: $x";
echo "<br>";
echo "变量 y 为: $y";
?> 
```
### PHP变量作用域
```
local
global
static
parameter
```
#### global
1. 用于函数内访问全局变量
```
<?php
$x=1;
$y=2

function myTest()
{
   glocal $x,$y;
    $y=$x+$y;
}
myTest();
echo $y;
?>
```
2. 也可用$GLOCAL[index]的形式，可以在函数内部访问
```
<?php
$x;
$y;
 
function myTest()
{
   $GLOBAL['y']=$GLOBAL['x']+$GLOBAL['y'];
}

myTest();
echo $y;
?>
```
#### static
 一个函数完成时，确保函数内的局部变量会保留下来
```
<?php
function myTest()
{
   static $x=0;
   echo $x;
   $x++;
   echo PHP_EOL;   //换行符
}

myTest();
myTest();
myTest();
?>
```

#### local
局部变量只能作用在函数内

#### 参数作用域
PHP中函数都有自己的作用域，如果在函数中没有声明之前，局部变量的优先级大于全局变量，声明之后，全局变量变为局部变量，如果
此时局部变量修改，则全局变量也随之修改
```
<?php
$a = 10;
$b = 5;
function test()
{
    $a = 15;
    $b = 5;
    $z = $a-$b;
    echo $z;
}

test();

function test1()
{
         global $a,$b;
         $a = 15;
         $b = 5;
         $z = $a-$b;
         echo PHP_EOL;
         echo $z;
}

test1();

function test2()
{
    global $a,$b;
    $z= $a-$b;
    echo PHP_EOL;
    echo $z;
}

test2();
?>
```

## EOF的使用
1. 以<<<EOF开始标志开始，以EOF结束标志结束
```
<?php
echo <<<EOF
        <h1>我的第一个标题</h1>
        <p>我的第一个段落。</p>
EOF;
// 结束需要独立一行且前后不能空格
?>
```
2. PHP定界符EOF就是按照原样，包括换行格式什么的，输出在其内部的东西，而且其中的任何特殊字符都不会被转义
    但双引号内的内容会有转义的效果
```
<?php
$name="变量会被解析";
$a=<<<EOF
$name<br><a>html格式会被解析</a><br/>双引号和Html格式外的其他内容都不会被解析
"双引号外所有被排列好的格式都会被保留"
"但是双引号内会保留转义符的转义效果,比如table:\t和换行：\n下一行"
EOF;
echo $a;
?>  
```





