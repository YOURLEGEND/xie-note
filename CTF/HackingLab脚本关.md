**目录**

[1：key又又找不到了](#t0)

[2：快速口算](#t1)

[3：这个题目是空的](#t2)

[5：逗比验证码第一期](#t3)

[6：逗比验证码第二期](#t4)

[7：逗比的验证码第三期（SESSION）](#t5)

[8：微笑一下就能过关了](#t6)

[9：逗比的手机验证码](#t7)

[10：基情燃烧的岁月](#t8)

[11：验证码识别](#t9)

[12：XSS基础关](#t10)

[13：XSS基础2:简单绕过](#t11)

[14：XSS基础3:检测与构造](#t12)

[15：Principle很重要的XSS](#t13)

* * *

HackingLab地址：[http://hackinglab.cn/ShowQues.php?type=scripts](http://hackinglab.cn/ShowQues.php?type=scripts)

### 1：key又又找不到了

小明这次哭了，key又找不到了！！！ key啊，你究竟藏到了哪里，为什么我看到的页面上都没有啊！！！！！！

通关地址：[http://lab1.xseclab.com/xss1\_30ac8668cd453e7e387c76b132b140bb/index.php](http://lab1.xseclab.com/xss1_30ac8668cd453e7e387c76b132b140bb/index.php)

**Poc：**

这道题考察的是

访问通关地址

![](https://img-blog.csdnimg.cn/20191024110850902.png)

![](https://img-blog.csdnimg.cn/20191024110906896.png)

使用bp抓包，发现脚本控制网页跳转，所以我们看不到key。最后得到key：yougotit\_script\_now

![](https://img-blog.csdnimg.cn/20191024110811793.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 2：快速口算

小明要参加一个高技能比赛，要求每个人都要能够快速口算四则运算，2秒钟之内就能够得到结果，但是小明就是一个小学生没有经过特殊的培训，那小明能否通过快速口算测验呢？

通关地址：[http://lab1.xseclab.com/xss2\_0d557e6d2a4ac08b749b61473a075be1/index.php](http://lab1.xseclab.com/xss2_0d557e6d2a4ac08b749b61473a075be1/index.php)

**Poc：**

这道题考察的是快速编写python脚本的能力

访问通关地址，需要在2秒内快速算出答案并且提交，所以不得不用脚本来算题和提交

![](https://img-blog.csdnimg.cn/20191024111116666.png)

以下是编写的python脚本

```
# -*- coding: utf-8 -*-      
#环境：python2.7      
"""      
Created on Wed Oct 23 22:51:54 2019      
@author: 小谢      
"""      
import re      
import requests       1
s=requests.Session()      1
url="http://lab1.xseclab.com/xss2_0d557e6d2a4ac08b749b61473a075be1/index.php"      1
r=s.get(url)      1
res=unicode(r.content,"utf-8").encode("gbk")      1
#print(res)      1
num = re.findall('([0-9].*?)=', res)[0]      1
print ('当前获取到需要口算的表达式及计算结果为:%s=%d' % (num, eval(num)))      1
r=s.post(url,data={'v':eval(num)})      1
print(re.findall(">(.*)<",r.content)[0])
```


运行脚本，得到key：123iohHKHJ%^&\*(jkh 

![](https://img-blog.csdnimg.cn/20191024111254665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

这道题需要注意的是你获得运算式的session和提交的session必须是同一个session。

### 3：这个题目是空的

Tips:这个题目真不是随便设置的。 什么才是空的呢？ 通关地址：没有，请直接提交答案(小写即可)

**poc：**

这道题考察的是选手对于编程语言中空值的了解

得到flag：null

4：怎么就是不弹出key呢？

提交说明：提交前14个字符即可过关 

通关地址：[http://lab1.xseclab.com/xss3\_5dcdde90bbe55087eb3514405972b1a6/index.php](http://lab1.xseclab.com/xss3_5dcdde90bbe55087eb3514405972b1a6/index.php)

**Poc：**

这题考察的是js的驾驭能力

访问通关地址

![](https://img-blog.csdnimg.cn/20191024141028752.png)

右键查看源代码发现变量a其实是一个匿名函数，而且js里面也禁用了一些常用的弹出对话框的函数

![](https://img-blog.csdnimg.cn/20191024141051186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

由于js是基于客户端浏览器的，所以我们把代码复制下来，去掉前后的<script></script>，美化成如下

```
var a = function () {      
  var b = function (p, a, c, k, e, r) {      
    e = function (c) {      
      return (c < a ? '' : e(parseInt(c / a))) + ((c = c % a) > 35 ? String.fromCharCode(c + 29)  : c.toString(36))      
    };      
    if (!''.replace(/^/, String)) {      
      while (c--) r[e(c)] = k[c] || e(c);      
      k = [      
        function (e) {      1
          return r[e]      1
        }      1
      ];      1
      e = function () {      1
        return '\\w+'      1
      };      1
      c = 1      1
    };      1
    while (c--) if (k[c]) p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]);      1
    return p      2
  }('1s(1e(p,a,c,k,e,r){e=1e(c){1d(c<a?\'\':e(1p(c/a)))+((c=c%a)>1q?1f.1j(c+1k):c.1n(1o))};1g(!\'\'.1h(/^/,1f)){1i(c--)r[e(c)]=k[c]||e(c);k=[1e(e){1d r[e]}];e=1e(){1d\'\\\\w+\'};c=1};1i(c--)1g(k[c])p=p.1h(1l 1m(\'\\\\b\'+e(c)+\'\\\\b\',\'g\'),k[c]);1d p}(\'Y(R(p,a,c,k,e,r){e=R(c){S(c<a?\\\'\\\':e(18(c/a)))+((c=c%a)>17?T.16(c+15):c.12(13))};U(!\\\'\\\'.V(/^/,T)){W(c--)r[e(c)]=k[c]||e(c);k=[R(e){S r[e]}];e=R(){S\\\'\\\\\\\\w+\\\'};c=1};W(c--)U(k[c])p=p.V(Z 11(\\\'\\\\\\\\b\\\'+e(c)+\\\'\\\\\\\\b\\\',\\\'g\\\'),k[c]);S p}(\\\'G(B(p,a,c,k,e,r){e=B(c){A c.L(a)};E(!\\\\\\\'\\\\\\\'.C(/^/,F)){D(c--)r[e(c)]=k[c]||e(c);k=[B(e){A r[e]}];e=B(){A\\\\\\\'\\\\\\\\\\\\\\\\w+\\\\\\\'};c=1};D(c--)E(k[c])p=p.C(I J(\\\\\\\'\\\\\\\\\\\\\\\\b\\\\\\\'+e(c)+\\\\\\\'\\\\\\\\\\\\\\\\b\\\\\\\',\\\\\\\'g\\\\\\\'),k[c]);A p}(\\\\\\\'t(h(p,a,c,k,e,r){e=o;n(!\\\\\\\\\\\\\\\'\\\\\\\\\\\\\\\'.m(/^/,o)){l(c--)r[c]=k[c]||c;k=[h(e){f r[e]}];e=h(){f\\\\\\\\\\\\\\\'\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\w+\\\\\\\\\\\\\\\'};c=1};l(c--)n(k[c])p=p.m(q s(\\\\\\\\\\\\\\\'\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\b\\\\\\\\\\\\\\\'+e(c)+\\\\\\\\\\\\\\\'\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\b\\\\\\\\\\\\\\\',\\\\\\\\\\\\\\\'g\\\\\\\\\\\\\\\'),k[c]);f p}(\\\\\\\\\\\\\\\'1 3="6";1 4="7";1 5="";8(1 2=0;2<9;2++){5+=3+4}\\\\\\\\\\\\\\\',j,j,\\\\\\\\\\\\\\\'|u|i|b|c|d|v|x|y|j\\\\\\\\\\\\\\\'.z(\\\\\\\\\\\\\\\'|\\\\\\\\\\\\\\\'),0,{}))\\\\\\\',H,H,\\\\\\\'|||||||||||||||A||B||M||D|C|E|F||I||J|G|N|O||P|Q|K\\\\\\\'.K(\\\\\\\'|\\\\\\\'),0,{}))\\\',X,X,\\\'||||||||||||||||||||||||||||||||||||S|R|V|W|U|T|Y|13|Z|11|14|12|10|19|1a|1b|1c\\\'.14(\\\'|\\\'),0,{}))\',1t,1u,\'|||||||||||||||||||||||||||||||||||||||||||||||||||||1e|1d|1f|1g|1h|1i|1v|1s|1l||1m|1n|1o|1r|1k|1j|1q|1p|1w|1x|1y|1z\'.1r(\'|\'),0,{}))', 62, 98, '|||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||return|function|String|if|replace|while|fromCharCode|29|new|RegExp|toString|36|parseInt|35|split|eval|62|75|53|var|slakfj|teslkjsdflk|for'.split('|'), 0, {      2
  });      2
  var d = eval(b);      2
  alert('key is first 14 chars' + '\n'+d.substr(0,14));      2
}()
```


将这段代码复制到新建页面的console控制台，回车即可弹出，得到key： slakfjteslkjsd

![](https://img-blog.csdnimg.cn/20191024141314997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 5：逗比验证码第一期

逗比的验证码，有没有难道不一样吗？

通关地址：[http://lab1.xseclab.com/vcode1\_bcfef7eacf7badc64aaf18844cdb1c46/index.php](http://lab1.xseclab.com/vcode1_bcfef7eacf7badc64aaf18844cdb1c46/index.php)

**Poc：**

这题考察的是选手对于爆破的掌握

访问通关地址，输入任意的4位数字进行登录，返回pwd error。重放，返回的还是 pwd error。由此可知验证码失效

![](https://img-blog.csdnimg.cn/20191024141826848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

由于验证码失效，所以我们可以对pwd参数进行爆破，最后爆破得到pwd为1238，得到key：LJLJL789sdf#@sd

![](https://img-blog.csdnimg.cn/20191024141940816.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 6：逗比验证码第二期

验证便失效的验证码

通关地址：[http://lab1.xseclab.com/vcode2\_a6e6bac0b47c8187b09deb20babc0e85/index.php](http://lab1.xseclab.com/vcode2_a6e6bac0b47c8187b09deb20babc0e85/index.php)

**Poc：**

这题考察的是选手对于图形验证码的绕过，以及爆破

访问通关地址，输入任意的4位数字进行登录，返回pwd error

![](https://img-blog.csdnimg.cn/20191024141826848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

重放，返回的是vcode error。可知验证码验证一次即失效了

![](https://img-blog.csdnimg.cn/20191024142518558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

尝试删除vcode参数的值，重放，返回pwd error。

![](https://img-blog.csdnimg.cn/20191024142811351.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

可知，只要不给vcode赋值，就不会验证vcode的正确与否。于是，删除vcode的值，对pwd参数进行爆破。最后爆破得到pwd= 1228，得到key：LJLJL789ss33fasvxcvsdf#@sd

![](https://img-blog.csdnimg.cn/20191024142900313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 7：逗比的验证码第三期（SESSION）

尼玛，验证码怎么可以这样逗比。。验证码做成这样，你家里人知道吗？

通关地址：[http://lab1.xseclab.com/vcode3\_9d1ea7ad52ad93c04a837e0808b17097/index.php](http://lab1.xseclab.com/vcode3_9d1ea7ad52ad93c04a837e0808b17097/index.php)

**Poc：**

这题考察的是选手对于图形验证码的绕过，以及爆破

这题和第6题解题是一样的，也不知道为啥要提示session。用第6题的思路解题，爆破，得到pwd=1298，得到key：LJLJLfuckvcodesdf#@sd

![](https://img-blog.csdnimg.cn/20191024143504842.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 8：微笑一下就能过关了

尼玛，碰到这样的题我能笑得出来嘛... 

通关地址：[http://lab1.xseclab.com/base13\_ead1b12e47ec7cc5390303831b779d47/index.php](http://lab1.xseclab.com/base13_ead1b12e47ec7cc5390303831b779d47/index.php)

**Poc：**

这道题考察的是

未完待续。。

### 9：逗比的手机验证码

你的手机号码是13388886666，验证码将会以弹窗的形式给出

通关地址：[http://lab1.xseclab.com/vcode5\_mobi\_5773f3def9f77f439e058894cefc42a8/](http://lab1.xseclab.com/vcode5_mobi_5773f3def9f77f439e058894cefc42a8/)

**Poc：**

这道题考察的是对手机验证码处的逻辑漏洞

访问通关地址，点击获取验证码弹出手机验证码是9038

![](https://img-blog.csdnimg.cn/20191024145743140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

输入9038登录，提示 please login as 13388886667

![](https://img-blog.csdnimg.cn/20191024145816384.png)

于是将手机号码改为13388886667，重新获取手机验证码，结果提示

![](https://img-blog.csdnimg.cn/20191024145919627.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是尝试利用13388886666获取验证码，然后登录的时候用13388886667，结果竟然得到key：LJLJLGod!@@sd

![](https://img-blog.csdnimg.cn/20191024150008671.png)

### 10：基情燃烧的岁月

Tips:你是一名黑客，你怀疑你的“（男/女）闺蜜”的出轨了，你要登陆TA手机的网上营业厅查看详单，一探究竟！ 闺蜜手机号码:13388886666

通关地址：[http://lab1.xseclab.com/vcode6\_mobi\_b46772933eb4c8b5175c67dbc44d8901/](http://lab1.xseclab.com/vcode6_mobi_b46772933eb4c8b5175c67dbc44d8901/)

**Poc：**

这道题考察的是对手机验证码的爆破

访问通关地址，点击获取手机验证码，返回如图

![](https://img-blog.csdnimg.cn/20191024150323814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

很自然的想到了对验证码的爆破，结果得到了下面的结果

![](https://img-blog.csdnimg.cn/20191024150405386.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

继续对 13399999999 进行验证码爆破，得到flag为：{LKK8\*(!@@sd}

![](https://img-blog.csdnimg.cn/20191024150543408.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 11：验证码识别

Tips:验证码依然是3位数

通关地址：[http://lab1.xseclab.com/vcode7\_f7947d56f22133dbc85dda4f28530268/index.php](http://lab1.xseclab.com/vcode7_f7947d56f22133dbc85dda4f28530268/index.php)

**Poc：**

这道题考察是的对图形验证码的破解和对手机验证码的爆破

访问通关地址点击获取验证码，但是并不知道验证码是多少，用bp抓包，返回包中也没有验证码

![](https://img-blog.csdnimg.cn/20191024151237569.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191024151331528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是输入任意的手机验证码，输入正确的图形验证码登录，用bp抓包，提示user\_code or mobi\_code error

![](https://img-blog.csdnimg.cn/20191024151431994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

重放，提示验证码失效。由此可知，登录每次，验证码都会刷新

![](https://img-blog.csdnimg.cn/20191024151528288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20191024151626398.png)

所以现在就需要同时爆破手机验证码和破解图形验证码了

未完待续。。

### 12：XSS基础关

XSS基础:很容易就可以过关

通关地址：[http://lab1.xseclab.com/realxss1\_f123c17dd9c363334670101779193998/index.php](http://lab1.xseclab.com/realxss1_f123c17dd9c363334670101779193998/index.php)

Poc：

这题考察简单的XSS payload

输入<script>alert(1)</script>，提示弹出 HackingLab

![](https://img-blog.csdnimg.cn/20191024153615869.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

于是输入 <script>alert(HackingLab)</script>，得到key：myxssteststart!

![](https://img-blog.csdnimg.cn/20191024153807794.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 13：XSS基础2:简单绕过

很容易就可以过关.

通关地址：[http://lab1.xseclab.com/realxss2\_bcedaba7e8618cdfb51178765060fc7d/index.php](http://lab1.xseclab.com/realxss2_bcedaba7e8618cdfb51178765060fc7d/index.php)

**Poc：**

这题考察的是对于XSS的绕过

这道题过滤了 <script></script> 标签，所以我们可以输入其他标签。输入 <img src=1 οnerrοr=alert(HackingLab)>

得到key：xss2test2you

![](https://img-blog.csdnimg.cn/20191024154419639.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

### 14：XSS基础3:检测与构造

XSS基础3:检测与构造  
Tips:不是很难

通关地址：[http://lab1.xseclab.com/realxss3\_9b28b0ff93d0b0099f5ac7f8bad3f368/index.php](http://lab1.xseclab.com/realxss3_9b28b0ff93d0b0099f5ac7f8bad3f368/index.php)

**Poc：**

这题考察的是对于XSS的构造与绕过

访问通关地址，输入11，查看源代码

![](https://img-blog.csdnimg.cn/20191024154725800.png)

![](https://img-blog.csdnimg.cn/20191024154747584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

通过几次尝试构造，发现其对我们输入的数据过滤了 < ，导致我们输入的所有在 < 后面的数据都被删除了，并且过滤了 <script> 、alert等标签。

未完待续。。

### 15：Principle很重要的XSS

原理/原则/理念很重要.....不是所有的xss都叫特仑苏.. ^\_^  
Take it easy!

通关地址：[http://lab1.xseclab.com/realxss4\_9bc1559999a87a9e0968ad1d546dfe33/index.php](http://lab1.xseclab.com/realxss4_9bc1559999a87a9e0968ad1d546dfe33/index.php)

**Poc：**

这题考察的是对于XSS的绕过

未完待续。。

参考文章：[CTF writeup 1\_网络安全实验室](https://blog.csdn.net/qq_30637197/article/details/52933130)

                  [网络安全攻防实验室通关教程-脚本关](https://blog.csdn.net/qq1124794084/article/details/78280186)

                  [网络信息安全实验室----脚本关](https://www.jianshu.com/p/20475700f7cc)