
wechatpy - 微信方面开发
对于基于公众号开发，这个库是我觉得目前功能最全、也是一直在持续更新的库。令我眼前一亮的是：竟然公众号开放平台代服务的一些功能也融入了进来。做微信方面开发，不要错过

xmltodict - xml 转字典功能
由于在微信的一些回调接口中，post 的数据是 xml 类型。利用该库，可以很方便转成 dict 类型。

django-rq - 简单的消息队列
非常轻量的消息队列服务，对于不需要复杂功能的需求看 3 分钟文档直接上手

restless - RESTFUL 框架
这个框架不只是针对 django 的，还可以对接 flask 等一些其他框架，轻量简洁。也比较灵活

django-constance - 常量管理
有时我们会在 django 的 settings 中设置一些常量，但是有可能会进行变更。利用这个包，只需简单的配置就可以自动生成 admin 管理后台 可以修改

django-braces - 提供了 django 便捷的 Mixin
登录判断、权限判断、组别判断等方便的 Mixin，还有一些 Form 方面的 Mixin。比如对于 csrfexempt 我们一般要重写 dispatch 方法。这个包里面提供了相关类，直接继承即可。

model-mommy - django 单元测试
在进行单元测试时，制作测试数据是个比较麻烦的事情。利用这个包可以很方便灵活的提供 model 测试数据。

django-crequest - 全局 request
在 django 开发时，view 中都可以方便的获取 request，但是在一些公共库中就获取不到了。该包提供了全局获取 request 的方法。

django-qurl-templatetag - url 参数修改
在进行 url 操作处理时，经常遇到的一个问题就是。我们要对一个字符串 url 中的参数就行修改、添加移除等。这个包简直是处理神器，而且在 html 中也提供了对应的处理方法。

django-settings-export - 模板中获取 settings 配置
有时，我们要在 html 模板中获取 settings 的某个配置，通过该包简单配置即可。