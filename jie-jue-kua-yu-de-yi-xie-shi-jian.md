3**跨域，指的是浏览器不能执行其他网站的脚本。它是由浏览器的同源策略造成的，是浏览器对javascript施加的安全限制。**
http://www.123.com/index.html 调用 http://www.123.com/server.php （非跨域）

http://www.123.com/index.html 调用 http://www.456.com/server.php （主域名不同:123/456，跨域）

http://abc.123.com/index.html 调用 http://def.123.com/server.php （子域名不同:abc/def，跨域）

http://www.123.com:8080/index.html 调用 http://www.123.com:8081/server.php （端口不同:8080/8081，跨域）

http://www.123.com/index.html 调用 https://www.123.com/server.php （协议不同:http/https，跨域）

请注意：localhost和127.0.0.1虽然都指向本机，但也属于跨域。

**后端:**
**1. url 转发**
在同一个窗口中，通过URL提交的方式，多次跳转，需要两边的页面相互支持。

比如在http://www.myexample.com/page/page-a.html 页面中，有一个提交按钮，将数据post到远端的服务器，需要告诉对方跳转回来到哪个页面，看起来像这样：
>http://www.othersite.com/page/show.html?url=http://www.myexample.com/page/page-b.html

远程服务器show.html处理完成后，再次将数据提交到page-b.html，page-a和page-b是在同一个域下面的，所以就可以相互访问了。
**2. nginx 代理**
原理就是把远程服务通过代理服务器变成本地的服务，需要借助WEB服务器，Nginx和Apache都支持代理转发。Nginx的配置参考：
```
location /api/proxy {
    proxy_pass  http://remoteipaddress:8080/api;
    proxy_set_header  Host  $host;
    proxy_set_header  X-Real-IP  $remote_addr;
    proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;
}
```

Node也有开源的组件http-proxy-middleware可以支持代理。

```
var express=require('express');
var proxy=require('http-proxy-middleware');
var app=express();
app.use('/api',proxy({target:'http://www.example.org', changeOrigin:true}));
app.listen(3000);
```
**3. cors**
看这个:http://www.ruanyifeng.com/blog/2016/04/cors.html
![](/assets/3281657-c41aa1081050e6cc.png)
<br>
**前端:**
**1. 修改Domain**
浏览器允许通过设置document.domain共享 Cookie，相当于是把两个不同源的页面设置成相同的源，这种方法只适用于 Cookie 和 iframe 窗口，而且要求两个网页一级域名相同，只是二级域名不同。
`document.domain='example.com';`
**2. jsonp**
在一个页面中，可以使用`<script>`标记来引用一个外部的JS文件，并且能够成功执行。直接上代码可能会更好理解一些。
假设我在某个网站有一个js文件，URL是：http://remoteserver.com/remote.js，里面的代码很简单：
```
alert('this is a remote alert!');
```
在另外一个网站的页面http://localserver.com/page.html，可以引入这个JS文件：
```
<head>
    <script type="text/javascript" src="http://remoteserver.com/remote.js"/>
</head>
```
在浏览器中打开page.html，会弹出一个警告对话框。

JSONP就是利用了这一点，如果将alert的内容改为下面的内容：
```
localMethod({"result":"data from remote!"});
```
同时将page.html的内容修改一下
```
<script type="text/javascript">
    function localMethod(data){
        alert(data.result);
    }
</script>
<script type="text/javascript" src="http://remoteserver.com/remote.js">
```
再次运行，alert的内容就是:`data from remote!`

而script元素是可以通过document.createElement('script')动态创建的，也就具备了随时可以引入一个外部script，这样就达到了跨域访问的目的，但是JSONP只支持GET请求，其他的方式不支持
**3. WebSocket**
WebSocket是一种通信协议，不实行同源政策，
上面的几种方法，可以说是奇技淫巧，绕过了浏览器的限制。随着前端框架的兴起，以及前后端分离架构的流行，上面的技巧已经比较陈旧了。


