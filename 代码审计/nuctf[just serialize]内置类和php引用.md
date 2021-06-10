# 内置类stdClass的使用及php引用及unserialize的特性

## 内置类stdClass

stdClass类是php的内置类，是所有php类都继承这个类，所以stdClass类随时都可以被new，但是stdClass类里面没有方法。

代码例子
```
<?php

$a=new stdClass();
$a->name="xc";
echo (serialize($a));
?>
```

注意的是当stdClass内置类反序列化时，由于stdClass内置类不能用echo来返回字符串，所以需要用print_r或var_dump()输出数组的形式才可以反序列化成功


## php引用

在序列化中添加一个类的属性为
```
s:1:"f";R:2;
```
f这个变量可以继承前面属性的值，输入O:8:"stdClass":2:{s:4:"flag";S:4:"flag";s:1:"f";R:2;}，例子：
```
<?php

$obj=$_GET['obj'];
$obj=unserialize($obj);
print_r($obj);
?>
```
从输出结果可以看出f变量继承了前面第一个类的属性flag的值，而如果想继承前面第二个类的属性的值，则将R:2;改为R:3;即可，以此类推

## unserialize的一个特性

如果反序列化中的类中的代表字符串的s符号改为大写的字符S的话，就可以识别十六进制的内容，例子：
```
<?php
$obj=$_GET['obj'];
if(preg_match('/flag/i',$obj)){

echo "你错了";
}
else{
echo "你爱我";
print_r(unserialize($obj));
}
?>
```
此时可以通过用\66\6c\61\67代替flag进行绕过


## nuctf中的just serialzie实例

```
<?php
highlight_file(__FILE__);
include 'flag.php';
$obj=$_GET['obj'];
if(preg_match('/flag/i',$obj)){
die("?");
}
$obj=@unserialize($obj);
if($obj->flag==="flag"){
$obj->flag=$flag;
}
foreach($obj as $k=>$v){
if($k!="flag"){
echo $v;
}
}
```
可以通过使用stdClass内置类构造paylaod，且在flag属性上，将s改为大写S，从而使用十六进制对preg_match()正则过滤进行绕过，由于
```
foreach($obj as $k=>$v){
if($k!="flag"){
echo $v;
}
}
```
对类中的flag属性进行过滤，所以需要用到php引用来绕过，因此可以构造s:1:"f";R:2;来继承flag属性的值，从而让代码echo出来