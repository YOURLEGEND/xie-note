**目录**

[使用 Github 进行邮件配置信息收集](#t0 "使用 Github 进行邮件配置信息收集")

[使用Github进行数据库信息收集](#t1 "使用Github进行数据库信息收集")

[使用Github进行 SVN 信息收集](#t2 "使用Github进行 SVN 信息收集")

[使用Github进行综合信息收集](#t3 "使用Github进行综合信息收集")

* * *

在[渗透测试](https://so.csdn.net/so/search?q=%E6%B8%97%E9%80%8F%E6%B5%8B%E8%AF%95&spm=1001.2101.3001.7020)的信息收集阶段，可以去Github和码云上搜索与目标有关的信息，或者就有意想不到的收获。(有些开发人员将代码上传到代码库的时候，有可能连一些重要的配置信息也上传了)

[Github](https://so.csdn.net/so/search?q=Github&spm=1001.2101.3001.7020)的搜素语法：

```
in:name test               #仓库标题搜索含有关键字test      
in:descripton test         #仓库描述搜索含有关键字      
in:readme test             #Readme文件搜素含有关键字      
stars:>3000 test           #stars数量大于3000的搜索关键字      
stars:1000..3000 test      #stars数量大于1000小于3000的搜索关键字      
forks:>1000 test           #forks数量大于1000的搜索关键字      
forks:1000..3000 test      #forks数量大于1000小于3000的搜索关键字      
size:>=5000 test           #指定仓库大于5000k(5M)的搜索关键字      
pushed:>2019-02-12 test    #发布时间大于2019-02-12的搜索关键字      1
created:>2019-02-12 test   #创建时间大于2019-02-12的搜索关键字      1
user:test                  #用户名搜素      1
license:apache-2.0 test    #明确仓库的 LICENSE 搜索关键字      1
language:java test         #在java语言的代码中搜索关键字      1
user:test in:name test     #组合搜索,用户名test的标题含有test的
```


### 使用 Github 进行邮件配置信息收集

很多网站及系统都会使用 pop3 和 smtp 发送来邮件，不少开发者由于安全意识不足会把相关的配置信息也放到Github上，所以如果这时候我们动用一下google搜索命令语句，构造一下关键字，就能把这些信息给找出来了。

```
site:Github.com smtp       
site:Github.com smtp @qq.com       
site:Github.com smtp @126.com       
site:Github.com smtp @163.com       
site:Github.com smtp @sina.com.cn       1
site:Github.com smtp password       1
site:Github.com String password smtp       1
……       1
我们也可以锁定域名搜索结合厂商域名 灵活运用例如搜百度的       1
site:Github.com smtp @baidu.com
```


### 使用Github进行数据库信息收集

```
site:Github.com sa password       
site:Github.com root password       
site:Github.com User ID=’sa’;Password       
site:Github.com inurl:sql
```


### 使用Github进行 SVN 信息收集

```
site:Github.com svn       
site:Github.com svn username       
site:Github.com svn password       
site:Github.com svn username password
```


### 使用Github进行综合信息收集

```
site:Github.com password       
site:Github.com ftp ftppassword       
site:Github.com 密码       
site:Github.com 内部
```


 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[Github上寻找敏感信息技巧分享](https://blog.csdn.net/zyhj2010/article/details/45064903 "Github上寻找敏感信息技巧分享")

                  [如何利用GitHub搜索敏感信息](https://blog.csdn.net/xiaoi123/article/details/85121827 "如何利用GitHub搜索敏感信息")