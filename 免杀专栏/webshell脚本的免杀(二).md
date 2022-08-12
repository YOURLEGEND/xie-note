脚本木马的免杀
-------

Web端木马一般是中国菜刀的一句话木马、中国蚁剑马、冰蝎马、还有各种各样脚本的大马。Web端木马的查杀一般是WAF通过分析流量来进行截断。在WAF的规则里面有这些木马的流量特征，只要WAF匹配到了木马的流量特征，就会对木马请求的流量进行截断丢弃。所以，Web端木马免杀实质就是要对木马通信流量做混淆，以达到WAF匹配不到该木马流量的目的。目前，像中国菜刀、中国蚁剑这种木马，其通信流量是很容易被WAF给检测到的。而冰蝎是目前最流行的Web木马，其通过动态二进制加密流量，可以躲避很多WAF的查杀。

但是也有一些文章关于分析冰蝎流量特征的：[流量加密又怎样？ 多种姿势检测“冰蝎”](https://www.freebuf.com/articles/web/216133.html) 、[冰蝎动态二进制加密WebShell基于流量侧检测方案](https://www.freebuf.com/articles/web/221241.html)

未完待续。。

相关文章：[Webshell免杀](https://www.jianshu.com/p/f374d8b684fe)

                  [经验分享 | 一句话免杀编写思路](https://www.freebuf.com/articles/system/164076.html)