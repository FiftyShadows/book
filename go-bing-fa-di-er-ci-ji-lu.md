再添加一些实例

####简单调度
```go
package main

import (
	"fmt"
	"time"
)

func newTask() {
	i := 0
	for {
		i++
		fmt.Printf("new goroutine: i = %d\n", i)
		time.Sleep(1 * time.Second) //延时1s
	}
}

func main() {
	//创建一个 goroutine，启动另外一个任务
	go newTask()

	i := 0
	//main goroutine 循环打印
	for {
		i++
		fmt.Printf("main goroutine: i = %d\n", i)
		time.Sleep(1 * time.Second) //延时1s
	}
}
//new goroutine: i = 1
//main goroutine: i = 1
//main goroutine: i = 2
//new goroutine: i = 2
//main goroutine: i = 3
//new goroutine: i = 3
//new goroutine: i = 4

```
####主goroutine退出后，其它的工作goroutine也会自动退出：

```go
package main

import (
	"fmt"
	"time"
)

func newTask() {
	i := 0
	for {
		i++
		fmt.Printf("new goroutine: i = %d\n", i)
		time.Sleep(1 * time.Second) //延时1s
	}
}

func main() {
	//创建一个 goroutine，启动另外一个任务
	go newTask()

	fmt.Println("main goroutine exit")
}
//main goroutine exit

//Process finished with exit code 0
```