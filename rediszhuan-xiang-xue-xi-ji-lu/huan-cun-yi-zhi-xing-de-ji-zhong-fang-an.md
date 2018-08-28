https://www.jianshu.com/p/91e91d28d931

我司目前用的绕写,配合删除缓存的做法,因为并发量小的原因,暂时满足了需求,但是查看
https://www.quora.com/Why-does-Facebook-use-delete-to-remove-the-key-value-pair-in-Memcached-instead-of-updating-the-Memcached-during-write-request-to-the-backend
又会有大并发情况下两个并发写导致的脏数据,(这个条件需要发生在读缓存时缓存失效，而且并发着有一个写操作。而实际上数据库的写操作会比读操作慢得多，而且还要锁表，而读操作必需在写操作前进入数据库操作，而又要晚于写操作更新缓存，所有的这些条件都具备的概率基本并不大。)
但是如果碰到了这样的问题:https://coolshell.cn/articles/17416.html
