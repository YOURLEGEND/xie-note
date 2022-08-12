**目录**

[CSRF分类](#t0 "CSRF分类")

[GET型：](#t1 "GET型：")

[POST型：](#t2 "POST型：")

[CSRF攻击原理及过程：](#t3 "CSRF攻击原理及过程：")

[CSRF攻击实例：](#t4 "CSRF攻击实例：")

[CSRF攻击的防御：](#t5 "CSRF攻击的防御：")

[（1）验证 HTTP Referer 字段](#t6 "（1）验证 HTTP Referer 字段        ")        

[（2）在请求地址中添加 token 并验证(Anti-CSRF token)](#t7 "（2）在请求地址中添加 token 并验证(Anti-CSRF token)        ")        

[（3）在 HTTP 头中自定义属性并验证](#t8 "（3）在 HTTP 头中自定义属性并验证        ")        

[CSRF漏洞的挖掘](#t9 "CSRF漏洞的挖掘")

[使用BurpSuite快速生成CSRF POC](#t10 "使用BurpSuite快速生成CSRF POC")

* * *

关于Cookie和Session，传送门——> [Cookie和Session的区别](https://blog.csdn.net/qq_36119192/article/details/84977902 "Cookie和Session的区别")

CSRF分类
------

**CSRF(Cross-Site Request Forgery)**，跟XSS漏洞攻击一样，存在巨大的危害性。

你可以这么来理解：攻击者盗用了你的身份，以你的名义发送恶意请求，对服务器来说这个请求是完全合法的，但是却完成了攻击者所期望的一个操作，比如以你的名义发送邮件、发消息，盗取你的账号，添加系统管理员，甚至于购买商品、虚拟货币转账等

### GET型：

如果一个网站某个地方的功能，比如用户修改邮箱是通过GET请求进行修改的。如：/user.php?id=1&email=123@163.com  ，这个链接的意思是用户id=1将邮箱修改为123@163.com。 当我们把这个链接修改为 /user.php?id=1&email=abc@163.com ，然后通过各种手段发送给被攻击者，诱使被攻击者点击我们的链接，当用户刚好在访问这个网站，他同时又点击了这个链接，那么悲剧发生了。这个用户的邮箱被修改为 abc@163.com 了

### POST型：

在普通用户的眼中，点击网页->打开试看视频->购买视频是一个很正常的一个流程。可是在攻击者的眼中可以算正常，但又不正常的，当然不正常的情况下，是在开发者安全意识不足所造成的。攻击者在购买处抓到购买时候网站处理购买(扣除)用户余额的地址。比如：/coures/user/handler/25332/buy.php 。通过提交表单，buy.php处理购买的信息，这里的25532为视频ID。那么攻击者现在构造一个链接，链接中包含以下内容。

```
<form action=/coures/user/handler/25332/buy method=POST>      
<input type="text" name="xx" value="xx" />      
</form>      
<script> document.forms[0].submit(); </script>
```


当用户访问该页面后，表单会自动提交，相当于模拟用户完成了一次POST操作，自动购买了id为25332的视频，从而导致受害者余额扣除。

CSRF攻击原理及过程：
------------

1.  用户C打开浏览器，访问受信任网站A，输入用户名和密码请求登录网站A；
2.  在用户信息通过验证后，网站A产生Cookie信息并返回给浏览器，此时用户登录网站A成功，可以正常发送请求到网站A；
3.  用户未退出网站A之前，在同一浏览器中，打开一个TAB页访问网站B；
4.  网站B接收到用户请求后，返回一些攻击性代码，并发出一个请求要求访问第三方站点A
5.  浏览器在接收到这些攻击性代码后，根据网站B的请求，在用户不知情的情况下携带Cookie信息，向网站A发出请求。网站A并不知道该请求其实是由B发起的，所以会根据用户C的Cookie信息以C的权限处理该请求，导致来自网站B的恶意代码被执行。 

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE1LmNuYmxvZ3MuY29tL2Jsb2cvODM2MDQ5LzIwMTYwMy84MzYwNDktMjAxNjAzMjIyMTQ3NDc5MDEtMTU0ODE1Mzk3OC5qcGc?x-oss-process=image/format,png)

CSRF攻击实例：
---------

1.  受害者 Bob 在银行有一笔存款，通过对银行的网站发送请求 http://bank.example/withdraw?account=bob&amount=1000000&for=bob2 可以使 Bob 把 1000000 的存款转到 bob2 的账号下。通常情况下，该请求发送到网站后，服务器会先验证该请求是否来自一个合法的 session，并且该 session 的用户 Bob 已经成功登陆。
2.  黑客 Mallory 自己在该银行也有账户，他知道上文中的 URL 可以把钱进行转帐操作。Mallory 可以自己发送一个请求给银行：http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory。但是这个请求来自 Mallory 而非 Bob，他不能通过安全认证，因此该请求不会起作用。
3.  这时，Mallory 想到使用 CSRF 的攻击方式，他先自己做一个网站，在网站中放入如下代码： src=”http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory ”，并且通过广告等诱使 Bob 来访问他的网站。当 Bob 访问该网站时，上述 url 就会从 Bob 的浏览器发向银行，而这个请求会附带 Bob 浏览器中的 cookie 一起发向银行服务器。大多数情况下，该请求会失败，因为他要求 Bob 的认证信息。但是，如果 Bob 当时恰巧刚访问他的银行后不久，他的浏览器与银行网站之间的 session 尚未过期，浏览器的 cookie 之中含有 Bob 的认证信息。这时，悲剧发生了，这个 url 请求就会得到响应，钱将从 Bob 的账号转移到 Mallory 的账号，而 Bob 当时毫不知情。等以后 Bob 发现账户钱少了，即使他去银行查询日志，他也只能发现确实有一个来自于他本人的合法请求转移了资金，没有任何被攻击的痕迹。而 Mallory 则可以拿到钱后逍遥法外

**CSRF攻击的防御：**
--------------

### （1）验证 HTTP Referer 字段        

根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址。在通常情况下，访问一个安全受限页面的请求来自于同一个网站，比如需要访问 http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory，用户必须先登陆 bank.example，然后通过点击页面上的按钮来触发转账事件。这时，该转帐请求的 Referer 值就会是转账按钮所在的页面的 URL，通常是以 bank.example 域名开头的地址。而如果黑客要对银行网站实施 [CSRF](https://so.csdn.net/so/search?q=CSRF&spm=1001.2101.3001.7020) 攻击，他只能在他自己的网站构造请求，当用户通过黑客的网站发送请求到银行时，该请求的 Referer 是指向黑客自己的网站。因此，要防御 CSRF 攻击，银行网站只需要对于每一个转账请求验证其 Referer 值，如果是以 bank.example 开头的域名，则说明该请求是来自银行网站自己的请求，是合法的。如果 Referer 是其他网站的话，则有可能是黑客的 CSRF 攻击，拒绝该请求。        

这种方法的显而易见的好处就是简单易行，网站的普通开发人员不需要操心 CSRF 的漏洞，只需要在最后给所有安全敏感的请求统一增加一个拦截器来检查 Referer 的值就可以。特别是对于当前现有的系统，不需要改变当前系统的任何已有代码和逻辑，没有风险，非常便捷。      

然而，这种方法并非万无一失。Referer 的值是由浏览器提供的，虽然 HTTP 协议上有明确的要求，但是每个浏览器对于 Referer 的具体实现可能有差别，并不能保证浏览器自身没有安全漏洞。使用验证 Referer 值的方法，就是把安全性都依赖于第三方（即浏览器）来保障，从理论上来讲，这样并不安全。事实上，对于某些浏览器，比如 IE6 或 FF2，目前已经有一些方法可以篡改 Referer 值。如果 bank.example 网站支持 IE6 浏览器，黑客完全可以把用户浏览器的 Referer 值设为以 bank.example 域名开头的地址，这样就可以通过验证，从而进行 CSRF 攻击。 即便是使用最新的浏览器，黑客无法篡改 Referer 值，这种方法仍然有问题。因为 Referer 值会记录下用户的访问来源，有些用户认为这样会侵犯到他们自己的隐私权，特别是有些组织担心 Referer 值会把组织内网中的某些信息泄露到外网中。因此，用户自己可以设置浏览器使其在发送请求时不再提供 Referer。当他们正常访问银行网站时，网站会因为请求没有 Referer 值而认为是 CSRF 攻击，拒绝合法用户的访问。

![](https://img-blog.csdn.net/2018092315495694?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### （2）在请求地址中添加 token 并验证(Anti-CSRF token)        

 CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于 cookie 中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的 cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。        

这种方法要比检查 Referer 要安全一些，token 可以在用户登陆后产生并放于 session 之中，然后在每次请求时把 token 从 session 中拿出，与请求中的 token 进行比对，但这种方法的难点在于如何把 token 以参数的形式加入请求。对于 GET 请求，token 将附在请求地址之后，这样 URL 就变成 http://url?csrftoken=tokenvalue。 而对于 POST 请求来说，要在 form 的最后加上 ，这样就把 token 以参数的形式加入请求了。但是，在一个网站中，可以接受请求的地方非常多，要对于每一个请求都加上 token 是很麻烦的，并且很容易漏掉，通常使用的方法就是在每次页面加载时，使用 javascript 遍历整个 dom 树，对于 dom 中所有的 a 和 form 标签后加入 token。这样可以解决大部分的请求，但是对于在页面加载之后动态生成的 html 代码，这种方法就没有作用，还需要程序员在编码时手动添加 token。          

该方法还有一个缺点是难以保证 token 本身的安全。特别是在一些论坛之类支持用户自己发表内容的网站，黑客可以在上面发布自己个人网站的地址。由于系统也会在这个地址后面加上 token，黑客可以在自己的网站上得到这个 token，并马上就可以发动 CSRF 攻击。为了避免这一点，系统可以在添加 token 的时候增加一个判断，如果这个链接是链到自己本站的，就在后面添加 token，如果是通向外网则不加。不过，即使这个 csrftoken 不以参数的形式附加在请求之中，黑客的网站也同样可以通过 Referer 来得到这个 token 值以发动 CSRF 攻击。这也是一些用户喜欢手动关闭浏览器 Referer 功能的原因。

### （3）在 HTTP 头中自定义属性并验证        

这种方法也是使用 token 并进行验证，和上一种方法不同的是，这里并不是把 token 以参数的形式置于 HTTP 请求之中，而是把它放到 HTTP 头中自定义的属性里。通过 XMLHttpRequest 这个类，可以一次性给所有该类请求加上 CSRFToken 这个 HTTP 头属性，并把 token 值放入其中。这样解决了上种方法在请求中加入 token 的不便，同时，通过 XMLHttpRequest 请求的地址不会被记录到浏览器的地址栏，也不用担心 token 会透过 Referer 泄露到其他网站中去。        

然而这种方法的局限性非常大。XMLHttpRequest 请求通常用于 Ajax 方法中对于页面局部的异步刷新，并非所有的请求都适合用这个类来发起，而且通过该类请求得到的页面不能被浏览器所记录下，从而进行前进，后退，刷新，收藏等操作，给用户带来不便。另外，对于没有进行 CSRF 防护的遗留系统来说，要采用这种方法来进行防护，要把所有请求都改为 XMLHttpRequest 请求，这样几乎是要重写整个网站，这代价无疑是不能接受的。关于 XMLHttpRequest，传送门——>[异步访问技术Ajax(XMLHttpRequest)](https://blog.csdn.net/qq_36119192/article/details/84196633 "异步访问技术Ajax(XMLHttpRequest)")

CSRF漏洞的挖掘
---------

1：最简单的方法就是抓取一个正常请求的数据包，如果没有Referer字段和token，那么极有可能存在CSRF漏洞

2：如果有Referer字段，但是去掉Referer字段后再重新提交，如果该提交还有效，那么基本上可以确定存在CSRF漏洞。

3：随着对CSRF漏洞研究的不断深入，不断涌现出一些专门针对CSRF漏洞进行检测的工具，如CSRFTester，CSRF Request Builder等。 以CSRFTester工具为例，CSRF漏洞检测工具的测试原理如下：使用CSRFTester进行测试时，首先需要抓取我们在浏览器中访问过的所有链接以及所有的表单等信息，然后通过在CSRFTester中修改相应的表单等信息，重新提交，这相当于一次伪造客户端请求。如果修改后的测试请求成功被网站服务器接受，则说明存在CSRF漏洞，当然此款工具也可以被用来进行CSRF攻击。

使用BurpSuite快速生成CSRF POC
-----------------------

当我们发现一个页面存在CSRF漏洞后，我们如何能快速的生成CSRC的攻击代码呢？我们可以借助BurpSuite这个强大的工具来完成。

抓取存在CSRF漏洞的数据包，右键，Engagement tools——>Generate CSRF PoC

![](https://img-blog.csdnimg.cn/20190123220524348.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

他会自动给我们生成CSRF的攻击代码，我们点击Copy HTML

![](https://img-blog.csdnimg.cn/20190123220655439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

然后保存到我们本地，后缀为.html。双击打开该html文件，点击Submit request。只要当受害者点击了Submit request按钮，我们的CSRF攻击代码就执行了。

![](https://img-blog.csdnimg.cn/20190123221212751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

相关文章：[DVWA之CSRF(跨站请求伪造攻击)](https://blog.csdn.net/qq_36119192/article/details/82918141 "DVWA之CSRF(跨站请求伪造攻击)")

                  [CSRF实战攻防](https://mp.weixin.qq.com/s?__biz=MzI4NjEyMDk0MA==&mid=2649848651&idx=1&sn=0876a8256d22c36e7111579680328bc4&chksm=f3e415c8c4939cde89926b21c393c1c2db116337ff398634e22b477442af02d04df27be9c448&mpshare=1&scene=1&srcid=&sharer_sharetime=1575252752903&sharer_shareid=246ae72ebf3b2af5c45fe95d5f09f3d1&key=3f40946aa1882f680a0b697ad8ba64ee486659a67535b8e17ccd4801bc291a09b917309a3ed8a723c7c377017d4b87a7327923ee8cd812422ebfa8ee5959c14012d1fb39446ee78120f6e6bed0694551&ascene=1&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows-QQBrowser&version=6103000b&lang=zh_CN&pass_ticket=IpeXxy30DmozYuDVsgikAA4PPKR5v3xZAypiFog%2FoRfDTp%2BSYBZeGnp12WW696QT "CSRF实战攻防")