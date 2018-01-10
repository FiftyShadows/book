####go 写简单的 web 服务器很简单的,直接上代码
```go
package main

import (
	"fmt"
	"net/http"
	"strings"
	"log"
)

func sayhelloName(w http.ResponseWriter, r *http.Request) {
	r.ParseForm()  //解析参数，默认是不会解析的
	fmt.Println(r.Form)  //这些信息是输出到服务器端的打印信息
	fmt.Println("path", r.URL.Path)
	fmt.Println("scheme", r.URL.Scheme)
	fmt.Println(r.Form["url_long"])
	for k, v := range r.Form {
		fmt.Println("key:", k)
		fmt.Println("val:", strings.Join(v, ""))
	}
	fmt.Fprintf(w, "Hello World!") //这个写入到w的是输出到客户端的
}

func main() {
	http.HandleFunc("/", sayhelloName) //设置访问的路由
	err := http.ListenAndServe(":9090", nil) //设置监听的端口
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```
在浏览器输入`http://localhost:9090`
再尝试`http://localhost:9090/?url_long=111&url_long=222`
	
<br>
#####几个名词解释
- Request：用户请求的信息，用来解析用户的请求信息，包括post、get、cookie、url等信息

- Response：服务器需要反馈给客户端的信息

- Conn：用户的每次请求链接

- Handler：处理请求和生成返回信息的处理逻辑

#####http包执行流程
1. 创建Listen Socket, 监听指定的端口, 等待客户端请求到来。

2. Listen Socket接受客户端的请求, 得到Client Socket, 接下来通过Client Socket与客户端通信。

3. 处理客户端的请求, 首先从Client Socket读取HTTP请求的协议头, 如果是POST方法, 还可能要读取客户端提交的数据, 然后交给相应的handler处理请求, handler处理完毕准备好客户端需要的数据, 通过Client Socket写给客户端。

#####解决主要的三个问题
1. 如何监听端口？
2. 如何接收客户端请求？
3. 如何分配handler？

<br>
<br>


##OAuth 认证流程详解


####首先一定要看下阮一峰的博客:http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html

很多网站、APP 弱化甚至没有搭建自己的账号体系，而是直接使用社会化登录的方式，这样不仅免去了用户注册账号的麻烦、还可以获取用户的好友关系来增强自身的社交功能。

比如我们可以使用微博登录简书，简书会自动将你的微博头像设置为你的简书头像，将你的微博昵称设置为你的简书昵称，甚至还可以获取你微博中的好友列表，提示你哪些朋友已经在使用简书，这是如何做到的呢？

最传统的办法是让用户直接在简书的登录页面输微博的账号和密码，简书通过用户的账号和密码去微博那里获取用户数据，但这样做有很多严重的缺点：

- 简书需要明文保存用户的微博账号和密码，这样很不安全。
- 简书拥有了获取用户在微博所有的权限，包括删除好友、给好友发私信、更改密码、注销账号等危险操作。
- 用户只有修改密码，才能收回赋予简书的权限。但是这样做会使得其他所有获得用户授权的第三方应用程序全部失效。
- 只要有一个第三方应用程序被破解，就会导致用户密码泄漏，以及所有使用微博登录的网站的数据泄漏。
为了解决以上的问题，OAuth 协议应运而生。

###原理概要

新浪微博作为服务提供商，拥有用户的头像、昵称、邮箱、好友以及所有的微博内容，简书希望获取用户存储在微博的头像和昵称，假设它们是三个人：

- 简书问新浪微博：我想要获取用户 A 的头像和昵称，请你提供
- 微博说：我需要经过用户Ａ 本人的许可，然后去问用户 A 是否要授权简书访问自己的头像和昵称
- 用户 A 对微博说：我给简书一个临时的钥匙，如果他给你出示了这把钥匙，你就把我的资料给他
- 简书使用户给它的钥匙获取用户头像和昵称信息。

以上是 OAuth 认证的大概流程。在使用微博授权之前，简书需要先在微博开放平台上注册应用，填写自己的名称、logo、用途等信息，微博开放平台颁发给简书一个应用 ID 和叫 APP Secret 的密钥，在实际对接中，会使用到这两个参数。

###详细流程

![](/assets/213.png)

接下来分步详细解释上图中每步都做了什么：

#### 1.用户点击简书上的微博登录按钮，跳转到微博授权页面。微博登录按钮的链接形如下方的 URL：

```python
https://api.weibo.com/oauth2/authorize?client_id=123050457758183&redirect_uri=http://jianshu.com/callback
```

URL 中要包含以下参数：
1. **client_id**：在微博开放平台申请的应用 ID
2. **redirect_uri**：授权成功后要跳转到的地址

点击以上链接后跳转到微博的授权页面如下图：

![](/assets/5555.png)

这个页面会告诉用户第三方应用要获取用户的哪些数据，以及拥有什么权限，比如在上图中简书会要求获得个人信息、好友关系、分享内容到微博以及获得评论的权限，用户点击“允许”则表示允许简书获得这些数据，进行下一步

####2. 页面自动跳转到初始参数中`redirect_uri`定义的那个URL，并自动在 URL 末尾添加一个 `code` 参数，实际跳转的地址形如:

```
 http://jianshu.com/callback?code=2559200ecd7ea433f067a2cf67d6ce6c
```

####3. 第三步，简书通过上一步获取的 code 参数换取 Token，Token 就是前文中说到的钥匙。简书请求如下的接口获取 Token：
	POST https://api.weibo.com/oauth2/access_token
####要包含以下参数：

- **client_id**：在微博开放平台申请的应用 ID很多语言都可以使用。                                                         
- **client_secret**：在微博开放平台申请时提供的APP Secret                                                       
- **grant_type**：需要填写authorization_code                                                           
- **code**：上一步获得的 code                                                                            
- **redirect_uri**：回调地址，需要与注册应用里的回调地址以及第一步的 redirect_uri 参数一致   

####4. 通过第三步的请求，接口返回 Token 和相关数据：

```json
{
 "access_token": "ACCESS_TOKEN",//Token 的值
 "expires_in": 1234,//过期时间
 "uid":"12341234"//当前授权用户的UID。
}
```
####5. 在第四步中获取了access_token ，使用它，就可以去获取用户的资源了，要获取用户昵称和头像，请求如下接口：
####GET https://api.weibo.com/2/users/show.json
携带参数：

- **access_token**：上一步获取的access_token
- **uid**：用户的账号 id，上一步的接口有返回                                   
                                                                                                
####6. 最后一步，微博返回用户信息，简书进行处理，整个流程结束

通过以上的方式，在简书和新浪微博中间建立了一个独立的权限层，这个权限由用户赋予，可以被用户随时取消，不同第三方应用之间相互独立，互不干扰，这样就彻底解决了明文存放账号密码的问题。


