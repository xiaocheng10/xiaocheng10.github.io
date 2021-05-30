---
title:php数据类型
data:2020-12-16
---

# PHP数据类型
String(字符串）
lnteger(整型）
Float(浮点型）
Boolean(布尔型）
array(数组型）
Object(对象）
NUll(空值)

## 字符串型（string）
可以将字符串放在单引号和双引号中
```
<?php 
$x = "Hello world!";
echo $x;
echo "<br>"; 
$x = 'Hello world!';
echo $x;
?>
```

## PHP 整型
1. 整型可以用三种格式表示：十进制、十六进制和八进制
2. var_dump()函数返回变量的数据类型和值，如果变量不存在，则输出NULL
```
<?php 
$x = 5985;
var_dump($x);
echo "<br>"; 
$x = -345; // 负数 
var_dump($x);
echo "<br>"; 
$x = 0x8C; // 十六进制数
var_dump($x);
echo "<br>";
$x = 047; // 八进制数
var_dump($x);
?>
```

## PHP浮点型
浮点数就是带小数部分的数字，或是指数形式
```
<?php 
$x = 10.365;
var_dump($x);
echo "<br>"; 
$x = 2.4e3;
var_dump($x);
echo "<br>"; 
$x = 8E-5;
var_dump($x);
?>
```

## PHP布尔型
布尔型可以是TURE或FALSE
```
$x=ture;
$y=false;
```

## PHP数组
数组可以在一个变量中存储多个值
```
<?php 
$cars=array("Volvo","BMW","Toyota");
var_dump($cars);
?>
```

## PHP对象
1. 对象数据类型也可以用于存储数据
2. 注意点：（1）必须使用class关键字声明类对象，类是可以包含属性和方法的结构
                （2）在类中定义数据类型，然后在实例化的类中使用数据类型
3. 下例的this是指向当前对象实例的指针，不指向任何其他对象或类
```
<?php
class Car
{
    var $color;
    function __construct($color="green") {
      $this->color = $color;
    }
    function what_color() {
      return $this->color;
    }
}

function print_vars($obj) {
   foreach (get_object_vars($obj) as $prop => $val) {
     echo "\t$prop = $val\n";
   }
}

// 实例一个对象
$herbie = new Car("white");

// 显示 herbie 属性
echo "\therbie: Properties\n";
print_vars($herbie);

?> 
```

## PHP NULL值
NULL值表示变量没有值，NULL是数据类型为NULL的值
```
<?php
$x="Hello world!";
$x=null;
var_dump($x);
?>
```





