####加锁处理
```go
package main

import (
	"time"
	"fmt"
	"github.com/garyburd/redigo/redis"
	"sync"
)
var (
	RedisPool *redis.Pool
)
func initRedis(){
	RedisPool = &redis.Pool{
		MaxIdle:    4,
		MaxActive:   10,
		IdleTimeout: time.Duration(60) * time.Second,
		Dial: func() (redis.Conn, error) {
			return redis.Dial("tcp", "127.0.0.1:6379")
		},
	}
	conn :=  RedisPool.Get()
	defer conn.Close()

	_, err := conn.Do("ping")
	if err != nil {
		fmt.Println("ping 不通")
		return
	}
}
var m sync.Mutex
func dosm(){
	m.Lock()
	defer m.Unlock()

	conn :=RedisPool.Get()
	defer wg.Done()
	defer conn.Close()
	for i := 0 ;i<100;i++{
		res,err:= redis.Int(conn.Do("GET", "name"))
		fmt.Println(res)
		_, err = conn.Do("SET", "name",res + 1 )
		if err !=nil{
			fmt.Println(err)
		}
	}
	fmt.Println("一个协程结束")
}
var wg sync.WaitGroup
func main(){
	initRedis()

	conn :=RedisPool.Get()

	conn.Do("SET", "name",1 )

	for i := 0 ;i < 3;i++{
		wg.Add(1)
		go dosm()
	}

	wg.Wait()
	conn =RedisPool.Get()

	defer conn.Close()

	fmt.Println(redis.Int(conn.Do("GET", "name")))
}
```

####简单加一的话,有原子操作
```go
package main

import (
	"time"
	"fmt"
	"github.com/garyburd/redigo/redis"
	"sync"
)
var (
	RedisPool *redis.Pool
)
func initRedis(){
	RedisPool = &redis.Pool{
		MaxIdle:    4,
		MaxActive:   10,
		IdleTimeout: time.Duration(60) * time.Second,
		Dial: func() (redis.Conn, error) {
			return redis.Dial("tcp", "127.0.0.1:6379")
		},
	}
	conn :=  RedisPool.Get()
	defer conn.Close()

	_, err := conn.Do("ping")
	if err != nil {
		fmt.Println("ping 不通")
		return
	}
}
//var m sync.Mutex
func dosm(){
	//m.Lock()
	//defer m.Unlock()

	conn :=RedisPool.Get()
	defer wg.Done()
	defer conn.Close()
	for i := 0 ;i<100;i++{
		//res,err:= redis.Int(conn.Do("GET", "name"))
		//fmt.Println(res)
		_, err := conn.Do("INCR", "name" )
		if err !=nil{
			fmt.Println(err)
		}
	}
	fmt.Println("一个协程结束")
}
var wg sync.WaitGroup
func main(){
	initRedis()

	conn :=RedisPool.Get()

	conn.Do("SET", "name",1 )

	for i := 0 ;i < 3;i++{
		wg.Add(1)
		go dosm()
	}

	wg.Wait()
	conn =RedisPool.Get()

	defer conn.Close()

	fmt.Println(redis.Int(conn.Do("GET", "name")))
}
```