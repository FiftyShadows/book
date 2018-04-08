####panic

但是，当遇到不可恢复的错误状态的时候，如数组访问越界、空指针引用等，这些运行时错误会引起painc异常。这时，上述错误处理方式显然就不适合了。反过来讲，在一般情况下，我们不应通过调用panic函数来报告普通的错误，而应该只把它作为报告致命错误的一种方式。当某些不应该发生的场景发生时，我们就应该调用panic。一般而言，当panic异常发生时，程序会中断运行，并立即执行在该goroutine（可以先理解成线程，在中被延迟的函数（defer 机制）。随后，程序崩溃并输出日志信息。日志信息包括panic value和函数调用的堆栈跟踪信息。

```go
func TestA() {    fmt.Println("func TestA()")}func TestB() {    panic("func TestB(): panic")}func TestC() {    fmt.Println("func TestC()")}func main() {    TestA()    TestB()//TestB()发生异常，中断程序    TestC()}
```

####recover
#####recover只有在defer调用的函数中有效。
```go

package main

import "fmt"

func TestA() {
	fmt.Println("func TestA()")
}

func TestB() (err error) {
	defer func() { //在发生异常时，设置恢复
		if x := recover(); x != nil {
			//panic value被附加到错误信息中；
			//并用err变量接收错误信息，返回给调用者。
			err = fmt.Errorf("internal error: %v", x)
		}
	}()

	panic("func TestB(): panic")
}

func TestC() {
	fmt.Println("func TestC()")
}

func main() {
	TestA()
	err := TestB()
	fmt.Println(err)
	TestC()
}
```