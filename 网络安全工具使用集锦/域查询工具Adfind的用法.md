使用：

```
AdFind.exe [switches]  [-b basedn]  [-f filter]  [attr list]
```


*   \-appver：显示adfind版本信息
*   \-b：指定要查询的根节点basedn
*   \-bit：指定位查询
*   \-c:  只统计数量
*   \-csv：导出为csv格式
*   \-dn：只显示dn，不返回详细信息
*   \-f：LDAP过滤条件，指定ldap语法
*   \-h：指定主机与端口(ip:port)
*   \-recmute：删除dn下面没有的
*   \-s：搜索的范围， 有 one(当前层级)/sub(一层一层递归)，默认是sub
*   \-sdna：非域管查询sd信息
*   \-t：查询超时时间，默认120秒
*   \-u：指定用户
*   \-up：指定用户的密码

![](https://img-blog.csdnimg.cn/20210430130108684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

<table border="1" cellspacing="0" style="margin-left:.4pt;"><tbody><tr><td><p style="margin-left:0pt;"></p></td><td><p style="margin-left:0pt;"><strong><strong>查询需求</strong></strong></p></td><td><p style="margin-left:0pt;"><strong><strong>adfind 语法</strong></strong></p></td></tr><tr><td><p style="margin-left:0pt;">1</p></td><td><p style="margin-left:0pt;">信任关系</p></td><td><p style="margin-left:0pt;">adfind.exe -f objectclass=trusteddomain</p></td></tr><tr><td><p style="margin-left:0pt;">2</p></td><td><p style="margin-left:0pt;">查询用户test</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">adfind -sc u:test&nbsp;</span></p></td></tr><tr><td><p style="margin-left:0pt;">3</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">查找某个对象De1ta在域内的ACL权限</span></p></td><td><p style="margin-left:0pt;">AdFind.exe -sc getacls -sddlfilter ;;;;;de1ctf2020\De1ta -recmute</p></td></tr><tr><td><p style="margin-left:0pt;">4</p></td><td><p style="margin-left:0pt;">导出整个域的ACL</p></td><td><p style="margin-left:0pt;">AdFind.exe -sc getacls -sddlfilter ;;;;; -recmute</p></td></tr><tr><td><p style="margin-left:0pt;">5</p></td><td><p style="margin-left:0pt;">查找更改一个对象的AllowedToActOnBehalfOfOtherIdentity的权限</p></td><td><p style="margin-left:0pt;">AdFind.exe -s subtree -b "DC=redteam1,DC=com" -sdna nTSecurityDescriptor -sddl+++ -sddlfilter ;;;"msDS-AllowedToActOnBehalfOfOtherIdentity";; -recmute</p></td></tr><tr><td><p style="margin-left:0pt;">6</p></td><td><p style="margin-left:0pt;">查找域内具备dcsync权限的用户</p></td><td><p style="margin-left:0pt;">AdFind.exe -s subtree -b "DC=redteam1,DC=com" -sdna nTSecurityDescriptor -sddl+++ -sddlfilter ;;;"Replicating Directory Changes";; -recmute<br>AdFind.exe -s subtree -b "DC=redteam1,DC=com" -sdna nTSecurityDescriptor -sddl+++ -sddlfilter ;;;"DS-Replication-Get-Changes";; -recmute</p></td></tr><tr><td><p style="margin-left:0pt;">7</p></td><td><p style="margin-left:0pt;">查询域内指定用户的sid</p></td><td><p style="margin-left:0pt;">AdFind.exe -sc u:rootclay objectSid</p></td></tr><tr><td><p style="margin-left:0pt;">8</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">查询域sid对应的dn</span></p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">AdFind.exe -sc adsid:xx</span></p></td></tr><tr><td><p style="margin-left:0pt;">9</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">查询所有用户</span></p></td><td><p style="margin-left:0pt;">AdFind.exe -f "<span style="color:#333333;">(&amp;(objectCategory=person)(objectClass=user))</span>"&nbsp;</p></td></tr><tr><td><p style="margin-left:0pt;">10</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">查询配置了非约束的用户</span></p></td><td><p style="margin-left:0pt;">AdFind -f "<span style="color:#333333;">userAccountControl:1.2.840.113556.1.4.803:=524288</span>" -dn</p></td></tr><tr><td><p style="margin-left:0pt;">11</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">查询配置了约束的用户</span></p></td><td><p style="margin-left:0pt;">AdFind -f "<span style="color:#333333;">userAccountControl:1.2.840.113556.1.4.803:=16777216</span>" -dn</p></td></tr><tr><td><p style="margin-left:0pt;">12</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">域控制器列表</span></p></td><td><p style="margin-left:0pt;">AdFind -sc dclist</p></td></tr><tr><td><p style="margin-left:0pt;">13</p></td><td><p style="margin-left:0pt;">域控版本</p></td><td><p style="margin-left:0pt;">AdFind -schema -s base objectversion</p></td></tr><tr><td><p style="margin-left:0pt;">14</p></td><td><p style="margin-left:0pt;">当前域中所有计算机</p></td><td><p style="margin-left:0pt;">AdFind -f "objectcategory=computer"</p></td></tr><tr><td><p style="margin-left:0pt;">15</p></td><td><p style="margin-left:0pt;">域内的所有GPO</p></td><td><p style="margin-left:0pt;">AdFind -sc gpodmp</p></td></tr><tr><td><p style="margin-left:0pt;">16</p></td><td><p style="margin-left:0pt;"><span style="color:#333333;">枚举受保护AD账户</span></p></td><td><p style="margin-left:0pt;">Adfind -f "&amp;(objectcategory=person)(samaccountname=*)(admincount=1)" -dn</p></td></tr><tr><td><p style="margin-left:0pt;">17</p></td><td><p style="margin-left:0pt;">导出域内的所有手机号为csv 格式</p></td><td><p style="margin-left:0pt;">adfind -b "DC=xx,DC=xxx,DC=xxx" -f "mobile=*" mobile mail displayName title -s Subtree -recmute -csv mobile</p></td></tr><tr><td><p style="margin-left:0pt;">18</p></td><td><p style="margin-left:0pt;">导出域内的所有邮箱为csv 格式</p></td><td><p style="margin-left:0pt;">adfind -b "DC=xx,DC=xxx,DC=xxx" -f "mail=*" mail displayName title -s Subtree -recmute -csv mobile</p></td></tr><tr><td><p style="margin-left:0pt;">19</p></td><td><p style="margin-left:0pt;">查询域内高权限的spn</p></td><td><p style="margin-left:0pt;">adfind -b&nbsp;"DC=test,DC=local"&nbsp;-f&nbsp;"&amp;(servicePrincipalName=*)(admincount=1)"&nbsp;servicePrincipalName</p></td></tr><tr><td><p style="margin-left:0pt;">20</p></td><td><p style="margin-left:0pt;">查找域内所有的开启”Do not require Kerberos preauthentication”选项的用户</p></td><td><p style="margin-left:0pt;">adfind -b "DC=xx,DC=xxx,DC=xxx" -f "useraccountcontrol:1.2.840.113556.1.4.803:=4194304" -dn</p></td></tr></tbody></table>

例如：

```
#查看域xie.com下objectcategory=computer的所有对象，会显示出所有对象以及对象的所有属性      
Adfind.exe -b dc=xie,dc=com -f "objectcategory=computer"       
#查看域xie.com下objectcategory=computer的所有对象，会显示出所有对象以及对象的所有属性      
Adfind.exe -b dc=xie,dc=com -f "objectcategory=user"
```


![](https://img-blog.csdnimg.cn/20200706110213887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200706111807961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
#查看域xie.com下objectcategory=computer的所有对象，过滤出cn 和  createTimeStamp 属性      
Adfind.exe -b dc=xie,dc=com -f "objectcategory=computer" cn createTimeStamp       
#查看域xie.com下objectcategory=user的所有对象，过滤出cn 和  createTimeStamp 属性      
Adfind.exe -b dc=xie,dc=com -f "objectcategory=user" cn createTimeStamp
```


![](https://img-blog.csdnimg.cn/20200706111052425.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20200706111651191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

查找域中配置非约束委派的用户

```
#查找域中配置非约束委派的用户,过滤出cn和distinguishedName属性      
AdFind.exe -b "DC=xie,DC=com" -f "(&(samAccountType=805306368)(userAccountControl:1.2.840.113556.1.4.803:=524288))" cn distinguishedName      
#查找域中配置非约束委派的主机,过滤出cn和distinguishedName属性      
AdFind.exe -b "DC=xie,DC=com" -f "(&(samAccountType=805306369)(userAccountControl:1.2.840.113556.1.4.803:=524288))" cn distinguishedName       
#查询域中配置非约束委派的主机      
AdFind.exe -b "DC=xie,DC=com" -f "(&(samAccountType=805306369)(msds-allowedtodelegateto=*))" -dn      
#查询域中配置非约束委派的服务账户      
AdFind.exe -b "DC=xie,DC=com" -f "(&(samAccountType=805306368)(msds-allowedtodelegateto=*))" -dn       1
#查询域中配置非约束委派的主机      1
AdFind.exe -b "DC=xie,DC=com" -f "(&(samAccountType=805306369)(msDS-AllowedToActOnBehalfOfOtherIdentity=*))" -dn      1
#查询域中配置非约束委派的服务账户      1
AdFind.exe -b "DC=xie,DC=com" -f "(&(samAccountType=805306368)(msDS-AllowedToActOnBehalfOfOtherIdentity=*))" -dn
```


![](https://img-blog.csdnimg.cn/20200706113323750.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

  如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)