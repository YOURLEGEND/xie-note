ldapsearch是类unix下一款用于AD信息查询的工具，[Kali](https://so.csdn.net/so/search?q=Kali&spm=1001.2101.3001.7020)中自带ldapsearch。

参数：

```
usage: ldapsearch [options] [filter [attributes...]]      
where:      
  filter	RFC 4515 compliant LDAP search filter      
  attributes	whitespace-separated list of attribute descriptions      
    which may include:      
      1.1   no attributes      
      *     all user attributes      
      +     all operational attributes      
Search options:      1
  -a deref   one of never (default), always, search, or find      1
  -A         retrieve attribute names only (no values)      1
  -b basedn  base dn for search      1
  -c         continuous operation mode (do not stop on errors)      1
  -E [!]<ext>[=<extparam>] search extensions (! indicates criticality)      1
             [!]domainScope              (domain scope)      1
             !dontUseCopy                (Don't Use Copy)      1
             [!]mv=<filter>              (RFC 3876 matched values filter)      1
             [!]pr=<size>[/prompt|noprompt] (RFC 2696 paged results/prompt)      1
             [!]sss=[-]<attr[:OID]>[/[-]<attr[:OID]>...]      2
                                         (RFC 2891 server side sorting)      2
             [!]subentries[=true|false]  (RFC 3672 subentries)      2
             [!]sync=ro[/<cookie>]       (RFC 4533 LDAP Sync refreshOnly)      2
                     rp[/<cookie>][/<slimit>] (refreshAndPersist)      2
             [!]vlv=<before>/<after>(/<offset>/<count>|:<value>)      2
                                         (ldapv3-vlv-09 virtual list views)      2
             [!]deref=derefAttr:attr[,...][;derefAttr:attr[,...][;...]]      2
             [!]<oid>[=:<b64value>] (generic control; no response handling)      2
  -f file    read operations from `file'      2
  -F prefix  URL prefix for files (default: file:///tmp/)      3
  -l limit   time limit (in seconds, or "none" or "max") for search      3
  -L         print responses in LDIFv1 format      3
  -LL        print responses in LDIF format without comments      3
  -LLL       print responses in LDIF format without comments      3
             and version      3
  -M         enable Manage DSA IT control (-MM to make critical)      3
  -P version protocol version (default: 3)      3
  -s scope   one of base, one, sub or children (search scope)      3
  -S attr    sort the results by attribute `attr'      3
  -t         write binary values to files in temporary directory      4
  -tt        write all values to files in temporary directory      4
  -T path    write files to directory specified by path (default: /tmp)      4
  -u         include User Friendly entry names in the output      4
  -z limit   size limit (in entries, or "none" or "max") for search      4
Common options:      4
  -d level   set LDAP debugging level to `level'      4
  -D binddn  bind DN      4
  -e [!]<ext>[=<extparam>] general extensions (! indicates criticality)      4
             [!]assert=<filter>     (RFC 4528; a RFC 4515 Filter string)      4
             [!]authzid=<authzid>   (RFC 4370; "dn:<dn>" or "u:<user>")      5
             [!]chaining[=<resolveBehavior>[/<continuationBehavior>]]      5
                     one of "chainingPreferred", "chainingRequired",      5
                     "referralsPreferred", "referralsRequired"      5
             [!]manageDSAit         (RFC 3296)      5
             [!]noop      5
             ppolicy      5
             [!]postread[=<attrs>]  (RFC 4527; comma-separated attr list)      5
             [!]preread[=<attrs>]   (RFC 4527; comma-separated attr list)      5
             [!]relax      5
             [!]sessiontracking      6
             abandon, cancel, ignore (SIGINT sends abandon/cancel,      6
             or ignores response; if critical, doesn't wait for SIGINT.      6
             not really controls)      6
  -h host    LDAP server      6
  -H URI     LDAP Uniform Resource Identifier(s)      6
  -I         use SASL Interactive mode      6
  -n         show what would be done but don't actually do it      6
  -N         do not use reverse DNS to canonicalize SASL host name      6
  -O props   SASL security properties      6
  -o <opt>[=<optparam>] general options      7
             nettimeout=<timeout> (in seconds, or "none" or "max")      7
             ldif-wrap=<width> (in columns, or "no" for no wrapping)      7
  -p port    port on LDAP server      7
  -Q         use SASL Quiet mode      7
  -R realm   SASL realm      7
  -U authcid SASL authentication identity      7
  -v         run in verbose mode (diagnostics to standard output)      7
  -V         print version info (-VV only)      7
  -w passwd  bind password (for simple authentication)      7
  -W         prompt for bind password      8
  -x         Simple authentication      8
  -X authzid SASL authorization identity ("dn:<dn>" or "u:<user>")      8
  -y file    Read password from file      8
  -Y mech    SASL mechanism      8
  -Z         Start TLS request (-ZZ to require successful response)
```


常见的几个参数如下:

| Option | 说明 |
| --- | --- |
| \-H | ldapuri，格式为 ldap://机器名或者IP:端口号，不能与-h和-p同时使用 |
| \-h | LDAP服务器IP或者可解析的hostname，与-p可结合使用，不能与-H同时使用 |
| \-p | LDAP服务器端口号，与 -h 可结合使用，不能与-H同时使用。不指定时默认为389端口 |
| \-x | 使用简单认证方式 |
| \-D | 认证的用户名 |
| \-w | 密码，与-W 二者选一 |
| \-W | 不输入密码，会交互式的提示用户输入密码，与-w二者选一 |
| \-b | 指定 DN 进行查询 |
| \-f | 指定输入条件，在RFC 4515中有更详细的说明 |
| \-c | 出错后忽略当前错误继续执行，缺省情况下遇到错误即终止 |
| \-n | 模拟操作但并不实际执行，用于验证，常与 -v 一同使用进行问题定位 |
| \-v | 显示详细信息 |
| \-d | 显示debug信息，可设定级别 |
| \-s | 指定搜索范围, 可选值：`base|one|sub|children` |

只需要知道普通用户账号密码即可使用该工具查询域信息。

```
ldapsearch -x -h 192.168.10.131 -D "hack@xie.com" -w xxxx -b "CN=Users,DC=xie,DC=com" dn      
ldapsearch -x -H ldap://192.168.10.131:389 -D "hack@xie.com" -w xxxx -b "CN=Users,DC=xie,DC=com" dn
```


![](https://img-blog.csdnimg.cn/20200727151304188.png)

![](https://img-blog.csdnimg.cn/20200930155126840.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

如果把最后面的dn去掉的话，会显示详细信息

![](https://img-blog.csdnimg.cn/20200930155422970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

使用ldapsearch查询导出的数据格式为 LDIF 格式，可以用adoffline.py将其转为sqlite文件。

```
ldapsearch -x -h 192.168.10.131 -D "hack@xie.com" -w xxxx -b "CN=Users,DC=xie,DC=com" -o ldif-wrap=no > test.ldif
```


![](https://img-blog.csdnimg.cn/20200727151158529.png)

然后用navicat打开tmp目录下的.db文件

![](https://img-blog.csdnimg.cn/20200727151030416.png)

未完待续。。

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)

参考文章：[LDAP基础：6：使用ldapsearch进行数据查询](https://blog.csdn.net/liumiaocn/article/details/83990918 "LDAP基础：6：使用ldapsearch进行数据查询")

文章知识点与官方知识档案匹配，可进一步学习相关知识

[CS入门技能树](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[Linux入门](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)[初识Linux](https://edu.csdn.net/skill/gml/gml-1c31834f07b04bcc9c5dff5baaa6680c)11176 人正在系统学习中