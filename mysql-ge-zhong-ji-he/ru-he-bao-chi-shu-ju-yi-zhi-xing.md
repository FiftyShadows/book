 1、使用自身支持分布式的数据库 2、使用数据库中间(mycat,,atlas,drds)
 
 sqlalchemy使用session的时候，大多数情况都是不需要自己加锁的。如果需要当前读则可以调用函数with_for_update。