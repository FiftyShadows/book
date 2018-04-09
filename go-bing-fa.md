####关键字 go 即可启动并发(协程),用 python 的我简直惊呆了
####goroutine运行在相同的地址空间，因此访问共享内存必须做好同步。goroutine 奉行通过通信来共享内存，而不是共享内存来通信。
####先来一个sync 包
#####sync 包提供了互斥锁这类的基本的同步原语.除 Once 和 WaitGroup 之外的类型大多用于底层库的例程。更高级的同步操作通过信道与通信进行。
WaitGroup总共有三个方法：Add(delta int),　Done(),　Wait()。

**1. Add:添加或者减少等待goroutine的数量**

**2. Done:相当于Add(-1)**

**3. Wait:执行阻塞，直到所有的WaitGroup数量变成0**
使用场景：
　　程序中需要并发，需要创建多个goroutine，并且一定要等这些并发全部完成后才继续接下来的程序执行．WaitGroup的特点是Wait()可以用来阻塞直到队列中的所有任务都完成时才解除阻塞，而不需要sleep一个固定的时间来等待．但是其缺点是无法指定固定的goroutine数目．
```go
package main

import (
	"fmt"
	"sync"
)

var waitgroup sync.WaitGroup

func Afunction(shownum int) {
	fmt.Println(shownum)
	waitgroup.Done() //任务完成，将任务队列中的任务数量-1，其实.Done就是.Add(-1)
}

func main() {
	for i := 0; i < 10; i++ {
		waitgroup.Add(1) //每创建一个goroutine，就把任务队列中任务的数量+1
		go Afunction(i)
	}
	waitgroup.Wait() //.Wait()这里会发生阻塞，直到队列中所有的任务结束就会解除阻塞
}
```
<br>
####熟练 channel 你可以理解为一个管道,应该是先进先出的数据结构,可以如下操作
(1) 创建chennel (通过make()函数)

(2) 放入数据 (通过 channel <- data 操作) 

(3) 取出数据 (通过 <-channel 操作)

(4)  关闭channel (通过close()函数)
***
#####我通俗一点讲我的理解
**1.channel是一种阻塞管道，是自动阻塞的。意思就是，如果管道满了，一个对channel放入数据的操作就会阻塞，直到有某个routine从channel中取出数据，这个放入数据的操作才会执行。相反同理，如果管道是空的，一个从channel取出数据的操作就会阻塞，直到某个routine向这个channel中放入数据，这个取出数据的操作才会执行。这事channel最重要的一个性质，没有之一。**
```go
func main() {  
    ch := make(chan int, 3)  
    ch <- 1  
    ch <- 1  
    ch <- 1  
    ch <- 1 //这一行操作就会发生阻塞，因为前三行的放入数据的操作已经把channel填满了  
}  
```
```go
func main() {  
    ch := make(chan int, 3)  
    <-ch //这一行会发生阻塞，因为channel才刚创建，是空的，没有东西可以取出  
} 
```
<br>
**2.channel分为有缓冲的channel和无缓冲的channel。两种channel的创建方法如下：**
```go
ch := make(chan int) //无缓冲的channel，同等于make(chan int, 0)  
ch := make(chan int, 5) //一个缓冲区大小为5的channel  
```
操作一个channel时一定要注意其是否带有缓冲，因为有些操作会触发channel的阻塞导致死锁。下面就来解释这些需要注意的情景。
首先来看一个一个例子，这个例子是两段只有主函数不同的代码：

```go
package main  
  
import "fmt"  
  
func Afuntion(ch chan int) {  
    fmt.Println("finish")  
    <-ch  
}  
  
func main() {  
    ch := make(chan int) //无缓冲的channel  
    go Afuntion(ch)  
    ch <- 1  
      
    // 输出结果：  
    // finish  
}  

```

1. 创建了一个无缓冲channel

2. 启动了一个goroutine，这个routine中对channel执行取出操作，但是因为这时候channel为空，所以这个取出操作发生阻塞，但是主routine可没有发生阻塞，它还在继续运行呢

3.  主goroutine这时候继续执行下一行，往channel中放入了一个数据
4. 这时阻塞的那个routine检测到了channel中存在数据了，所以接触阻塞，从channel中取出数据，程序就此完毕

