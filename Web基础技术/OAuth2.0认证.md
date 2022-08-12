**目录**

[Oauth](#t0)

[Oauth2.0](#t1)

[客户端应用注册](#t2)

[授权码模式（authorization code）流程](#t3)

[简化模式(Implicit Flow)流程](#t4)

[密码模式(Resource owner password credentials)流程](#t5)

[客户端模式(Client Credentials Flow)流程](#t6)

* * *

> 前言：开发者A注册某IT论坛后，发现可以在信息栏中填写自己的 [Github](https://so.csdn.net/so/search?q=Github&spm=1001.2101.3001.7020) 个人信息和仓库项目，但是他又觉得手工填写十分麻烦，但是直接提供 Github 账户和密码给论坛又是非常危险的事情。开发者A发现在该论坛处，可以点击“导入”按钮，授权该论坛访问自己的 Github 账户并限制其只具备读权限。这样一来， Github 中的所有仓库和相关信息就可以很方便地被导入到信息栏中，账户隐私信息也不会泄露。

Oauth
-----

Oauth协议为用户资源的授权提供了一个安全的、开放而又简易的标准。与以往的授权方式不同之处是Oauth的授权不会使第三方触及到用户的帐号信息（如用户名与密码），即第三方无需使用用户的用户名与密码就可以申请获得该用户资源的授权，因此Oauth是安全的。OAuth是Open Authorization的简写。Oauth已经有2.0版本了，2007年发布了Oauth1.0协议，2011年发布会了Oauth2.0，2.0与1.0之间不兼容。Oauth2.0 是目前最流行的授权机制，用来授权第三方应用，获取用户数据。

关于Oauth2.0认证的一个文章，讲的很简单明了——> [OAuth 2.0 的一个简单解释](http://www.ruanyifeng.com/blog/2019/04/oauth_design.html)

| 英文 | 中文 |
| --- | --- |
| Authorization Grant | 授权许可 |
| Authorization Code | 授权码 |
| Access Token | 访问令牌 |
| Authorization | 授权 |
| Authentication | 认证 |

### Oauth2.0

**OAuth2.0认证流程：**获取授权码(Authorization Code)—>换取访问令牌（Access\_token）—>访问资源

*   用户到授权服务器，请求授权，然后返回授权码(Authorization Code)
*   客户端由授权码到授权服务器换取访问令牌(Access token) 
*   用访问令牌去访问得到授权的资源

**OAuth 2 标准中定义了以下几种角色：**

*   资源所有者（Resource Owner）：资源所有者是 OAuth 2 .0四大基本角色之一，在 OAuth 2 .0标准中，资源所有者即代表授权客户端访问本身资源信息的用户（User），也就是应用场景中的“开发者A”。客户端访问用户帐户的权限仅限于用户授权的“范围”（aka. scope，例如读取或写入权限）。
*   资源服务器（Resource Server）：资源服务器托管了受保护的用户账号信息，而授权服务器验证用户身份然后为客户端派发资源访问令牌。在上述应用场景中，Github 既是授权服务器也是资源服务器，个人信息和仓库信息即为资源（Resource）。而在实际工程中，不同的服务器应用往往独立部署，协同保护用户账户信息资源。
*   授权服务器（Authorization Server）：资源服务器托管了受保护的用户账号信息，而授权服务器验证用户身份然后为客户端派发资源访问令牌。在上述应用场景中，Github 既是授权服务器也是资源服务器，个人信息和仓库信息即为资源（Resource）。而在实际工程中，不同的服务器应用往往独立部署，协同保护用户账户信息资源。
*   客户端（Client）： OAuth 2.0中，客户端即代表意图访问受限资源的第三方应用。在访问实现之前，它必须先经过用户者授权，并且获得的授权凭证将进一步由授权服务器进行验证。

**OAuth2.0的授权模式**

*   授权码模式（Authorization code）：授权码模式是目前最常见的一种授权模式， 适用于所有有Server端的应用，如Web站点、有Server端的手机客户端
*   简化模式（Implicitly Grant）：结合移动应用或 Web App 使用
*   密码模式（Resource owner password credentials）：适用于受信任客户端应用，例如同个组织的内部或外部应用
*   客户端模式（Client credentials）：适用于客户端调用主服务API型应用（比如百度API Store）

### 客户端应用注册

**客户端应用注册**

在应用 OAuth 2 之前，你必须在授权方服务中注册你的应用。如 Google Identity Platform 或者 Github OAuth Setting，诸如此类 OAuth 实现平台中一般都要求开发者提供如下所示的授权设置项。

*   应用名称
*   应用网站
*   重定向URI或回调URL（redirect\_uri）

重定向URI是授权方服务在用户授权（或拒绝）应用程序之后重定向供用户访问的地址，因此也是用于处理授权码或访问令牌的应用程序的一部分。

**Client ID 和 Client Secret(客户端秘钥)**

一旦你的应用注册成功，授权方服务将以client id和client secret的形式为应用发布client credentials（客户端凭证）。client id是公开透明的字符串，授权方服务使用该字符串来标识应用程序，并且还用于构建呈现给用户的授权 url 。当应用请求访问用户的帐户时，client secret用于验证应用身份，并且必须在客户端和服务之间保持私有性。

### **授权码模式（authorization code）流程**

Authorization Code 是最常使用的一种授权许可类型，它适用于第三方应用类型为server-side型应用的场景。Authorization Code授权流程基于重定向跳转，客户端必须能够与User-agent（即用户的 Web 浏览器）交互并接收通过User-agent路由发送的实际authorization code值。

![](https://img-blog.csdnimg.cn/20190825233320882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MTE5MTky,size_16,color_FFFFFF,t_70)

**1.User Authorization Request**

首先，客户端构造了一个用于请求Authorization code的URL并引导User-agent跳转访问。

```
https://authorization-server.com/auth      
 ?response_type=code      
 &client_id=29352915982374239857      
 &redirect_uri=https://example-client.com/callback      
 &scope=create+delete      
 &state=xcoiv98y2kd22vusuye3kch       
如下，是微博登录使用QQ授权      1
https://graph.qq.com//oauth2.0/authorize?client_id=101019034      1
&response_type=code      1
&scope=get_info,get_user_info      1
&redirect_uri=https://passport.weibo.com/othersitebind/bind?site=qq      1
&state=CODE-gz-1I24J0-2hHSCM-UB6CQF9gP1BzBvYa58483      1
&bentry=miniblog      1
&wl=&display=
```


*   response\_type=code：此参数和参数值用于提示授权服务器当前客户端正在进行Authorization Code授权流程。
*   client\_id：客户端身份标识。
*   redirect\_uri：标识授权服务器接收客户端请求后返回给User-agent的跳转访问地址。
*   scope：指定客户端请求的访问级别。
*   state：由客户端生成的随机字符串，步骤2中用户进行授权客户端的请求时也会携带此字符串用于比较，这是为了防止CSRF攻击。

**2\. User Authorizes Applcation**

当用户点击上文中的示例链接时，用户必须已经在授权服务中进行登录（否则将会跳转到登录界面，不过 OAuth 2 并不关心认证过程），然后授权服务会提示用户授权或拒绝应用程序访问其帐户。以下是授权应用程序的示例：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy95SHltVnlhZ0wwNDdGUm5HN3pwbklZZHdLSTNQa3Q5cTNNaFVUVFdXVzJpY094ZkRGYWlhcTZLQTJyRXlQaGpoaWNZcHM1a3k5SDN4R1pSTTRHZk45bHY2QS82NDA_d3hfZm10PXBuZyZ0cD13ZWJwJnd4ZnJvbT01Jnd4X2xhenk9MSZ3eF9jbz0x?x-oss-process=image/format,png)

**3\. Authorization Code Grant**

如果用户确认授权，授权服务器将重定向User-agent至之前客户端提供的指向客户端的redirect\_uri地址，并附带code和state参数（由之前客户端提供），于是客户端便能直接读取到authorization code值。

```
https://example-client.com/redirect?      
code=g0ZGZmNjVmOWIjNTk2NTk4ZTYyZGI3      
&state=xcoiv98y2kd22vusuye3kch       
如下，是QQ授权成功      
https://passport.weibo.com/othersitebind/bind?site=qq      
&state=CODE-gz-1I24J0-2hHSCM-UB6CQF9gP1BzBvYa58483      
&bentry=miniblog      
&wl=      1
&code=4F3BE37354B5148C06CA82032C513AAC
```


*   state值将与客户端在请求中最初设置的值相同。客户端将检查重定向中的状态值是否与最初设置的状态值相匹配。这可以防止CSRF和其他相关攻击。
*   code是授权服务器生成的authorization code值。code相对较短，通常持续1到10分钟，具体取决于授权服务器设置。

**4\. Access Token Request**

现在客户端已经拥有了服务器派发的Authorization code，接下来便可以使用Authorization code和其他参数向服务器请求Access Token（POST方式）。其他相关参数如下：

*   grant\_type=authorization\_code ：这告诉服务器当前客户端正在使用Authorization Code授权流程。
*   code ：应用程序包含它在重定向中给出的授权码。
*   redirect\_uri ：与请求authorization code时使用的redirect\_uri相同。某些资源（API）不需要此参数。
*   client\_id ：客户端标识。
*   client\_secret ：应用程序的客户端密钥。这确保了获取access token的请求只能从客户端发出，而不能从可能截获authorization code的攻击者发出。

**5\. Access Token Grant**

服务器将会验证第4步中的请求参数，当验证通过后（校验Authorization code是否过期，client id和client secret是否匹配等），服务器将向客户端返回Access token。

```
{      
  "access_token":"MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3",      
  "token_type":"bearer",      
  "expires_in":3600,      
  "refresh_token":"IwOGYzYTlmM2YxOTQ5MGE3YmNmMDFkNTVk",      
  "scope":"create delete"      
}
```


至此，授权流程全部结束。直到access token 过期或失效之前，客户端可以通过资源服务器API访问用户的帐户，并具备scope中给定的操作权限。

### 简化模式(Implicit Flow)流程

Implicit授权流程和Authorization Code基于重定向跳转的授权流程十分相似，但它适用于移动应用和 Web App，这些应用与普通服务器端应用相比有个特点，即client secret不能有效保存和信任。相比Authorization Code授权流程，Implicit去除了请求和获得authorization code的过程，而用户点击授权后，授权服务器也会直接把access token放在redirect\_uri中发送给User-agent（浏览器）。 同时第1步构造请求用户授权 url 中的response\_type参数值也由 code 更改为 token 或 id\_token 。

**1\. User Authorization Request**

客户端构造的URL如下所示：

```
https://{yourOktaDomain}.com/oauth2/default/v1/authorize?      
client_id=0oabv6kx4qq6h1U5l0h7      
&response_type=token      
&redirect_uri=http://localhost:8080      
&state=state-296bc9a0-a2a2-4a57-be1a-d0e2fd9bb601      
&nonce=foo
```


response\_type的response\_type参数值为 token 或 id\_token 。其他请求参数与Authorization Code授权流程相比没有并什么变化。

**2\. User Authorizes Application（略）**

**3\. Redirect URI With Access Token In Fragment**

假设用户授予访问权限，授权服务器将User-agent（浏览器） 重定向回客户端使用之前提供的redirect\_uri。并在 uri 的 # 部分添加access\_token键值对。如下所示：

```
http://localhost:8080/#      
access_token=eyJhb[...]erw      
&token_type=Bearer      
&expires_in=3600&scope=openid      
&state=state-296bc9a0-a2a2-4a57-be1a-d0e2fd9bb601
```


token\_type：当且仅当response\_type设置为 token 时返回，值恒为 Bearer。

> 注意在Implicit流程中，access\_token值放在了 URI 的 #  部分，而不是作为 ?query 参数。

**4\. User-agent Follows the Redirect URI**

User-agent（浏览器）遵循重定向指令，请求redirect\_uri标识的客户端地址，并在本地保留 uri 的 # 部分的access\_token信息。

**5\. Application Sends Access Token Extraction Script**

客户端生成一个包含 token 解构脚本的 Html 页面，这个页面被发送给User-agent（浏览器），执行脚本解构完整的redirect\_uri并提取其中的access\_token（access token信息在第4步中已经被User-agent保存）。

**6\. Access Token Passed to Application**

User-agent（浏览器）向客户端发送解构提取的access token。

至此，授权流程全部结束。直到access token 过期或失效之前，客户端可以通过资源服务器API访问用户的帐户，并具备scope中给定的操作权限。

### 密码模式(Resource owner password credentials)流程

Resource Owner Password Credentials授权流程适用于用户与客户端具有信任关系的情况，例如设备操作系统或同一组织的内部及外部应用。用户与应用交互表现形式往往体现为客户端能够直接获取用户凭据（用户名和密码，通常使用交互表单）。

**1\. Resource Owner Password Credentials From User Input**

用户向客户端提供用户名与密码作为授权凭据。

**2\. Resource Owner Password Credentials From Client To Server**

客户端向授权服务器发送用户输入的授权凭据以请求 access token。客户端必须已经在服务器端进行注册。

```
POST /token HTTP/1.1      
Host: server.example.com      
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW      
Content-Type: application/x-www-form-urlencoded       
grant_type=password&username=johndoe&password=A3ddj3w
```


grant\_type：必选项，值恒为 password

**3\. Access Token Passed to Application**

授权服务器对客户端进行认证并检验用户凭据的合法性，如果检验通过，将向客户端派发 access token

```
{      
  "access_token":"2YotnFZFEjr1zCsicMWpAA",      
  "token_type":"example",      
  "expires_in":3600,      
  "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",      
  "example_parameter":"example_value"      
}
```


### 客户端模式(**Client Credentials Flow**)流程

Client Credential是最简单的一种授权流程。客户端可以直接使用它的client credentials或其他有效认证信息向授权服务器发起获取access token的请求。

两步中的请求体和返回体分别如下：

```
POST /token HTTP/1.1      
Host: server.example.com      
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW      
Content-Type: application/x-www-form-urlencoded       
grant_type=client_credentials
```


grant\_type：必选项，值恒为 client\_credentials

```
{      
       "access_token":"2YotnFZFEjr1zCsicMWpAA",      
       "token_type":"example",      
       "expires_in":3600,      
       "example_parameter":"example_value"      
}
```


参考文章：[OAuth 2 深入介绍](https://mp.weixin.qq.com/s?__biz=MzU5OTMyODAyNg==&mid=2247485341&idx=1&sn=f2988175b7d6bc62520a6628625634d2&chksm=feb7d3e8c9c05afef2cd6e798910714daf4a3c0450c3e6a1df89559b8c9cf341b979674ac707&mpshare=1&scene=24&srcid=08267uQ7TIyIsFRjIirIKh0H&sharer_sharetime=1566749537554&sharer_shareid=a12f834461b510ae1828dcc9a9328315&key=5f61bbc2a59beb94aea25fd0d203006c81d68563a3e698be516844ab5cac15f0e5da20228f64e0324233f16b9ccf05810f2b5490a18ca783e5e1a8e95866f4469d3e9b98c37d5ca193e52a8e40c38733&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=lybIui8tSPJmPvxM6jCeu0HIP0fu4k4Y3sMPDg%2BOanNmg6HZF8id8LvU2FB4AThU)

                  [OAuth 2.0 授权认证详解](https://mp.weixin.qq.com/s?__biz=MzI5NDE2NzY5OQ==&mid=2649177626&idx=1&sn=6a04e7a2f996810c7370b5068361a9ad&chksm=f4756be6c302e2f006abbe67002a657b889099f55acfafba9d3b1e2edf3a9287a3666fdaf6eb&mpshare=1&scene=24&srcid=08268iBfrDk9Lzrj0EgEuA0l&sharer_sharetime=1566749530587&sharer_shareid=a12f834461b510ae1828dcc9a9328315&key=56828def8418f30b1f6c09bdc667f59c6b7e25661806598425ce2f85f7390fcf2c6c397281d236b93850d1b4e07f773b537ffa69f5a32e070d6ba6838bed34b75c6ca7071824f52b62d13820e023d2f1&ascene=14&uin=MjIwMDQzNjQxOQ%3D%3D&devicetype=Windows+10&version=62060833&lang=zh_CN&pass_ticket=lybIui8tSPJmPvxM6jCeu0HIP0fu4k4Y3sMPDg%2BOanNmg6HZF8id8LvU2FB4AThU)

相关文章：[OAuth 2.0攻击方法及案例总结](https://blog.csdn.net/cd_xuyue/article/details/52084220)