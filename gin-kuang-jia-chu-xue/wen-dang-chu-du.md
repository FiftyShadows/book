####1.路由
对于 xxxxx.com/article/1?state=2?name=aaa 这样的 url
一般对应 xxxxx.com/article/:id  
```go
id := com.StrTo(c.Param("id")).MustInt()
name := c.Query("name")
//默认值的提供方法
state := com.StrTo(c.DefaultQuery("state", "0")).MustInt()
```
####Post参数的获取
```go
if arg := c.PostForm("state"); arg != "" {
    state = com.StrTo(arg).MustInt()
	maps["state"] = state
	}
```

####2.分组路由
```go
v1 := router.Group("/v1")

    v1.GET("/login", func(c *gin.Context) {
        c.String(http.StatusOK, "v1 login")
    })

    v2 := router.Group("/v2")

    v2.GET("/login", func(c *gin.Context) {
        c.String(http.StatusOK, "v2 login")
    })
```

####3.重定向
gin对于重定向的请求，相当简单。调用上下文的Redirect方法：
```go
router.GET("/redict/google", func(c *gin.Context) {
        c.Redirect(http.StatusMovedPermanently, "https://google.com")
    })
```

####4.middleware中间件

golang的net/http设计的一大特点就是特别容易构建中间件。gin也提供了类似的中间件。需要注意的是中间件只对注册过的路由函数起作用。对于分组路由，嵌套使用中间件，可以限定中间件的作用范围。中间件分为全局中间件，单个路由中间件和群组中间件。
#####全局中间件
先定义一个中间件函数：
```go
func MiddleWare() gin.HandlerFunc {
    return func(c *gin.Context) {
        fmt.Println("before middleware")
        c.Set("request", "clinet_request")
        c.Next()
        fmt.Println("before middleware")
    }
}
```
该函数很简单，只会给c上下文添加一个属性，并赋值。后面的路由处理器，可以根据被中间件装饰后提取其值。需要注意，虽然名为全局中间件，只要注册中间件的过程之前设置的路由，将不会受注册的中间件所影响。只有注册了中间件一下代码的路由函数规则，才会被中间件装饰。

```go
router.Use(MiddleWare())
    {
        router.GET("/middleware", func(c *gin.Context) {
            request := c.MustGet("request").(string)
            req, _ := c.Get("request")
            c.JSON(http.StatusOK, gin.H{
                "middile_request": request,
                "request": req,
            })
        })
    }
```
使用router装饰中间件，然后在/middlerware即可读取request的值，注意在router.Use(MiddleWare())代码以上的路由函数，将不会有被中间件装饰的效果。

#####单个路由中间件

```go

router.GET("/before", MiddleWare(), func(c *gin.Context) {
        request := c.MustGet("request").(string)
        c.JSON(http.StatusOK, gin.H{
            "middile_request": request,
        })
    })
```

#####群组中间件
群组的中间件也类似，只要在对于的群组路由上注册中间件函数即可：
```go

authorized := router.Group("/", MyMiddelware())
// 或者这样用：
authorized := router.Group("/")
authorized.Use(MyMiddelware())
{
    authorized.POST("/login", loginEndpoint)
}
```
#####鉴权中间件demo
```go
router.GET("/auth/signin", func(c *gin.Context) {
        cookie := &http.Cookie{
            Name:     "session_id",
            Value:    "123",
            Path:     "/",
            HttpOnly: true,
        }
        http.SetCookie(c.Writer, cookie)
        c.String(http.StatusOK, "Login successful")
    })

    router.GET("/home", AuthMiddleWare(), func(c *gin.Context) {
        c.JSON(http.StatusOK, gin.H{"data": "home"})
    })
```
登录函数会设置一个session_id的cookie，注意这里需要指定path为/，不然gin会自动设置cookie的path为/auth，一个特别奇怪的问题。/homne的逻辑很简单，使用中间件AuthMiddleWare注册之后，将会先执行AuthMiddleWare的逻辑，然后才到/home的逻辑。

AuthMiddleWare的代码如下：

```go
func AuthMiddleWare() gin.HandlerFunc {
    return func(c *gin.Context) {
        if cookie, err := c.Request.Cookie("session_id"); err == nil {
            value := cookie.Value
            fmt.Println(value)
            if value == "123" {
                c.Next()
                return
            }
        }
        c.JSON(http.StatusUnauthorized, gin.H{
            "error": "Unauthorized",
        })
        c.Abort()
        return
    }
}
```
从上下文的请求中读取cookie，然后校对cookie，如果有问题，则终止请求，直接返回，这里使用了c.Abort()方法。

####异步协程
golang的高并发一大利器就是协程。gin里可以借助协程实现异步任务。因为涉及异步过程，请求的上下文需要copy到异步的上下文，并且这个上下文是只读的

```go
router.GET("/sync", func(c *gin.Context) {
        time.Sleep(5 * time.Second)
        log.Println("Done! in path" + c.Request.URL.Path)
    })

    router.GET("/async", func(c *gin.Context) {
        cCp := c.Copy()
        go func() {
            time.Sleep(5 * time.Second)
            log.Println("Done! in path" + cCp.Request.URL.Path)
        }()
    })
```