```go
package main  
  
import "fmt"  
  
func Afuntion(ch chan int) {  
    fmt.Println("finish")  
    <-ch  
}  
  
func main() {  
    ch := make(chan int) //无缓冲的channel  
    //只是把这两行的代码顺序对调一下  
    ch <- 1  
    go Afuntion(ch)  
  
    // 输出结果：  
    // 死锁，无结果  
}  
```
1. 创建了一个无缓冲的channel

2. 主routine要向channel中放入一个数据，但是因为channel没有缓冲，相当于channel一直都是满的，所以这里会发生阻塞。可是下面的那个goroutine还没有创建呢，主routine在这里一阻塞，整个程序就只能这么一直阻塞下去了，然后。。。然后就没有然后了。。死锁！

**※从这里可以看出，对于无缓冲的channel，放入操作和取出操作不能再同一个routine中，而且应该是先确保有某个routine对它执行取出操作，然后才能在另一个routine中执行放入操作。**

<br>
#####对于带缓冲的channel，就没那么多讲究了，因为有缓冲空间，所以只要缓冲区不满，放入操作就不会阻塞，同样，只要缓冲区不空，取出操作就不会阻塞。而且，带有缓冲的channel的放入和取出可以用在同一个routine中。
#####总结：
#####对于无缓存的channel,放入channel和从channel中向外面取数据这两个操作不能放在同一个协程中，防止死锁的发生；同时应该先利用go 开一个协程对channel进行操作，此时阻塞该go 协程，然后再在主协程中进行channel的相反操作（与go 协程对channel进行相反的操作），实现go 协程解锁．即必须go协程在前，解锁协程在后．

#####带缓存channel:
#####对于带缓存channel，只要channel中缓存不满，则可以一直向 channel中存入数据，直到缓存已满；同理只要channel中缓存不为０，便可以一直从channel中向外取数据，直到channel缓存变为０才会阻塞．

#####由此可见，相对于不带缓存channel，带缓存channel不易造成死锁，可以同时在一个goroutine中放心使用，

#####但是，并不是说有了缓冲就可以随意使用channel的放入和取出了，我们一定要注意放入和取出的速率问题。下面我们就举个例子来说明这种问题：

#####我们经常会用利用channel自动阻塞的性质来控制当前运行的goroutine的总数量，如下：
```go
package main  
  
import (  
    "fmt"  
)  
  
func Afunction(ch chan int) {  
    fmt.Println("finish")  
    <-ch //goroutine执行完了就从channel取出一个数据  
}  
  
func main() {  
    ch := make(chan int, 10)  
    for i := 0; i < 1000; i++ {  
        //每当创建goroutine的时候就向channel中放入一个数据，如果里面已经有10个数据了，就会  
        //阻塞，由此我们将同时运行的goroutine的总数控制在<=10个的范围内  
        ch <- 1  
        go Afunction(ch)  
    }  
    // 这里只是示范个例子，当然，接下来应该有些更加周密的同步操作  
}  
```
#####给一个我用错的场景
```go
package main  
  
func Afunction(ch chan int) {  
    ch <- 1  
    ch <- 1  
    ch <- 1  
    ch <- 1  
    ch <- 1  
  
    <-ch  
}  
  
func main() {  
    //主routine的操作同上面那段代码  
    ch := make(chan int, 10)  
    for i := 0; i < 100; i++ {  
        ch <- 1  
        go Afunction(ch)  
    }  
  
    // 这段代码运行的结果为死锁  
}  
```
上面这段运行和之前那一段基本上原理是一样的，但是运行后却会发生死锁。为什么呢？其实总结起来就一句话，"放得太快，取得太慢了"。


按理说，我们应该在我们主routine中创建子goroutine并每次向channel中放入数据，而子goroutine负责从channel中取出数据。但是我们的这段代码在创建了子goroutine后，每个routine会向channel中放入5个数据。这样，每向channel中放入6个数据才会执行一次取出操作，这样一来就可能会有某一时刻，channel已经满了，但是所有的routine都在执行放入操作(因为它们当前执行放入操作的概率是执行取出操作的6倍)，这样一来，所有的routine都阻塞了，从而导致死锁。

