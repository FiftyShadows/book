####缓存穿透

也叫缓存击穿，查询一个数据库中不存在的数据，比如商品详情，查询一个不存在的ID，每次都会访问DB，导致数据库高负荷

#####1.缓存空对象
当通过某一个key去查询数据的时候，如果对应在数据库中的数据都不存在，我们将此key对应的value设置为一个默认的值，比如“NULL”，并设置一个缓存的失效时间，这时在缓存失效之前，所有通过此key的访问都被缓存挡住了。后面如果此key对应的数据在DB中存在时，缓存失效之后，通过此key再去访问数据，就能拿到新的value了。这种方式实现起来成本较低，比较适合命中不高，但可能被频繁更新的数据
#####2.BloomFilter
http://www.cnblogs.com/duanxz/p/3480254.html
例如： 一个个性化推荐系统有 4 亿个用户 ID，每个小时算法工程师会根据每个用户之前历史行为做出来的个性化放到存储层中，但是最新的用户由于没有历史行为，就会发生缓存穿透的行为，为此可以将所有有个性化推荐数据的用户做成布隆过滤器。如果布隆过滤器认为该用户 ID 不存在，那么就不会访问存储层，在一定程度保护了存储层。

#####总结:
对于一些恶意攻击，查询的key往往各不相同，而且数据贼多。此时，第一种方案就显得提襟见肘了。因为它需要存储所有空数据的key，而这些恶意攻击的key往往各不相同，而且同一个key往往只请求一次。因此即使缓存了这些空数据的key，由于不再使用第二次，因此也起不了保护数据库的作用。
因此，对于空数据的key各不相同、key重复请求概率低的场景而言，应该选择第二种方案。而对于空数据的key数量有限、key重复请求概率较高的场景而言，应该选择第一种方案。

####热点 key
缓存中的某些Key(可能对应用与某个促销商品)对应的value存储在集群中一台机器，使得所有流量涌向同一机器，成为系统的瓶颈，该问题的挑战在于它无法通过增加机器容量来解
#####1.客户端热点key缓存
将热点key对应value并缓存在客户端本地，并且设置一个失效时间。对于每次读请求，将首先检查key是否存在于本地缓存中，如果存在则直接返回，如果不存在再去访问分布式缓存的机器。
#####2.服务端负载均衡
将热点key复制多个副本，然后存储到缓存集群的不同机器上。当通过热点key去查询数据时，通过某种hash算法随机选择一个副本机器访问缓存，将热点分散到了不同机器上。
#####3.设置不同的失效时间
#####4.数据库 互斥锁