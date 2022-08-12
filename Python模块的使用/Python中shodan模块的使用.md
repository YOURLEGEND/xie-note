关于shodan的安装和使用，传送门——> [渗透测试之Shodan的安装和使用](https://blog.csdn.net/qq_36119192/article/details/84031765)

### 常用 Shodan 库函数

*   `shodan.Shodan(key)` ：初始化连接API
*   `Shodan.count(query, facets=None)`：返回查询结果数量
*   `Shodan.host(ip, history=False)`：返回一个IP的详细信息
*   `Shodan.ports()`：返回Shodan可查询的端口号
*   `Shodan.protocols()`：返回Shodan可查询的协议
*   `Shodan.services()`：返回Shodan可查询的服务
*   `Shodan.queries(page=1, sort='timestamp', order='desc')`：查询其他用户分享的查询规则
*   `Shodan.scan(ips, force=False)`：使用Shodan进行扫描，ips可以为字符或字典类型
*   `Shodan.search(query, page=1, limit=None, offset=None, facets=None, minify=True)`： 查询Shodan数据

先写一个简单的脚本，扫描 apache 的主机

```
import shodan    #导入shodan库      
api=shodan.Shodan("cB9sXwb7l95ZhSJaNgcaO7NQpkzfhQVM")  #指定API_KEY,返回句柄      
try:      
    results=api.search('apache')    #搜索apache，返回 JSON格式的数据      
    print(results)      
    print("Results found:%s"%results['total'])      
    for result in results['matches']:      
        print(result['ip_str'])     #打印出ip地址      
except shoadn.APIError,e:      1
    print("Error:%s"%e)
```


返回的JSON格式的数据 

```
{      
        'total': 8669969,      
        'matches': [      
                {      
                        'data': 'HTTP/1.0 200 OK\r\nDate: Mon, 08 Nov 2010 05:09:59 GMT\r\nSer...',      
                        'hostnames': ['pl4t1n.de'],      
                        'ip': 3579573318,      
                        'ip_str': '89.110.147.239',      
                        'os': 'FreeBSD 4.4',      1
                        'port': 80,      1
                        'timestamp': '2014-01-15T05:49:56.283713'      1
                },      1
                ...      1
        ]      1
}
```


![](https://img-blog.csdnimg.cn/20181113202111239.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

我们也可以加上端口号，并且写入文件中，作为访问链接

```
import shodan       
api=shodan.Shodan("cB9sXwb7l95ZhSJaNgcaO7NQpkzfhQVM")      
def FindTarget():      
    try:      
        f=open("target.txt","w")      
        results=api.search('JAWS/1.0')       
        print("Results found:%s"%results['total'])      
        for result in results['matches']:      1
            url=result['ip_str']+":"+str(result['port'])      1
            print(url)       1
            f.write(url)      1
            f.write("\n")      1
        f.close()      1
    except shodan.APIError,e:      1
        print("Error:%s"%e)      1
FindTarget()
```


![](https://img-blog.csdnimg.cn/20181113205428995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)