在使用带缓冲的channel时一定要注意放入与取出的速率问题。
<br>
####关闭后的channel可以取数据，但是不能放数据。而且，channel在执行了close()后并没有真的关闭，channel中的数据全部取走之后才会真正关闭。
```go
package main  
  
func main() {  
    ch := make(chan int, 5)  
    ch <- 1  
    ch <- 1  
    close(ch)  
    ch <- 1 //不能对关闭的channel执行放入操作  
          
        // 会触发panic  
}  
```
正确示例
```go
package main

func main() {
	ch := make(chan int, 5)
	ch <- 1
	ch <- 1
	close(ch)
	<-ch //只要channel还有数据，就可能执行取出操作

	//正常结束
}
```
放一个较为完整的示例
```go
package main

import "fmt"

func main() {
	ch := make(chan int, 5)
	ch <- 1
	ch <- 1
	ch <- 1
	ch <- 1
	close(ch)  //如果执行了close()就立即关闭channel的话，下面的循环就不会有任何输出了
	for {
		data, ok := <-ch
		if !ok {
			break
		}
		fmt.Println(data)
	}

	// 输出：
	// 1
	// 1
	// 1
	// 1
	//
	// 调用了close()后，只有channel为空时，channel才会真的关闭
}  
```
<br>
####一个重点,敲黑板
#####使用channel控制goroutine数量
##### 1. 如果任务数量是固定的
```go
package main

import "fmt"

func Afunction(ch chan int) {
	ch <- 1
	fmt.Println("a")
}

func main() {
	var (
		ch chan int = make(chan int, 20) //可以同时运行的routine数量为20
		dutycount int      = 500
	)
	for i := 0; i < dutycount; i++ {
		go Afunction(ch)
	}

	//知道了任务总量，可以像这样利用固定循环次数的循环检测所有的routine是否工作完毕
	for i := 0; i < dutycount; i++ {
		<-ch
	}
}
```
##### 2. 如果任务的数量不固定
```go
package main


func Afunction(routineControl chan int, feedback chan string) {
	defer func() {
		<-routineControl
		feedback <- "finish"
	}()

	// do some process
	// ...
}

func main() {
	var (
		routineCtl chan int    = make(chan int, 20)
		feedback   chan string = make(chan string, 10000)

		msg      string
		allwork  int
		finished int
	)
	for i := 0; i < 1000; i++ {
		routineCtl <- 1
		allwork++
		go Afunction(routineCtl, feedback)
	}

	for {
		msg = <-feedback
		if msg == "finish" {
			finished++
		}
		if finished == allwork {
			break
		}
	}
}
```
####channel阻塞超时处理：
#####goroutine有时候会进入阻塞情况，那么如何避免由于channel阻塞导致整个程序阻塞的发生那？解决方案：通过select设置超时处理，select的用法与switch语言非常类似，由select开始一个新的选择块，每个选择条件由case语句来描述。
#####与switch语句可以选择任何可使用相等比较的条件相比， select有比较多的限制，其中最大的一条限制就是每个case语句里必须是一个IO操作，具体程序如下：
```go

select {    case <-chan1:        // 如果chan1成功读到数据，则进行该case处理语句    case chan2 <- 1:        // 如果成功向chan2写入数据，则进行该case处理语句    default:        // 如果上面都没有成功，则进入default处理流程    }
```
在一个select语句中，Go语言会按顺序从头至尾评估每一个发送和接收的语句。如果其中的任意一语句可以继续执行(即没有被阻塞)，那么就从那些可以执行的语句中任意选择一条来使用。
如果没有任意一条语句可以执行(即所有的通道都被阻塞)，那么有两种可能的情况：如果给出了default语句，那么就会执行default语句，同时程序的执行会从select语句后的语句中恢复。如果没有default语句，那么select语句将被阻塞，直到至少有一个通信可以进行下去。
```go
package main

 import (
    "fmt"
    "time"
)

func main() {
    c := make(chan int)
    o := make(chan bool)
    go func() {
        for {
            select {
            case i := <-c:
                fmt.Println(i)
            case <-time.After(time.Duration(3) * time.Second):    //设置超时时间为３ｓ，如果channel　3s钟没有响应，一直阻塞，则报告超时，进行超时处理．
                fmt.Println("timeout")
                o <- true
                break
            }
        }
    }()
    <-o
}
```
####单方向的channel

```go
var ch1 chan int       // ch1是一个正常的channel，不是单向的
var ch2 chan<- float64 // ch2是单向channel，只用于写float64数据
var ch3 <-chan int     // ch3是单向channel，只用于读取int数据
```