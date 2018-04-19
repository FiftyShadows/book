mac 上两个容器就是互相连不通.别人说是 mac 的系统限制,只能自己搭环境了.

通过在github上查 issue， google 上查资料，有了以下不负责任的猜想：

目前的 docker for mac 使用了自己搞的 github.com/docker/hyperkit 来实现轻量级的虚拟化。从项目的说明可以看到，其基于 github.com/mist64/xhyve，借助了 mac os 自身的 hypervisor.framework 来实现虚拟化。在 developer.apple.com 上可以看到相关的说明和 api refrence。它是 OS X v10.10 新增的一个功能，从 api 上来看，可以实现对 cpu 和 mem 的简单控制 ，并没有提供对网络的控制。网络这块是 xhyve 自己通过 virto-net 实现的。这可能也是导致网络这块体验比较差的原因吧。完善网络还需要花不少时间。

测试还是要继续，从外部连接容器网络有问题，那就把测试程序也放到容器环境中去。