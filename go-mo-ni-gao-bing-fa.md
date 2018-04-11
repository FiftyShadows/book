看了这篇相当有意思的文章,可惜实际工作用不到 go, 实在手痒

实例代码,根据业务修改
```go
package main

import (
	"time"
	"sync"
)

func doTask() {
	//耗时炒作(模拟)
	time.Sleep(200 * time.Millisecond)
	wg.Done()
}

//这里模拟的http接口,每次请求抽象为一个job
func handle() {
	//wg.Add(1)
	job := Job{}
	JobQueue <- job
}

var (
	MaxWorker = 1000
	MaxQueue = 200000
	wg  sync.WaitGroup
)

type Worker struct {
	quit chan bool
}

func NewWorker() Worker {
	return Worker{
		quit: make(chan bool)}
}

// Start method starts the run loop for the worker, listening for a quit channel in
// case we need to stop it
func (w Worker) Start() {
	go func() {
		for {
			select {
			case <-JobQueue:
				// we have received a work request.
				doTask()
			case <-w.quit:
				// we have received a signal to stop
				return
			}
		}
	}()
}

// Stop signals the worker to stop listening for work requests.
func (w Worker) Stop() {
	go func() {
		w.quit <- true
	}()
}

type Job struct {
}

var JobQueue chan Job = make(chan Job, MaxQueue)

type Dispatcher struct {
}

func NewDispatcher() *Dispatcher {
	return &Dispatcher{}
}

func (d *Dispatcher) Run() {
	// starting n number of workers
	for i := 0; i < MaxWorker; i++ {
		worker := NewWorker()
		worker.Start()
	}
}
```
####测试
```go
unc Benchmark_handle(b *testing.B) {
 runtime.GOMAXPROCS(runtime.NumCPU())
 d := NewDispatcher()
 d.Run()
 for i:=0;i<10000;i++ {
  wg.Add(1)
  handle()
 }
 wg.Wait()
}
```

####测试结果
```go
pkg: golang-study-demo/goroutine
1 2029931652 ns/op
PASS
```
1w个任务放到队列中,1000个worker去执行任务,每个任务的耗时200ms,任务执行完需要2s多