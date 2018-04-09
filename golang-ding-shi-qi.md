####Timer是一个定时器，代表未来的一个单一事件，你可以告诉timer你要等待多长时间，它提供一个channel，在将来的那个时间那个channel提供了一个时间值。
```go

package main

import "fmt"
import "time"

func main() {
	//创建定时器，2秒后，定时器就会向自己的C字节发送一个time.Time类型的元素值
	timer1 := time.NewTimer(time.Second * 2)
	t1 := time.Now() //当前时间
	fmt.Printf("t1: %v\n", t1)

	t2 := <-timer1.C
	fmt.Printf("t2: %v\n", t2)

	//如果只是想单纯的等待的话，可以使用 time.Sleep 来实现
	timer2 := time.NewTimer(time.Second * 2)
	<-timer2.C
	fmt.Println("2s后")

	time.Sleep(time.Second * 2)
	fmt.Println("再一次2s后")

	<-time.After(time.Second * 2)
	fmt.Println("再再一次2s后")

	timer3 := time.NewTimer(time.Second)
	go func() {
		<-timer3.C
		fmt.Println("Timer 3 expired")
	}()

	stop := timer3.Stop() //停止定时器
	if stop {
		fmt.Println("Timer 3 stopped")
	}

	fmt.Println("before")
	timer4 := time.NewTimer(time.Second * 5) //原来设置3s
	timer4.Reset(time.Second * 1)            //重新设置时间
	<-timer4.C
	fmt.Println("after")
}
```

####Ticker是一个定时触发的计时器，它会以一个间隔(interval)往channel发送一个事件(当前时间)，而channel的接收者可以以固定的时间间隔从channel中读取事件。
```go
package main

import (
	"time"
	"fmt"
)
func main() {
	//创建定时器，每隔1秒后，定时器就会给channel发送一个事件(当前时间)
	ticker := time.NewTicker(time.Second * 1)

	i := 0
	go func() {
		for { //循环
			<-ticker.C
			i++
			fmt.Println("i = ", i)

			if i == 5 {
				ticker.Stop() //停止定时器
			}
		}
	}() //别忘了()

	//死循环，特地不让main goroutine结束
	for {
	}
}
```